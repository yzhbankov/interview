# System Design Interview Preparation

## Session 6: Ad/Click Aggregator

---

## 1. Session Context & Goal

**Purpose:**

* Week 6 of system design interview preparation
* Practice designing a **high-throughput ad click collection and aggregation system**
* Focus on streaming pipelines, idempotency, and analytics at scale

**Problem Statement:**
Design a system that records user ad clicks, redirects users to the advertiser's website, and provides advertisers with near-real-time click metrics aggregated at 1-minute granularity.

---

## 2. Assumptions & Scope

* Users click on ads embedded in third-party websites; the click triggers a redirect and is recorded
* Advertisers query click metrics over time (1-minute buckets) via an analytics API
* 10 million active ads at any given time
* Peak load: 10,000 clicks per second
* Data retention: 1 year (for aggregated data)
* Idempotency: a single ad impression should be counted at most once

**Out of Scope:**
* Conversion tracking (post-click actions)
* Real-time bidding (RTB)
* Demographic profiling
* Authentication and billing

---

## 3. Requirements

### 3.1 Functional Requirements

* When a user clicks an ad, they are redirected to the advertiser's website
* Advertisers can query click metrics over a time range with 1-minute granularity
* Advertisers can create and manage their ads (redirect URLs, metadata)
* Browser/client can retrieve ad content and placement for rendering

---

### 3.2 Non-Functional Requirements

* **Scalable:** support peaks of 10,000 clicks per second
* **Low latency analytics:** query response time < 1 second
* **Fault tolerant:** no click data loss; system degrades gracefully
* **Data integrity:** click counts must be accurate
* **Idempotency:** each ad impression should be counted at most once, even with multiple clicks

---

## 4. Scale & Capacity Assumptions

* Active ads: 10 million at any time
* Peak click throughput: 10,000 clicks/second
* Analytics granularity: 1 minute
* Data retention: 1 year (aggregates)

---

## 5. Capacity Estimation

### Raw Click Volume (if stored)

```
Clicks per second:           10,000
Seconds per year:            365 × 24 × 3600 ≈ 36 million
Total raw records:           10,000 × 36M = 360 billion records
```

> Storing raw events at this scale is expensive and unnecessary given the 1-minute granularity requirement. We skip raw storage and aggregate directly in the stream.

### OLAP Aggregated Storage

```
Clicks per minute:           10,000 × 60 = 600,000
Max unique ad-minute rows:   600,000 per minute (one row per ad per minute)
Rows per year:               600K × 525,600 min/year ≈ 315 billion (theoretical max)
Realistic rows per year:     Much smaller — most ads receive zero clicks in most minutes
```

> In practice the OLAP table is small: index on (ad_uid, minute) enables sub-second queries.

### Ad Metadata Storage

```
Total ads:                   10 million
Row size:                    ~500 bytes
Total:                       ~5 GB — fits comfortably in PostgreSQL
```

---

## 6. Core Entities

### Ad

* Ad UID
* Advertiser UID
* Redirect URL (destination when clicked)
* Title / creative metadata
* Status (active / paused / archived)

### Advertiser

* Advertiser UID
* Name, metadata

### Impression (ephemeral, in Redis)

* Impression ID (UUID, generated at placement time)
* Ad UID
* HMAC signature (prevents forgery)
* TTL: 24 hours

### OLAP Aggregate

* Ad UID
* Minute (timestamp truncated to minute)
* Total clicks

---

## 7. System Interface (API)

### Ad Click Redirect

```
GET /click/{ad_uid}?impression_id={id}
Headers: X-Impression-Signature: {hmac}

Response: 302 Found
  Location: {advertiser_redirect_url}
```

### Ad Analytics Query

```
GET /api/v1/analytics/ads/{ad_uid}/clicks
  ?from={timestamp}&to={timestamp}&granularity=minute

Response: 200 OK
{
  "ad_uid": "...",
  "data": [
    { "minute": "2024-01-01T10:00:00Z", "clicks": 342 },
    ...
  ],
  "total": 1842
}
```

### Ad Placement (for browser rendering)

```
GET /api/v1/ads/{ad_uid}/placement

Response: 200 OK
{
  "ad_uid": "...",
  "title": "...",
  "impression_id": "uuid...",
  "impression_signature": "hmac...",
  "click_url": "..."
}
```

---

## 8. High-Level Design (HLD)

### Components

* **API Gateway:** routing, SSL termination, rate limiting, horizontal scaling entry point
* **Ad Placement Service:** serves ad content to browsers; generates impression ID + HMAC signature; stores impression in Redis
* **Click Processor Service:** stateless; validates impression; deduplicates via Redis; looks up redirect URL; publishes to Kinesis; returns 302 redirect
* **Ad DB (PostgreSQL):** stores ad metadata and redirect URLs; ~5 GB, fits standard SQL
* **Redis:** stores impression state (clicked / not clicked) with TTL; stores HMAC signing key
* **AWS Kinesis:** durable, high-throughput event stream for click events; acts as buffer between click processors and aggregation
* **Apache Flink:** consumes click events from Kinesis; applies 1-minute tumbling window per ad; writes aggregated counts to OLAP DB
* **OLAP DB (PostgreSQL):** stores pre-aggregated click counts per ad per minute; indexed on (ad_uid, minute)
* **Analytics API Service:** queries OLAP DB; serves advertiser analytics requests

### Architecture Flow

```
End User (Browser)
        │
        │  1. Load webpage with ad
        ▼
Ad Placement Service ──────────────────────── Ad DB (PostgreSQL)
        │                                      (redirect URLs, metadata)
        │  Generate impression_id + HMAC
        │  Store in Redis (TTL 24h)
        │
        │  2. Return ad content + impression_id to browser
        │
        │  User clicks ad
        ▼
   API Gateway
  (load balancing, SSL)
        │
        ▼
Click Processor (stateless, horizontally scaled)
        │
        ├── Validate HMAC signature on impression_id
        ├── Check Redis: already clicked?
        │     Yes → drop click
        │     No  → mark clicked in Redis
        ├── Read redirect URL from Ad DB (or Redis cache)
        ├── Publish click event to AWS Kinesis
        └── Return 302 redirect to browser

              │
              ▼
         AWS Kinesis
      (click event stream)
              │
              ▼
        Apache Flink
   (1-min tumbling window
    aggregation per ad_uid)
              │
              ▼
         OLAP DB (PostgreSQL)
    (ad_uid, minute, total_clicks)
              │
              ▼
       Analytics API Service
              │
              ▼
        Advertiser Dashboard


Advertiser also manages ads via:
Ad Placement Service ──► Ad DB (create / update ads)
```

---

## 9. Deep Dive: Impression-Based Idempotency

### Problem

A user can click an ad multiple times. Each duplicate click inflates the advertiser's metrics. We need to count each impression at most once.

### Solution

1. When the **Ad Placement Service** serves an ad, it generates a UUID `impression_id` and an HMAC signature using a shared secret key stored in Redis.
2. The impression is stored in Redis with `{ clicked: false, TTL: 24h }`.
3. When the **Click Processor** receives a click:
   * It verifies the HMAC signature — rejects unsigned or tampered impressions.
   * It checks Redis: if `clicked: true`, the click is dropped.
   * If `clicked: false`, it atomically sets `clicked: true` and forwards the event to Kinesis.

### Why HMAC (Symmetric Signing)?

* The signing key never leaves our internal services — clients cannot forge impression IDs
* Symmetric HMAC is fast and sufficient for internal use
* Key rotation is supported by versioning keys in Redis / Secrets Manager

### Redis Key Design

```
impression:{impression_id}  →  { ad_uid, clicked: bool }  TTL: 24h
signing_key:v1              →  {base64-encoded key}
```

---

## 10. Deep Dive: Streaming Pipeline

### Why No Raw Click Storage?

Storing 360 billion raw records per year in Cassandra or ClickHouse is expensive and complex to query. Since the requirement is 1-minute granularity, we can aggregate directly in the stream and write only aggregated rows to the OLAP DB.

### Kinesis Configuration

```
Stream:         click-events
Shards:         10 (each shard handles 1,000 records/sec → 10K total)
Retention:      7 days (replay buffer for recovery)
Partition key:  ad_uid (preserves ordering per ad)
```

### Flink Aggregation

* Consumes from Kinesis with at-least-once delivery
* Applies **1-minute tumbling window** keyed by `ad_uid`
* Emits `(ad_uid, window_start, count)` at end of each window
* Writes to OLAP DB via **upsert** (idempotent on conflict)
* Checkpoints every 30 seconds — can resume from Kinesis on failure

### Fault Tolerance

| Component | Failure | Recovery |
|-----------|---------|----------|
| Click Processor | Crash | API Gateway routes to another instance |
| Kinesis | Shard failure | AWS automatic multi-AZ failover |
| Flink | Job crash | Restore from 30s checkpoint, replay from Kinesis |
| OLAP DB | Write failure | Flink retries; idempotent upsert |

---

## 11. Deep Dive: Analytics Query Performance

### OLAP DB Schema

```sql
CREATE TABLE ad_click_aggregates (
    ad_uid       UUID        NOT NULL,
    minute       TIMESTAMP   NOT NULL,
    total_clicks INT         NOT NULL,
    PRIMARY KEY (ad_uid, minute)
);

CREATE INDEX idx_ad_minute ON ad_click_aggregates (ad_uid, minute DESC);
```

### Example Query

```sql
SELECT minute, total_clicks
FROM ad_click_aggregates
WHERE ad_uid = $1
  AND minute BETWEEN $2 AND $3
ORDER BY minute ASC;
```

* Single partition scan (by `ad_uid`)
* Time-range filtered by index
* Returns in milliseconds for any reasonable time range

---

## 12. Technology Choices

### Ad DB: PostgreSQL

* 10 million ads at ~500 bytes each = ~5 GB — small for SQL
* Reliable, simple, well-understood
* Supports read replicas if needed

### Streaming: AWS Kinesis

* Fully managed, durable, highly available
* Scales to 10K events/sec with 10 shards
* 7-day replay buffer for fault recovery

### Stream Processor: Apache Flink

* Low-latency stream processing with stateful windows
* Native Kinesis source connector
* Exactly-once processing with checkpointing

### OLAP Storage: PostgreSQL

* Pre-aggregated data is small (~600K rows/minute theoretical max, much less in practice)
* Standard SQL queries serve < 1 second latency
* Can be upgraded to TimescaleDB or ClickHouse if scale increases

### Deduplication: Redis

* Sub-millisecond reads for impression lookups
* TTL-managed memory (24h impression window)
* Shared signing key accessible to all Click Processor instances

---

## 13. Requirements Fulfillment

| Requirement | Solution |
|-------------|----------|
| Ad click redirect | Click Processor reads redirect URL from Ad DB, returns 302 |
| Advertiser analytics query | Analytics API queries pre-aggregated OLAP DB |
| 10K clicks/sec scalability | Stateless Click Processor + Kinesis (10 shards) + Flink |
| < 1s analytics latency | OLAP DB with (ad_uid, minute) index; small dataset |
| Fault tolerance | Kinesis durability + Flink checkpoints + multi-instance processors |
| Idempotency of clicks | Impression ID + HMAC signature + Redis deduplication |
| Data integrity | Signed impressions + idempotent Flink upserts |

---

## 14. Summary & Reflection

* The key architectural decision is to **skip raw click storage entirely** — aggregating directly in the Flink stream is simpler, cheaper, and meets the 1-minute granularity requirement perfectly
* **Impression IDs with HMAC signing** solve idempotency at the right level of granularity — per impression rather than per user — allowing legitimate repeat engagement while preventing fraud
* **AWS Kinesis as the event buffer** decouples the Click Processor from the aggregation pipeline, providing fault tolerance without tight coupling
* The system is **split into two distinct write paths**: click/redirect (latency-sensitive, stateless) and analytics (throughput-sensitive, batch-like); keeping these separate simplifies scaling each independently
* Next improvements to consider: sub-minute analytics with sliding windows in Flink; raw event archival to S3 for historical re-aggregation; multi-region deployment with geo-routing

---

*End of Session 6*
