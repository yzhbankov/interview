# Component Card — DynamoDB

**One line:** a fully managed key-value/document store that gives you unlimited
horizontal throughput in exchange for designing every query up front.

---

## Data model

**Key-value and document**, not wide-column. (Wide-column is Cassandra, HBase,
Bigtable — a different physical model. What DynamoDB has is *item collections*: many
items sharing one partition key, which gives a wide-row **access pattern** without
being a wide-column store. Calling it wide-column in an interview invites a correction.)

- **Item** — a row. Schemaless attributes, max **400 KB** including attribute names.
- **Partition key (HASH)** — hashed to pick a physical partition. Required.
- **Sort key (RANGE)** — orders items inside one partition key. Optional, and the source
  of every interesting access pattern (ranges, `begins_with`, one-to-many).

| Index | Key | Consistency | Gotcha |
|-------|-----|-------------|--------|
| **GSI** | any attributes, different partition key | **eventually consistent only** | own throughput; added/removed anytime; max 20 per table |
| **LSI** | same partition key, different sort key | strong reads supported | **must be created with the table, never added later**; caps that partition key's item collection at 10 GB |

> **Say this in the interview:** you cannot do a strongly consistent read on a GSI. If a
> read path needs read-your-writes, it goes through the base table or an LSI.

## Consistency

**Per-item**, not across items:

- **Eventually consistent read** — default, 0.5 RCU per 4 KB.
- **Strongly consistent read** — 1 RCU per 4 KB, so **2× the cost**. Base table and LSI only.
- **Conditional writes** — `ConditionExpression` gives compare-and-set on a single item.
  This is the cheap concurrency primitive; reach for it before transactions.

**Multi-item ACID** via `TransactWriteItems` / `TransactGetItems`:

- **2× the capacity cost** (prepare + commit).
- Max **100 items / 4 MB**.
- **Single region only** — transactions do not span Global Table replicas.

## Partitioning

Fully managed auto-partitioning. A partition splits when it exceeds **10 GB** of storage
or its throughput share. Hard per-partition limits: **1,000 WCU / 3,000 RCU**.

**Adaptive capacity** now handles most of the classic problem — DynamoDB splits
partitions by throughput and isolates frequently-accessed items automatically. What it
*cannot* fix is a hot **single partition key**: one logical key lives on one partition,
so it caps at 1,000 WCU / 3,000 RCU no matter how much table-level capacity you have.

So the failure is a **hot key**, not a hot partition. Fix it by widening the key —
write sharding (`key#0`..`key#N`), a time or tenant component, or moving the counter
into a cache.

## Replication

**Within a region:** every partition is replicated to **3 storage nodes across 3 AZs**.
Multi-Paxos elects the leader; a write is acknowledged once durably on a quorum. For an
acknowledged write, **RPO = 0** on node or AZ loss. Nothing to configure, nothing to
fail over.

**Across regions:** **Global Tables** give active-active, **asynchronous**, RPO of
roughly seconds.

> **The catch nobody mentions:** conflict resolution is **last-writer-wins** on a
> timestamp. Two regions updating the same item concurrently means one update is
> silently discarded. Global Tables are safe when regions own disjoint key ranges (per
> user, per tenant); they are not a general-purpose multi-master.

## Failure modes

**Hot key throttling.** Concentrated traffic on one partition key returns
`ProvisionedThroughputExceededException` (or `ThrottlingException` on-demand) while the
table sits far below its total capacity. The symptom is confusing precisely because
account-level headroom looks fine.

**GSI backpressure.** In provisioned mode, if a GSI lacks the write capacity to keep up,
throttling propagates **back to the base table** — writes to the table start failing
because of an index. On-demand largely avoids it. Always size GSI writes for the base
table's write rate times the number of GSIs the item touches.

**Item size overflow.** At 400 KB the write is rejected outright. Offload the payload to
S3 and store a pointer, or split across items in one collection.

**Scan cost blowout.** A `Scan` reads every item and bills for all of it. One
un-paginated `Scan` in a hot code path is the classic DynamoDB bill incident.

**On-demand cold start.** A brand-new on-demand table serves ~2× its previous peak;
a genuine flat-start spike still throttles until it scales. Pre-warm before a launch.

## Scale ceiling

**Storage and total throughput are effectively uncapped.** The ceiling is always
**per-key**, never per-table:

- **1,000 WCU / 3,000 RCU per partition key** — the real limit you design against.
- **400 KB** per item.
- **10 GB** per item collection *only when the table has an LSI*; without an LSI a
  partition key's items can spread across partitions and exceed it.
- **100 items / 4 MB** per transaction; **1 MB** per Query/Scan page.

## Cost driver

- **RCU/WCU** (provisioned + autoscaling) or **on-demand request** pricing. On-demand
  costs roughly 5–7× per request but bills nothing idle — spiky and unpredictable
  traffic usually still wins on it.
- **GSI write amplification** — one base write becomes N+1 writes with N matching GSIs.
- **Global Table cross-region replication** — a replicated write is charged in each
  region, plus data transfer.
- **Storage** at $/GB-month, plus **PITR and on-demand backups**.
- **Scans**, per above.

## Ops

**Monitor** (CloudWatch)

| Signal | Metric |
|--------|--------|
| Throttling | `ThrottledRequests`, `ReadThrottleEvents`, `WriteThrottleEvents` |
| Server-side failures | `SystemErrors` (5xx) |
| Client-side failures | `UserErrors` (4xx — usually a bad request or a conditional-write failure) |
| Capacity headroom | `ConsumedRead/WriteCapacityUnits` vs provisioned |
| Global Table health | `ReplicationLatency`, `PendingReplicationCount` |
| Hot keys | CloudWatch Contributor Insights for DynamoDB |

**Page on**

- Sustained throttling on any table or GSI (a brief burst is normal, a plateau is not)
- `SystemErrors` above baseline
- `ReplicationLatency` breaching its SLO
- Provisioned utilization pinned at 100% with autoscaling already at max

## Choose it when

- **Access patterns are known and stable**, and they're key-based lookups: get by id,
  range within one key.
- You need **massive autoscaling write throughput** with single-digit-millisecond p99,
  and the key space spreads naturally (per-user, per-device, per-tenant).
- **Zero database operations** is worth real money — no patching, no failover, no
  capacity planning for storage.
- Traffic is spiky enough that paying per request beats provisioning for peak.

## Don't when

- Queries are **ad-hoc or analytical** — unpredictable filters, aggregations, joins
  across entities. There is no query planner; anything you didn't index is a `Scan`.
- The workload does **bulk updates or full-table scans** as a routine operation.
- Access patterns are **still being discovered**. Changing them later means a table
  redesign and a backfill, not an `ALTER TABLE`.
- You need cross-item transactions as the norm rather than the exception.

## Alternative

| Switch to | When |
|-----------|------|
| **Cassandra / ScyllaDB** | You want the same partitioned write scaling without AWS lock-in — open source, multi-cloud or on-prem, and cheaper at massive *continuous* throughput (DynamoDB's pricing rewards spiky, punishes flat-and-huge). Cost: you now run repair, compaction, and topology yourself. |
| **Postgres + Citus** | It turns out you needed joins and ad-hoc queries after all. |
| **DynamoDB + DAX or ElastiCache** | The read path is hot but the data model is fine — cache in front rather than switch engines. |
