# Ad/Click Aggregator System Design
## FAANG Senior Engineer Level

---

## 1. Problem Clarification & Scope

### Core Problem
Design an **ad click aggregator** — a system that collects, processes, and aggregates ad click events in near-real time. Advertisers use this system to track ad performance and optimize their campaigns. When a user clicks an ad on a platform (e.g., Facebook), they are redirected to the advertiser's website and the click is recorded. Advertisers can then query click metrics over time to understand how their ads are performing.

### Clarifying Questions to Ask
- Do we need to track conversions (not just clicks, but resulting purchases)?
- Do we need demographic profiling or user-level analytics?
- Do we need spam/bot click detection?
- What is the expected query granularity? (Assumed: 1-minute buckets)
- Do we need real-time dashboards or is slight delay acceptable?
- What is the data retention period? (Assumed: 1 year for raw stream, indefinite for aggregates)
- Do we need to support ad impression tracking in addition to clicks?

### Scope Definition

**In Scope:**
- Redirect user to advertiser's website on ad click
- Record and process click events at high throughput
- Aggregate click metrics with 1-minute granularity
- Provide low-latency analytics API for advertisers
- Idempotency: deduplicate clicks per ad impression
- Fault tolerance and data integrity

**Out of Scope (mention but don't design):**
- Advertiser billing and invoicing
- Conversion tracking (post-click actions)
- Real-time bidding (RTB) or ad serving optimization
- Full authentication and authorization flows
- Demographic profiling and user segmentation

---

## 2. Requirements

### Functional Requirements

| Requirement | Priority | Description |
|-------------|----------|-------------|
| Ad click redirect | P0 | When user clicks an ad, system redirects them to the advertiser's website |
| Click metric query | P0 | Advertisers can query click counts over time with 1-minute granularity |
| Ad management | P1 | Advertisers can create and manage their ads (redirect URLs, metadata) |
| Ad placement | P1 | Browser/client can retrieve ad content for display |

### Non-Functional Requirements

| Requirement | Target | Rationale |
|-------------|--------|-----------|
| Scalability | 10,000 clicks/sec peak | High-traffic events (Black Friday, viral campaigns) |
| Analytics latency | < 1 second | Advertisers need fast feedback on campaign performance |
| Fault tolerance | No data loss | Every click is revenue-relevant |
| Data integrity | Accurate counts | Incorrect metrics mean incorrect billing |
| Idempotency | Deduplicate clicks | Prevent click fraud and double-counting |
| Ads supported | 10 million active ads | Large advertiser base at any time |

---

## 3. Capacity Estimation

### Traffic Assumptions
```
Active ads at any time:      10 million
Peak click throughput:       10,000 clicks/second
Data retention:              1 year
Analytics granularity:       1 minute
```

### Click Storage (Raw Events)
```
Clicks per second:           10,000
Seconds per year:            365 × 24 × 3600 ≈ 31.5 million
Total raw click records:     10,000 × 31.5M ≈ 360 billion records
```

> At this scale, a raw click store is expensive to maintain and slow to query.
> The key insight is to skip raw storage entirely and stream directly to aggregation.

### OLAP (Aggregated) Storage
```
Aggregation window:          1 minute = 60 seconds
Max unique ad-minute pairs:  10M ads × (365 × 24 × 60) minutes/year
                             = 10M × 525,600 ≈ 5.25 trillion rows (theoretical max)

Realistic OLAP rows:         Only ads that received clicks in a given minute
At 10K clicks/sec, 600K clicks/minute spread across 10M ads
→ At most 600K new rows per minute → small, manageable dataset
```

### Ad Metadata Storage
```
Total active ads:            10 million
Record size:                 ~500 bytes (UID, redirect URL, metadata)
Total metadata:              10M × 500 bytes = ~5 GB
```

> Ad metadata is small and fits comfortably in a standard SQL database with caching.

### Bandwidth
```
Click payload size:          ~1 KB (click event + metadata)
Ingestion bandwidth:         10,000 × 1 KB = 10 MB/sec = ~80 Mbps
```

---

## 4. Core Entities & Data Model

### Entities

```
┌──────────────────────────────────────────────────────┐
│ Ad                                                    │
├──────────────────────────────────────────────────────┤
│ ad_uid: UUID            (primary key)                 │
│ advertiser_uid: UUID    (owner)                       │
│ redirect_url: string    (destination URL)             │
│ title: string           (ad display text)             │
│ metadata: jsonb         (targeting, creative info)    │
│ status: enum            (active | paused | archived)  │
│ created_at: timestamp                                 │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│ Advertiser                                            │
├──────────────────────────────────────────────────────┤
│ advertiser_uid: UUID    (primary key)                 │
│ name: string                                          │
│ metadata: jsonb         (billing, contact info)       │
│ created_at: timestamp                                 │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│ Impression (in-memory / Redis)                        │
├──────────────────────────────────────────────────────┤
│ impression_id: UUID     (generated at placement time) │
│ ad_uid: UUID            (which ad was shown)          │
│ user_uid: UUID          (who saw it)                  │
│ issued_at: timestamp    (TTL-managed in Redis)        │
│ signature: string       (HMAC to prevent forgery)     │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│ OLAP Aggregate (PostgreSQL / analytics DB)            │
├──────────────────────────────────────────────────────┤
│ ad_uid: UUID            (partition key)               │
│ minute: timestamp       (truncated to minute)         │
│ total_clicks: int       (aggregated count)            │
│ PRIMARY KEY (ad_uid, minute)                          │
└──────────────────────────────────────────────────────┘
```

---

## 5. System Interface (API)

### Click Redirect (User → System)

```
GET /click/{ad_uid}?impression_id={impression_id}

Headers:
  X-Impression-Signature: {hmac_signature}

Response: 302 Found
  Location: {advertiser_redirect_url}
```

### Get Ad Analytics (Advertiser → System)

```
GET /api/v1/analytics/ads/{ad_uid}/clicks
  ?from={ISO8601_timestamp}
  &to={ISO8601_timestamp}
  &granularity=minute

Response: 200 OK
{
  "ad_uid": "...",
  "data": [
    { "minute": "2024-01-01T10:00:00Z", "clicks": 342 },
    { "minute": "2024-01-01T10:01:00Z", "clicks": 289 },
    ...
  ],
  "total": 1842
}
```

### Ad Placement (Browser → System)

```
GET /api/v1/ads/{ad_uid}/placement

Response: 200 OK
{
  "ad_uid": "...",
  "title": "...",
  "impression_id": "uuid-...",
  "impression_signature": "hmac-...",
  "click_url": "https://our-system.com/click/{ad_uid}?impression_id=..."
}
```

### Ad Management (Advertiser → System)

```
POST /api/v1/ads
Body: { "redirect_url": "...", "title": "...", "metadata": {...} }
Response: 201 Created { "ad_uid": "..." }

PATCH /api/v1/ads/{ad_uid}
Body: { "status": "paused" }
Response: 200 OK
```

---

## 6. High-Level Architecture

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                     Advertiser (Browser / API Client)                │
│                                                                      │
│    Manage Ads ──────────────────────────────────────────────────┐   │
│    Query Analytics ─────────────────────────────────────────┐   │   │
└────────────────────────────────────────────────────────────────┼───┘
                                                                │
┌─────────────────────────────────────────────────────────────────────┐
│                     End User (Browser)                               │
│                                                                      │
│    Load webpage with ad ─────────────────────────────────────────►  │
│    Click ad ────────────────────────────────────────────────────►   │
└────────────────────────────────────────────────────────────────────┘
         │                     │
         ▼                     ▼
┌─────────────────────────────────────────────┐
│              API Gateway                     │
│  (Load balancing, SSL, rate limiting,        │
│   geographic routing)                        │
└──────────┬─────────────┬────────────────────┘
           │             │
           ▼             ▼
┌──────────────┐  ┌──────────────────┐
│  Ad Placement │  │  Click Processor │
│  Service      │  │  Service         │
│               │  │                  │
│ - Serves ad   │  │ - Validates click│
│   content     │  │ - Checks Redis   │
│ - Generates   │  │   impression     │
│   impression  │  │ - Returns 302    │
│   ID + HMAC   │  │   redirect       │
│               │  │ - Publishes to   │
└──────┬────────┘  │   Kinesis        │
       │           └────────┬─────────┘
       │                    │
       ▼                    ▼
┌──────────────┐  ┌─────────────────────┐
│  Ad DB        │  │   AWS Kinesis        │
│  (PostgreSQL) │  │   (Click Event       │
│               │  │    Stream)           │
│ - Ad metadata │  │                     │
│ - Redirect    │  │ - Durable, ordered  │
│   URLs        │  │ - 10K+ events/sec   │
└──────────────┘  └──────────┬──────────┘
                              │
                              ▼
                  ┌───────────────────────┐
                  │   Apache Flink         │
                  │   (Stream Processor)   │
                  │                        │
                  │ - Consumes from Kinesis│
                  │ - 1-minute tumbling    │
                  │   window aggregation  │
                  │ - Deduplication       │
                  │ - Writes to OLAP DB   │
                  └───────────┬───────────┘
                              │
                              ▼
                  ┌───────────────────────┐      ┌──────────────────┐
                  │   OLAP DB              │◄─────│  Analytics API   │
                  │   (PostgreSQL)         │      │  Service         │
                  │                        │      │                  │
                  │ - ad_uid + minute      │      │ - Query OLAP DB  │
                  │ - total_clicks         │      │ - Serve results  │
                  │ - Indexed on ad_uid    │      │   to advertisers │
                  └───────────────────────┘      └──────────────────┘

     ┌────────────────────┐
     │   Redis             │
     │   (Impression Store)│
     │                     │
     │ - impression_id:    │
     │   {clicked: bool}   │
     │ - TTL-managed       │
     │ - HMAC signing key  │
     └────────────────────┘
```

---

## 7. Data Flow

### Click Event Flow
```
1.  Browser loads webpage containing ad (served by Ad Placement Service)
2.  Ad Placement Service generates impression_id + HMAC signature
3.  Ad Placement Service stores impression_id in Redis (TTL: e.g., 24h)
4.  Ad content + impression_id is returned to browser
5.  User clicks the ad
6.  Browser sends GET /click/{ad_uid}?impression_id={id} with signature header
7.  Click Processor validates HMAC signature on impression_id
8.  Click Processor checks Redis: has this impression_id been clicked?
    a. Yes → drop the click (idempotency protection)
    b. No  → mark as clicked in Redis, proceed
9.  Click Processor looks up redirect_url for ad_uid in Ad DB (or cache)
10. Click Processor publishes click event to AWS Kinesis
11. Click Processor returns 302 redirect to browser
12. Browser follows redirect to advertiser's website
```

### Analytics Aggregation Flow
```
1.  Apache Flink continuously reads click events from AWS Kinesis
2.  Flink applies 1-minute tumbling window per ad_uid
3.  At end of each minute window, Flink emits (ad_uid, minute, count)
4.  Flink writes aggregated rows to OLAP DB (upsert on conflict)
5.  Advertiser queries Analytics API for their ad's metrics
6.  Analytics API queries OLAP DB with ad_uid + time range
7.  Results returned in < 1 second
```

---

## 8. Deep Dive: Idempotency & Click Deduplication

### The Problem
A user can click an ad multiple times — accidentally or maliciously. Each duplicate click would inflate the advertiser's metrics and potentially their billing.

### Solution: Impression ID with HMAC Signing

```
Ad Placement Service                  Redis                Click Processor
        │                               │                        │
        │── generate impression_id ──►  │                        │
        │── store impression_id ──────► │ {clicked: false, TTL}  │
        │                               │                        │
        │◄─ return ad + impression_id ──────────────────────────►│
        │                               │                        │
        │   (User clicks ad)            │                        │
        │──────────────────────── click + impression_id ────────►│
        │                               │                        │
        │                               │◄── check impression ───│
        │                               │                        │
        │                               │ if clicked: drop       │
        │                               │ if not: mark clicked   │
        │                               │         → Kinesis      │
```

### HMAC Signing Flow
```python
# Ad Placement Service — generate and sign impression ID
import hmac, hashlib, uuid

def generate_impression(ad_uid: str, signing_key: bytes) -> dict:
    impression_id = str(uuid.uuid4())
    message = f"{impression_id}:{ad_uid}"
    signature = hmac.new(signing_key, message.encode(), hashlib.sha256).hexdigest()
    return {
        "impression_id": impression_id,
        "signature": signature
    }

# Click Processor — verify impression signature
def verify_impression(impression_id: str, ad_uid: str,
                       signature: str, signing_key: bytes) -> bool:
    message = f"{impression_id}:{ad_uid}"
    expected = hmac.new(signing_key, message.encode(), hashlib.sha256).hexdigest()
    return hmac.compare_digest(expected, signature)
```

### Why Symmetric HMAC?
- The signing key never leaves our internal services — client cannot forge impressions
- HMAC-SHA256 is fast and computationally cheap
- The same key is used for signing (placement service) and verification (click processor)
- Key can be rotated by storing versioned keys in Redis/Secrets Manager

### Redis Key Design
```
# Impression state
Key:   impression:{impression_id}
Value: { "ad_uid": "...", "clicked": false }
TTL:   24 hours (impression expires after 1 day)

# Signing key (versioned)
Key:   signing_key:v1
Value: {base64-encoded HMAC key}
```

---

## 9. Deep Dive: Streaming Pipeline (Kinesis + Flink)

### Why Skip Raw Storage?

| Approach | Storage | Query Latency | Cost |
|----------|---------|---------------|------|
| Store raw click events (Cassandra) | 360B rows/year | Very slow without heavy indexing | Very high |
| Stream + aggregate with Flink | ~600K rows/minute in OLAP | < 1 second | Low |

Streaming directly to aggregated OLAP is simpler, cheaper, and meets the 1-minute granularity requirement perfectly.

### Flink Aggregation Logic

```java
// Flink job: 1-minute tumbling window aggregation per ad
DataStream<ClickEvent> clicks = env
    .addSource(new FlinkKinesisConsumer<>(
        "click-events", new ClickEventSchema(), kinesisConfig));

clicks
    .keyBy(ClickEvent::getAdUid)
    .window(TumblingProcessingTimeWindows.of(Time.minutes(1)))
    .aggregate(new ClickCountAggregator())
    .addSink(new OlapDatabaseSink());

// Aggregator logic
class ClickCountAggregator implements AggregateFunction<ClickEvent, Long, AggregateResult> {
    public Long createAccumulator() { return 0L; }
    public Long add(ClickEvent event, Long count) { return count + 1; }
    public AggregateResult getResult(Long count) {
        return new AggregateResult(adUid, windowStart, count);
    }
    public Long merge(Long a, Long b) { return a + b; }
}
```

### Kinesis Configuration
```
Stream:          click-events
Shards:          10 (each handles 1MB/s or 1000 records/s)
                 → 10 shards handles 10,000 clicks/sec
Retention:       7 days (replay buffer for reprocessing)
Partition key:   ad_uid (ensures order per ad)
```

### Fault Tolerance in the Pipeline

| Component | Failure | Recovery |
|-----------|---------|----------|
| Click Processor | Crash | API Gateway routes to another instance |
| Kinesis | Shard unavailable | Automatic failover, multi-AZ by default |
| Flink | Job failure | Checkpoints every 30s, resume from last checkpoint |
| OLAP DB | Write failure | Flink retries with idempotent upsert |

---

## 10. Deep Dive: Analytics API & OLAP Query Performance

### OLAP DB Schema

```sql
CREATE TABLE ad_click_aggregates (
    ad_uid      UUID        NOT NULL,
    minute      TIMESTAMP   NOT NULL,
    total_clicks INT         NOT NULL,
    PRIMARY KEY (ad_uid, minute)
);

-- Partition by ad_uid for fast per-ad queries
-- Index on minute for time-range queries
CREATE INDEX idx_ad_minute ON ad_click_aggregates (ad_uid, minute DESC);
```

### Example Analytics Query

```sql
-- Get per-minute click counts for an ad over the last hour
SELECT minute, total_clicks
FROM ad_click_aggregates
WHERE ad_uid = $1
  AND minute >= NOW() - INTERVAL '1 hour'
ORDER BY minute ASC;
```

This query hits a single partition (ad_uid), scans at most 60 rows, and returns in milliseconds.

### Scaling the OLAP DB

| Scale Level | Approach |
|-------------|----------|
| Small (< 1M ads) | Single PostgreSQL with partitioning |
| Medium (1–10M ads) | Read replicas + connection pooling |
| Large (> 10M ads) | Partition table by ad_uid hash; or use TimescaleDB |

---

## 11. Scalability & Geographic Distribution

### Horizontal Scaling

```
                    ┌─────────────────────────┐
                    │      API Gateway          │
                    │  (AWS API Gateway /       │
                    │   CloudFront + ALB)        │
                    └────────────┬────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              ▼                  ▼                  ▼
   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
   │ Click        │   │ Click        │   │ Click        │
   │ Processor 1  │   │ Processor 2  │   │ Processor N  │
   │ (stateless)  │   │ (stateless)  │   │ (stateless)  │
   └──────────────┘   └──────────────┘   └──────────────┘
```

- Click Processor is stateless — scales horizontally with auto-scaling groups
- Ad DB read replicas serve redirect URL lookups (cached with Redis/Memcached)
- Kinesis shards can be increased to handle more throughput

### Caching Strategy

| Data | Cache | TTL | Rationale |
|------|-------|-----|-----------|
| Ad redirect URL | Redis/Memcached | 5 min | Hot path — every click needs this |
| OLAP query results | App-level cache | 30 sec | Analytics queries repeat for same ad |
| Impression IDs | Redis | 24 hours | Deduplication window |
| Signing key | Redis | Rotated | Shared by all click processors |

---

## 12. Fault Tolerance & Data Integrity

### End-to-End Durability

```
User Click
    │
    ▼
API Gateway         → Replicated, multi-AZ
    │
    ▼
Click Processor     → Stateless, multiple instances; if one dies, others handle traffic
    │
    ▼
AWS Kinesis         → Durable, multi-AZ, 7-day replay buffer
    │
    ▼
Apache Flink        → Checkpointed every 30s; can replay from Kinesis on failure
    │
    ▼
OLAP DB             → Upsert writes; idempotent; replicated
```

### What Happens When Things Fail

| Failure | Impact | Mitigation |
|---------|--------|------------|
| Click Processor crash | Some clicks in-flight lost | Kinesis not yet written; user retries redirect |
| Kinesis shard unavailable | Click events delayed | AWS auto-failover; Flink retries |
| Flink job crashes | Aggregation delayed | Restores from checkpoint; replays from Kinesis |
| Redis unavailable | Deduplication skipped | Click processor can fail-open (allow click) or fail-closed (drop click) |
| OLAP DB unavailable | Analytics queries fail | Analytics API returns cached or error; Flink queues writes |

---

## 13. Summary: Requirements Mapping

| Requirement | Solution | Component(s) |
|-------------|----------|--------------|
| Ad click redirect | Read redirect URL from Ad DB, return 302 | Click Processor + Ad DB |
| Click metrics query | 1-min aggregates in OLAP DB | Flink + OLAP DB + Analytics API |
| 10K clicks/sec | Stateless processors + Kinesis + Flink | Click Processor + Kinesis |
| < 1s analytics latency | Pre-aggregated OLAP, indexed by ad_uid | OLAP DB + Analytics API |
| Fault tolerance | Kinesis durability + Flink checkpoints | Kinesis + Flink |
| Idempotency | Impression ID + HMAC + Redis dedup | Placement Service + Redis + Click Processor |
| Data integrity | Signed impressions + idempotent OLAP upserts | HMAC signing + Flink |

---

## 14. Interview Discussion Points

### Trade-offs Made

1. **Skip raw click storage entirely:** Store aggregates only → much cheaper and simpler, but you lose the ability to re-aggregate at different granularities or backfill historical data
2. **1-minute tumbling windows in Flink:** Simple and meets requirements, but doesn't support sub-minute analytics or sliding window queries
3. **Impression-based deduplication over user-based:** More precise (allows same user to legitimately click same ad again), but requires impression lifecycle management in Redis
4. **Symmetric HMAC over asymmetric signing:** Faster and simpler for internal use; asymmetric would be needed if external parties need to verify signatures
5. **PostgreSQL for OLAP:** Simple and sufficient at current scale; at 10x scale, consider TimescaleDB or a columnar store like ClickHouse

### Alternative Approaches Worth Mentioning

- **Raw event storage (Cassandra/ClickHouse):** Enables arbitrary re-aggregation but at 100x storage cost
- **Apache Kafka instead of Kinesis:** Open-source, more control, but requires self-managed infrastructure
- **Apache Spark Streaming instead of Flink:** Similar capabilities; Flink has lower latency for stream processing
- **ClickHouse for OLAP:** Columnar storage, excellent for time-series analytics, scales better than PostgreSQL at massive scale
- **User-based deduplication:** Simpler (user X can only click ad Y once per session), but may incorrectly block legitimate repeat engagement

### Questions to Expect

- "How would you handle late-arriving click events?" (Watermarks in Flink, allowed lateness window)
- "How would you support sub-minute analytics?" (Flink session windows or sliding windows instead of tumbling)
- "What if Flink falls behind processing Kinesis?" (Increase Flink parallelism; increase Kinesis shards)
- "How would you backfill historical data if the aggregation logic changes?" (Replay from Kinesis — 7-day buffer; or store raw events if needed)
- "How do you prevent the Redis impression store from becoming a bottleneck?" (Redis cluster, shard by impression_id prefix; TTL keeps memory bounded)

---

*End of Ad/Click Aggregator System Design*
