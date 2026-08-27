# Component Cards

One card per storage/infra primitive, following the template in
[staff-system-design.md § Part 4](../../staff-system-design.md#part-4--component-cards).

| Category | Card |
|----------|------|
| Relational | [PostgreSQL](postgres.md) |
| Key-value / document | [DynamoDB](dynamodb.md) |
| Coordination | [Raft / Consensus](raft-consensus.md) |
| Replication | [Read Replicas](read-replicas.md) |

---

## Side-by-side: PostgreSQL vs DynamoDB

| | PostgreSQL | DynamoDB |
|---|---|---|
| **Model** | relational tuples in heap files, MVCC | key-value / document items |
| **Query flexibility** | planner + ad-hoc SQL; add an index later | only what you indexed; everything else is a `Scan` |
| **Strongest consistency** | serializable (SSI, aborts on conflict) | strong per item; 100-item single-region transactions |
| **Cross-entity transactions** | native and cheap | possible, 2× cost, 100-item cap |
| **Write scaling** | one primary — scale up | horizontal, uncapped — scale out |
| **Hard limit to design around** | single-primary write throughput; maintenance window at ~3–5 TB | 1,000 WCU / 3,000 RCU **per partition key** |
| **Multi-region writes** | needs Cockroach/Yugabyte class | Global Tables, async, last-writer-wins |
| **Failover** | external (Patroni + etcd) | invisible, managed |
| **Dominant cost** | provisioned IOPS + RAM | RCU/WCU + GSI write amplification |
| **Cost of a bad key** | no partition pruning → scans every partition | hot-key throttling below table capacity |

**The decision in one sentence:** if you don't know your queries yet, or entities need to
transact together, take Postgres; if you know the access pattern, it's key-based, and
write volume is the thing that scares you, take DynamoDB.

---

## Side-by-side: Raft vs read replicas

The same problem — keep N copies of data — with opposite answers. Naming which axis you
are trading is most of the credit in a replication deep dive.

| | Raft / consensus | Read replicas |
|---|---|---|
| **What it buys** | Linearizability and automatic failover | Read throughput and workload isolation |
| **What it costs** | A quorum round trip on every write | Staleness, and read-your-writes breaking |
| **Write path** | Leader + majority fsync before ack | Primary only; replicas replay after the fact |
| **Reads** | Linearizable via ReadIndex or lease; naive leader reads are **stale** | Eventually consistent by construction |
| **On failover** | Automatic, no data loss, no human | Manual or orchestrated; async means RPO > 0 |
| **Node count effect** | More voters = **slower** writes (bigger quorum) | More replicas = more read capacity, more fan-out |
| **Right cluster size** | 3, 5, or 7 voters — always odd | As many as fan-out allows; cascade past ~5–10 |
| **Scales writes?** | No — one leader per group. Shard into many groups. | No — every replica applies every write |
| **Scales data size?** | No | No — every replica is a full copy |
| **Dominant cost** | fsync + RTT to a majority | N x storage and compute, plus transfer |
| **Signature failure** | Election storm; quorum loss needs unsafe manual recovery | Lag spike from single-threaded apply; a dead replica's slot fills the primary's disk |
| **Use it for** | Metadata, config, locks, leader election, shard assignment | Analytics, dashboards, exports, search indexing, warm standby |

**The decision in one sentence:** if a wrong answer is worse than a slow answer, pay for
consensus; if a slightly old answer is fine, take the replica and build the routing that
sends writes-then-reads back to the primary.

**What neither one does:** scale writes or reduce data size. Both of those are sharding.

---

## Corrections applied to the first draft of these cards

Kept here because these are the exact points an interviewer pushes on.

| Card | Original claim | Correction |
|---|---|---|
| Postgres | "Unindexed foreign keys or long-running transactions block `VACUUM`" | Only the long-running transactions do (plus `idle in transaction`, unconsumed replication slots, abandoned prepared transactions) — they hold back the xmin horizon. Unindexed FKs are a separate problem: full child-table scans on parent delete/update. |
| Postgres | Bad partition keys "lock tables and inflate disk bloat" | A bad key defeats **partition pruning** → every partition is scanned. It does not cause bloat. The other real traps are global uniqueness (the key must be in every unique constraint) and locks during attach/detach. |
| Postgres | "read-only shutdown when XID age hits 2 billion" | Directionally right, mechanism sharpened: aggressive freeze at 200M (`autovacuum_freeze_max_age`), failsafe at 1.6B (PG 14+), hard write refusal near 2.1B (2³¹). |
| Postgres | "Page on XID age > 1.5B" | Too late — 1.5B is already an emergency. Alert at ~500M. Added: an inactive replication slot is its own page-worthy condition. |
| Postgres | Monitor slow queries via `pg_stat_activity` | `pg_stat_activity` is current state only. Slow-query aggregates come from `pg_stat_statements` / `auto_explain`. |
| Postgres | "Scale ceiling ~3–5 TB" | Reframed as a rule of thumb, not a limit — the real constraint is whether maintenance (index rebuild, restore, major upgrade) fits a tolerable window. |
| Postgres | ">50k writes/sec" as the switch threshold | Order-of-magnitude only; swings with row size, index count, and batching. Treat as a prompt to measure. |
| Postgres | Alternative: CockroachDB / YugabyteDB | Added **Citus / sharded Postgres** as the incremental step to try first. |
| Postgres | (missing) | SSI *aborts* rather than blocks — Serializable requires application retry logic. |
| DynamoDB | "Key-Value and **Wide-Column** store" | Not wide-column (that's Cassandra/HBase/Bigtable). It's key-value/document; *item collections* give a wide-row access pattern. |
| DynamoDB | (missing) | **GSIs are eventually consistent only** — no strongly consistent read on a GSI. Big design consequence for read-your-writes. |
| DynamoDB | (missing) | **LSIs must be created with the table** and cap that partition key's item collection at 10 GB. |
| DynamoDB | "Bad keys cause hot-**partition** throttling" | Adaptive capacity now splits by throughput and isolates hot items. What still throttles is a hot **single partition key** — one key, one partition, 1k WCU / 3k RCU. |
| DynamoDB | Global Tables "active-active" | Added the catch: conflict resolution is **last-writer-wins**, so concurrent same-item writes in two regions silently lose one. Safe only with region-disjoint key ownership. Also: transactions don't span regions. |
| DynamoDB | "RPO = 0 on node crash" | Scoped to *acknowledged* writes (durable on a quorum across 3 AZs). |
| DynamoDB | (missing) | Strongly consistent reads cost **2×** eventually consistent. Conditional writes are the cheap concurrency primitive to reach for before transactions. |
| DynamoDB | Cost drivers | Added storage, PITR/backups, and `Scan` — the classic bill incident. |
| DynamoDB | Monitor list | Added `UserErrors` (4xx vs 5xx), consumed-vs-provisioned capacity, `ReplicationLatency`, and Contributor Insights for hot-key detection. |
