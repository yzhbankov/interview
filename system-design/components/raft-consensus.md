# Component Card — Raft / Consensus

**One line:** a replicated log that a majority of nodes agree on, giving you linearizable
state and automatic failover — at the cost of a quorum round trip on every write.

---

## Data model

A **replicated log** of opaque commands, applied in order to a deterministic state
machine. Each entry is `(term, index, command)`. The log is the source of truth; the
state machine is derived from it.

Three mechanisms, and that's the whole protocol:

- **Terms** — a logical clock that only increases. Each term has **at most one leader**.
- **Leader election** — a follower that misses heartbeats for its (randomized) election
  timeout becomes a candidate and requests votes. Majority wins. The randomization is
  what breaks split votes.
- **Log replication** — only the leader accepts writes. `AppendEntries` doubles as the
  heartbeat. An entry is **committed** once it is durably replicated to a majority, and
  only then applied to the state machine.

**The election restriction** is what makes it safe: a voter refuses any candidate whose
log is less up-to-date than its own (compare last log term, then index). That single
rule guarantees a new leader already holds every committed entry, so nothing committed
is ever lost.

> **Interview gotcha (Figure 8 in the paper):** a leader may *not* commit an entry from
> a **previous** term just by counting replicas — that's unsafe. Prior-term entries
> commit indirectly, once an entry from the *current* term commits. This is why real
> implementations append a **no-op entry** the moment a leader is elected: it flushes the
> backlog safely.

## Consistency

**Linearizable writes**, always through the leader.

**Reads are the part people get wrong.** Reading from the leader is *not* automatically
linearizable — a leader that has been partitioned away doesn't know it lost the election
yet, and will happily serve stale data. Three correct options:

| Read mode | Cost | Guarantee |
|-----------|------|-----------|
| **Log read** — put the read in the log | Full write cost, including fsync | Linearizable, simplest to reason about |
| **ReadIndex** — record commit index, confirm leadership with one heartbeat round to a quorum, wait for apply | One network round trip, no disk write | Linearizable. The usual choice. |
| **Lease read** — leader holds a time-based lease and answers locally | Free | Linearizable *only* under a bounded clock-drift assumption |

Follower reads are possible by having the follower fetch a ReadIndex from the leader
first — you keep linearizability and offload the leader's CPU, but not the round trip.

## Partitioning

**A single Raft group has a hard write ceiling** — one leader, one fsync-quorum, no way
around it. You scale by running **many Raft groups**, one per shard or key range
("multi-Raft"): CockroachDB per range, TiKV per region. etcd deliberately does *not*
shard — it's one group, and that's the design.

The moment you have multiple groups, any operation spanning them needs **two-phase
commit layered on top**. That's where distributed-SQL write latency actually comes from.

## Availability math

Tolerates **f failures with 2f+1 nodes**.

| Voters | Tolerates | Quorum |
|--------|-----------|--------|
| 3 | 1 | 2 |
| **4** | **1** | 3 |
| 5 | 2 | 3 |
| 7 | 3 | 4 |

**Even cluster sizes buy nothing** — 4 nodes tolerate the same single failure as 3, with
more machines available to fail and a larger quorum to wait for. Always odd.

**Membership changes** use joint consensus (the paper) or single-server-at-a-time (most
implementations). Add a new node as a **non-voting learner** first and let it catch up:
promoting a cold node straight to voter enlarges the quorum before it can answer, which
can cost you availability during the catch-up.

## Failure modes

**Election storms.** Aggressive election timeouts plus a GC pause, a slow disk, or a
flaky network produces continuous re-elections and zero progress. The tell is the **term
number climbing fast** — every re-election burns a term.

**Quorum loss.** Lose the majority and writes stop, full stop. There is no safe
automatic recovery; you force a new cluster configuration by hand, and that operation
**can lose committed data**. This is the failure to design your blast radius around.

**Slow-disk tail latency.** Commit latency is set by the **majority-th** fastest fsync,
so in a 3-node cluster one slow disk stalls everything. A 5-node cluster can absorb the
two slowest — which is a real argument for 5 over 3 beyond fault tolerance.

**Unbounded log growth.** Without periodic snapshots and log compaction, restart replay
time grows forever and a new follower can never catch up.

**Stale reads.** Split brain is genuinely impossible — but if you skipped ReadIndex, a
deposed leader serves stale reads that *look* consistent.

**Disk full on the leader.** Raft must durably append before acknowledging. A full disk
is an immediate hard stop, not a degradation.

## Scale ceiling

- **3, 5, or 7 voters.** Past 7, quorum latency is dominated by the slowest member and
  election traffic gets expensive. Add **learners** (non-voting replicas) instead — they
  serve reads and act as failover candidates without joining the quorum.
- **Per-group write throughput** is bounded by leader fsync plus one RTT to a majority.
  With batching and group commit, order of magnitude 10k–50k small writes/sec; far less
  without batching. Design for the group count, not the group.
- **etcd specifically** targets a small dataset (single-digit GB) — it's a coordination
  store, not a database. Treating it as one is a classic outage.

## Cost driver

**fsync latency** and **network RTT to a majority** — every commit pays both.

The consequence that matters in a design interview: a Raft group spanning three regions
pays **inter-region RTT on every single write**. That's why globally distributed
databases pin each range's leader near its data rather than spreading every group evenly.
Fast NVMe or a battery-backed write cache is the single highest-leverage hardware choice.

## Ops

**Monitor**

| Signal | Why |
|--------|-----|
| **Leader changes / term increments per minute** | The leading indicator of everything else |
| Commit latency p99 | Quorum health, not just node health |
| Follower lag (`matchIndex` vs leader `commitIndex`) | Which node is about to cost you the quorum |
| fsync duration (etcd: `wal_fsync_duration_seconds`) | Disk is usually the culprit |
| Snapshot duration and frequency | Compaction keeping up with the log |
| Failed proposals | Backpressure or lost quorum |

**Page on**

- Leader change rate above baseline (an election storm is an outage in progress)
- **Any** node down in a 3-node cluster — you are at zero redundancy, not "degraded"
- Commit latency p99 breach
- Disk > 80% on any member

## Choose it when

- You need **linearizable replicated state** with automatic failover, and can pay quorum
  latency on writes.
- The data is **small and control-plane shaped**: leader election, cluster configuration,
  service discovery, locks and leases, shard assignment, metadata.
- You're building a distributed database and need per-shard replication that fails over
  without a human.

## Don't when

- You're moving **bulk data**. Every byte pays consensus. Use Raft for the metadata
  *about* the data, not the data — this is the single most common misuse.
- The workload tolerates eventual consistency. You'd be buying latency for nothing.
- You need **low-latency writes in multiple regions**. One group, one leader, one
  region's RTT for everybody else.
- You'd be implementing it yourself. Consensus bugs surface as rare, unreproducible
  data loss. Use an implementation someone else has been running for a decade.

## Alternative

| Switch to | When |
|-----------|------|
| **Multi-Paxos** | Same guarantees. More implementation freedom, considerably harder to reason about — Raft exists specifically as the understandable alternative. |
| **ZAB / ZooKeeper** | You want the mature ecosystem (Curator recipes for locks, barriers, leader election) more than you want to run your own. |
| **etcd or Consul as a service** | Almost always the right answer: don't implement consensus, deploy one. |
| **EPaxos / leaderless** | Multi-region writes without a single-leader bottleneck. Substantially more complex and rare in production. |
| **Spanner / TrueTime** | External consistency across regions, where bounded clock uncertainty is worth the hardware. |
