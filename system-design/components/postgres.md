# Component Card — PostgreSQL

**One line:** a single-primary relational engine with real ACID and the richest query
planner in open source. You scale it up and out for reads, not out for writes.

---

## Data model

Rows are stored as tuples in **heap files** — unordered 8 KB pages. Every row version
carries `xmin`/`xmax` transaction stamps, so readers never block writers (**MVCC**).
The cost of that: an `UPDATE` writes a *new* row version and leaves the old one dead
until `VACUUM` reclaims it.

| Index | Use it for | Note |
|-------|-----------|------|
| **B-Tree** | default; equality + range on scalar columns | the only type that backs a unique constraint |
| **GIN** | JSONB containment, full-text search, array membership | slow to write, fast to search |
| **GiST** | geospatial (PostGIS), ranges, nearest-neighbour | lossy, needs a recheck step |
| **BRIN** | huge append-only tables where rows correlate with physical order | tiny index, coarse filter |
| **Hash** | equality only | rarely worth it over B-Tree |

## Consistency

Full ACID. Default isolation is **Read Committed** — each statement sees a fresh
snapshot, so two statements in one transaction can see different data.

- **Repeatable Read** — one snapshot for the whole transaction.
- **Serializable** — true serializability via **SSI** (Serializable Snapshot Isolation).
  It is genuinely serializable, not a lock-based approximation.

> **Say this in the interview:** SSI does not block, it *aborts*. Conflicting
> transactions fail with a serialization error (`40001`), so the application needs a
> retry loop. Choosing Serializable is choosing to write retry logic.

## Partitioning

**Declarative partitioning** by Range, List, or Hash — one instance, many child tables.
There is **no native sharding across nodes**.

What a bad key does:

- **Defeats partition pruning.** If the query predicate doesn't include the partition
  key, the planner can't exclude partitions, so it scans *all* of them. A 100-partition
  table turns one index scan into 100.
- **Blocks global uniqueness.** The partition key must be part of every unique
  constraint and primary key, so you cannot enforce uniqueness on a non-key column
  across the whole table.
- **Makes DDL expensive.** Attaching and detaching partitions takes locks; use
  `DETACH PARTITION CONCURRENTLY` (PG 14+) and pre-validated `CHECK` constraints on
  attach to avoid a table-wide pause.

Partition count itself has a cost: planning time grows with the number of partitions,
so thousands of partitions hurt even when pruning works.

## Replication

- **Physical / streaming WAL** — byte-level copy of the primary. Whole cluster, all
  databases, read-only standbys.
- **Logical replication** — per-table row changes over a publication/subscription. Use
  for selective replication, cross-version, and migrations.

Default is **asynchronous** — RPO > 0, you can lose the last few transactions on
failover. `synchronous_commit = on` with `synchronous_standby_names` gives **RPO = 0**
at the price of a network round trip on every commit.

**There is no built-in failover.** Promotion, leader election, and fencing come from
external orchestration — **Patroni + etcd/Consul** is the standard answer, or your
cloud provider's managed equivalent.

## Failure modes

**XID wraparound → forced read-only.** Transaction IDs are 32-bit. Autovacuum starts an
anti-wraparound freeze at `autovacuum_freeze_max_age` (default **200M**); PG 14+ trips a
failsafe at `vacuum_failsafe_age` (default **1.6B**) that abandons cost throttling to
catch up. If age still reaches ~**2.1B** (2³¹), the database **refuses new write
transactions** until you vacuum in single-user mode. Almost always a symptom of
autovacuum being starved or blocked, not of write volume.

**Connection exhaustion.** One OS process per connection, several MB of RAM each. A
traffic spike without a pooler (PgBouncer, or the provider's proxy) exhausts RAM and
the machine starts swapping or OOM-killing the postmaster. Pool before you need to.

**Bloat from a held-back xmin horizon.** `VACUUM` can only remove row versions older
than the oldest snapshot any session might still need. Four things pin that horizon:
long-running transactions, sessions sitting `idle in transaction`, **unconsumed
replication slots** (the biggest silent killer — a dead replica keeps WAL and dead rows
forever), and abandoned prepared transactions. Dead rows accumulate, tables and indexes
grow, and sequential scans get slower with no schema change to blame.

**Unindexed foreign keys** are a *different* problem, often confused with the above:
every delete or update of a parent row scans the child table to check the constraint.
The result is slow cascades and lock contention — not blocked `VACUUM`.

## Scale ceiling

**~3–5 TB of active data per node** is the practical comfort zone, and it's a rule of
thumb, not a hard limit — much larger Postgres installs exist.

The real constraint is **maintenance, not capacity**: index rebuilds, `pg_dump`/restore,
major-version upgrades, and vacuum cycles all have to fit inside a window you can
tolerate. Writes are also ultimately capped by a single primary — one node's fsync
throughput.

## Cost driver

Provisioned **IOPS** on cloud block storage (EBS `gp3`/`io2`, GCP `pd-ssd`) and **RAM**
— enough for `shared_buffers`, per-operation `work_mem`, and OS page cache to keep the
working set off disk. Storage capacity itself is cheap; the IOPS you provision for it
is not.

## Ops

**Monitor**

| Signal | Where |
|--------|-------|
| Locks, blocked and long-running sessions | `pg_stat_activity` (current state) |
| Slow query aggregates | `pg_stat_statements`, `auto_explain` |
| Transaction ID age | `age(datfrozenxid)` per database |
| Replication lag + slot retention | `pg_stat_replication`, `pg_replication_slots` |
| Connection count vs `max_connections` | `pg_stat_activity` |
| Table/index bloat, vacuum progress | `pg_stat_user_tables`, `pg_stat_progress_vacuum` |

**Page on**

- XID age > **500M** (act early — 1.5B is already an emergency, not an alert)
- Inactive replication slot retaining WAL
- Disk > 85%
- Primary heartbeat loss
- Connections > 80% of `max_connections`

## Choose it when

- The domain needs **multi-table ACID transactions**, foreign keys, and cross-entity joins.
- Query patterns are **not fully known up front** — you want a planner, ad-hoc SQL, and
  the freedom to add an index later.
- Write volume fits one primary; read volume scales out on async replicas.
- You want one engine for relational, JSONB, full-text, and geospatial instead of four.

## Don't when

- Writes need **horizontal partitioning across multiple primaries**. Order of magnitude:
  tens of thousands of writes/sec is where single-primary tuning stops being fun — but
  the real number swings wildly with row size, index count, and batching, so treat it as
  a prompt to measure, not a threshold.
- **Active-active multi-region** out of the box is a hard requirement.
- The workload is analytical scans over billions of rows (that's ClickHouse/BigQuery).

## Alternative

| Switch to | When |
|-----------|------|
| **Citus / sharded Postgres** | You've outgrown one primary but want to keep Postgres, its extensions, and your SQL. The incremental step — try this before leaving. |
| **CockroachDB / YugabyteDB** | Vertical write capacity is genuinely exhausted *and* you must keep strict SQL semantics with serializable, distributed transactions. Cost: higher write latency from cross-node consensus. |
| **Vitess / sharded MySQL** | Same shape of problem, if the shop is already MySQL. |
