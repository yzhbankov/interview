# Component Card — Read Replicas

**One line:** full copies of the primary that serve reads at the price of staleness.
They scale read throughput — not writes, and not data size.

---

## Data model

A **complete copy** of the primary's dataset, kept current by shipping the primary's
change stream and replaying it. Read-only.

- **Physical / streaming** — byte-level replay of the primary's WAL (Postgres) or
  ROW-format binlog (MySQL). Same version, whole cluster, identical on-disk layout.
- **Logical** — row-level changes re-applied per table. Selective, works across major
  versions, and the replica can carry **different indexes** — which is what makes a
  dedicated reporting replica worth building.

**The thing to say out loud:** every replica holds the *entire* dataset. Read replicas
do not shard, do not partition, and do not reduce storage. If the problem is data size
or write volume, replicas are the wrong tool and proposing them is a common interview
misstep.

## Consistency

**Eventual, with lag as the only real variable.** This card is mostly about that lag.

**Replication lag** is the delay between commit on the primary and visibility on the
replica. Normally single-digit milliseconds. Under a write burst, unbounded.

**Read-your-writes breaks.** A user writes, the next read lands on a replica, and their
own change isn't there. This is the single most common production bug that read replicas
introduce, and it appears the moment you add the first one.

| Fix | How it works | Cost |
|-----|--------------|------|
| **Sticky-to-primary** | After a write, route that session to the primary for N seconds | Simplest, most common. Wastes primary capacity. |
| **LSN / GTID wait** | Client records the write position; the replica read blocks until it has applied ≥ that position | Correct and precise. Needs client cooperation. Postgres LSN comparison, MySQL `WAIT_FOR_EXECUTED_GTID_SET`, Aurora session tokens. |
| **Monotonic reads** | Pin a session to one replica so the user never moves backwards in time | Cheap. Fixes "my data vanished", not "my write is missing". |
| **Route by tolerance** | Only lag-tolerant queries go to replicas — dashboards, analytics, exports, search indexing | The design that ages best. |

## Partitioning

**None.** Worth stating explicitly, because it's the boundary of what this component can
do: read replicas scale read *throughput* only. Data size, write throughput, and index
build cost all stay exactly where they were.

## Replication mechanics

- **Async** is the default — RPO > 0, you lose the last commits on failover.
- **Semi-sync / sync** gives RPO = 0, but couples the primary's commit latency to the
  slowest sync replica. If that replica hangs and you configured only one,
  **the primary stalls** — a replica taking down the primary.
- **Cascading replicas** (replica-of-a-replica) keep fan-out load off the primary once
  you have many.
- **Delayed replica** (deliberately hours behind) is the cheap defense against a bad
  migration or an accidental `DROP`.

## Failure modes

**Lag spike from single-threaded apply.** The primary commits with many concurrent
backends; the replica replays with one. Postgres has no parallel WAL apply at all; MySQL
needs parallel replication workers configured. A write-heavy burst means lag grows and
reads silently get stale — invisible unless you're measuring it.

**Replay-vs-query conflict** (Postgres hot standby). A long-running replica query can
block WAL replay of a change that would remove rows it's reading. The replica must
either cancel the query or pause replay; `max_standby_streaming_delay` picks which.
Setting `hot_standby_feedback = on` avoids the cancellations by pushing the problem back
to the primary as **bloat** — you've moved the cost, not removed it.

**A dead replica takes down the primary.** A Postgres replication slot for a replica
that never comes back retains WAL on the primary *forever*, until the primary's disk
fills and the primary goes down. The most counterintuitive failure here, and the reason
slot retention belongs on the dashboard.

**Cold promotion.** Promote a replica on failover and all traffic hits an empty buffer
cache — plus you've just lost a read replica at exactly the moment load spiked.

**A replica is not a backup.** `DROP TABLE` replicates in milliseconds. Delayed replicas
or real point-in-time backups cover this; a normal replica does not.

**Silent divergence** under statement-based replication with non-deterministic functions.
Use ROW-based and this goes away.

## Scale ceiling

- **Read throughput** scales close to linearly, but the primary's replication fan-out
  cost grows with replica count. Past roughly 5–10 direct replicas, cascade.
- **Zero help for writes.** Every replica applies 100% of the write volume, so a replica
  needs roughly the primary's write capacity just to keep up. You cannot escape a write
  ceiling this way — that's sharding.
- **Zero help for size.** Each replica is a full copy; storage cost multiplies by N.
- On write-heavy workloads, **lag becomes the ceiling before CPU does**. The replica is
  saturated keeping up, not serving reads.

## Cost driver

**N × the primary's storage and compute**, plus cross-AZ and cross-region replication
data transfer.

The hidden one: a write-heavy primary means expensive replicas that spend most of their
capacity on replay rather than on the reads you bought them for. Storage-level
replication (Aurora, Neon) sidesteps this — replicas share one storage volume, so lag is
sub-10ms and an extra replica doesn't duplicate storage.

## Ops

**Monitor**

| Signal | Why |
|--------|-----|
| **Lag in seconds *and* in bytes** | Both. Time-lag looks fine on an idle primary even when the replica is stalled. |
| **Receive lag vs apply lag, separately** | Distinguishes a network problem from a replay problem — different fixes. |
| Replication slot retention on the primary | The failure mode that kills the primary |
| Replica connection state | A silently disconnected replica still answers reads |
| Query cancellations from replay conflict | Your users are seeing errors you attributed to the app |
| Replica buffer cache hit ratio | A replica thrashing on disk isn't offloading anything |

**Page on**

- Lag > SLO sustained (pick a number — 10s is a common default — and enforce it)
- **Apply lag growing monotonically** — that means it will never catch up on its own
- Inactive replication slot retaining WAL on the primary
- Replica disconnected while still receiving traffic

## Choose it when

- The workload is **read-heavy** (10:1 or better) and those reads tolerate seconds of
  staleness.
- You want **workload isolation** — analytics, reporting, exports, and search indexing
  off the primary, where one bad query can't take production down.
- You need a **warm standby** anyway. The same mechanism gives you reads and failover,
  which makes the first replica nearly free in justification terms.
- **Geographic read latency** — a regional replica serves local reads without a
  cross-ocean round trip.

## Don't when

- The bottleneck is **writes**. Replicas add write load; they don't remove it.
- The bottleneck is **data size**. Every replica is a full copy.
- Every read needs **read-your-writes** and you have no routing layer. You will build
  one — budget for it up front rather than discovering it in production.
- Reads are a small fraction of the workload. You're paying N× to offload a little.

## Alternative

| Switch to | When |
|-----------|------|
| **Cache in front (Redis / memcached)** | Usually try this *first*. Cheaper per read than a replica and far better on hot keys — but you own invalidation. |
| **Materialized views / precomputed read models** | The expensive reads are a handful of aggregates. Precompute beats out-scaling. |
| **CQRS with a purpose-built read store** (Elasticsearch, ClickHouse) | The read queries are shaped nothing like the write model. |
| **Sharding** | The real problem was writes or size. Replicas were never going to fix it. |
| **Storage-level replication (Aurora, Neon)** | You want many low-lag replicas without paying N× storage. |
