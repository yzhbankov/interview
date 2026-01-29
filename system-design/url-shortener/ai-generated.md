# URL Shortener System Design
## FAANG Senior Engineer Level

---

## 1. Problem Clarification & Scope

### Core Problem
Design a globally distributed URL shortening service (similar to bit.ly, TinyURL) that generates short aliases for long URLs and handles high-throughput redirections.

### Clarifying Questions to Ask
- What's the expected scale? (Users, URLs/day, read/write ratio)
- Do we need analytics (click tracking, geolocation, referrer)?
- Should URLs be customizable (vanity URLs)?
- Authentication required or anonymous usage?
- What's the acceptable latency for redirects? (<100ms?)
- Geographic distribution requirements?
- Compliance requirements (GDPR, data retention)?

### Scope Definition

**In Scope:**
- Short URL generation with collision-free guarantees
- Low-latency redirection (P99 < 50ms)
- URL expiration and deletion
- Multi-region deployment
- Rate limiting and abuse prevention

**Out of Scope (mention but don't design):**
- Analytics dashboard
- Custom vanity URLs
- QR code generation
- Link preview/metadata extraction

---

## 2. Requirements

### Functional Requirements
| Requirement | Priority | Notes |
|-------------|----------|-------|
| Generate unique short URL | P0 | Must guarantee uniqueness globally |
| Redirect to original URL | P0 | HTTP 301/302 with <50ms P99 |
| URL expiration | P1 | TTL-based, configurable per URL |
| Delete URL | P1 | Immediate removal from system |
| Update URL | P2 | Change destination, not short code |

### Non-Functional Requirements
| Requirement | Target | Rationale |
|-------------|--------|-----------|
| Availability | 99.99% (4 nines) | ~52 min downtime/year; redirects are critical path |
| Latency | P50 <10ms, P99 <50ms | User experience, SEO impact |
| Throughput | 100K redirects/sec | Handle viral content spikes |
| Consistency | Eventual (reads), Strong (writes) | Trade-off for availability |
| Durability | 99.999999999% (11 nines) | URLs must not be lost |

### Security Requirements
- Non-enumerable/unpredictable short codes
- Rate limiting per IP/API key
- Malware/phishing URL detection
- No PII in URL paths

---

## 3. Capacity Estimation

### Traffic Assumptions
```
Daily Active Users:     100M
New URLs/month:         200M (~77/sec)
Read:Write Ratio:       100:1
Redirects/sec:          ~7,700 (avg), ~50,000 (peak)
URL Retention:          5 years
```

### Storage Calculation
```
Records over 5 years:   200M × 12 × 5 = 12 billion URLs
Bytes per record:       ~500 bytes (with indexes)
Total storage:          12B × 500B = 6 TB raw
With replication (3x):  ~18 TB
With indexes:           ~25 TB total
```

### Bandwidth
```
Write bandwidth:   77 req/s × 500B = ~40 KB/s
Read bandwidth:    7,700 req/s × 500B = ~4 MB/s
Peak (10x):        ~40 MB/s
```

### Cache Sizing (80/20 Rule)
```
Daily unique redirects: ~665M requests
Hot URLs (20%):         ~133M unique URLs
Cache size:             133M × 500B = ~67 GB
Per region (5 regions): ~15 GB per region
```

### Short Code Length Analysis
```
Base62 characters:  [a-zA-Z0-9] = 62 chars
6 chars:            62^6 = 56.8 billion combinations
7 chars:            62^7 = 3.5 trillion combinations

With 12B URLs over 5 years → 6 characters sufficient
Recommend 7 chars for 10+ year runway
```

---

## 4. API Design

### RESTful API with Versioning

#### Create Short URL
```http
POST /api/v1/urls
Authorization: Bearer <token>
X-Request-ID: <uuid>
Content-Type: application/json

{
  "long_url": "https://example.com/very/long/path",
  "expiry_at": "2025-12-31T23:59:59Z",  // optional
  "custom_alias": "my-link"              // optional, P2
}

Response 201:
{
  "short_url": "https://short.ly/Ab3xK9z",
  "short_code": "Ab3xK9z",
  "long_url": "https://example.com/very/long/path",
  "created_at": "2024-01-15T10:30:00Z",
  "expiry_at": "2025-12-31T23:59:59Z"
}
```

#### Redirect (Optimized Path)
```http
GET /{short_code}
Response 301/302:
Location: https://example.com/very/long/path
Cache-Control: private, max-age=90
```

**Design Decision:** Use 301 (permanent) for SEO, 302 (temporary) if analytics needed. 302 preferred for flexibility.

#### Delete URL
```http
DELETE /api/v1/urls/{short_code}
Authorization: Bearer <token>
Response 204: No Content
```

#### Get URL Metadata
```http
GET /api/v1/urls/{short_code}/info
Response 200:
{
  "short_code": "Ab3xK9z",
  "long_url": "...",
  "created_at": "...",
  "expiry_at": "...",
  "click_count": 15420  // if analytics enabled
}
```

### Rate Limiting Strategy
```
Anonymous:      100 req/hour (by IP)
Authenticated:  1000 req/hour (by API key)
Enterprise:     Custom limits

Response 429:
{
  "error": "rate_limit_exceeded",
  "retry_after": 3600
}
```

---

## 5. Data Model

### Primary Schema (URL Mapping)
```sql
urls {
  id:           BIGINT PRIMARY KEY    -- internal, for sharding
  short_code:   VARCHAR(10) UNIQUE    -- indexed, Base62 encoded
  long_url:     VARCHAR(2048)         -- max URL length
  user_id:      UUID                  -- nullable for anonymous
  created_at:   TIMESTAMP
  expiry_at:    TIMESTAMP             -- nullable, indexed for TTL
  is_active:    BOOLEAN DEFAULT true  -- soft delete
}

-- Indexes
CREATE INDEX idx_short_code ON urls(short_code);
CREATE INDEX idx_expiry ON urls(expiry_at) WHERE is_active = true;
CREATE INDEX idx_user_urls ON urls(user_id, created_at DESC);
```

### Database Selection: Trade-off Analysis

| Option | Pros | Cons | Verdict |
|--------|------|------|---------|
| **DynamoDB** | Auto-scaling, managed, single-digit ms latency | Expensive at scale, vendor lock-in | ✅ Recommended |
| Cassandra | Write-optimized, proven at scale | Operational complexity | Good alternative |
| PostgreSQL + Citus | ACID, familiar SQL | Harder to scale writes | OK for smaller scale |
| MongoDB | Flexible schema | Less predictable latency | Not ideal |

**Recommendation:** DynamoDB with short_code as partition key, or Cassandra with consistent hashing.

---

## 6. High-Level Architecture

```
                                    ┌─────────────────────────────────────────┐
                                    │              CDN (CloudFront)           │
                                    │         Cache redirects at edge         │
                                    └─────────────────┬───────────────────────┘
                                                      │
                                    ┌─────────────────▼───────────────────────┐
                                    │           Global Load Balancer          │
                                    │      (Route53 / GeoDNS / Anycast)       │
                                    └─────────────────┬───────────────────────┘
                                                      │
                    ┌─────────────────────────────────┼─────────────────────────────────┐
                    │                                 │                                 │
          ┌─────────▼─────────┐             ┌────────▼────────┐             ┌─────────▼─────────┐
          │   Region: US-East │             │  Region: EU     │             │  Region: AP      │
          └─────────┬─────────┘             └────────┬────────┘             └─────────┬─────────┘
                    │                                │                                 │
          ┌─────────▼─────────┐             ┌────────▼────────┐             ┌─────────▼─────────┐
          │   Rate Limiter    │             │   Rate Limiter  │             │   Rate Limiter    │
          │   (Redis/Token)   │             │                 │             │                   │
          └─────────┬─────────┘             └────────┬────────┘             └─────────┬─────────┘
                    │                                │                                 │
          ┌─────────▼─────────┐             ┌────────▼────────┐             ┌─────────▼─────────┐
          │   App Servers     │             │   App Servers   │             │   App Servers     │
          │   (K8s Pods)      │             │   (K8s Pods)    │             │   (K8s Pods)      │
          └─────────┬─────────┘             └────────┬────────┘             └─────────┬─────────┘
                    │                                │                                 │
          ┌─────────▼─────────┐             ┌────────▼────────┐             ┌─────────▼─────────┐
          │   Redis Cluster   │◄────────────►   Redis Cluster │◄────────────►   Redis Cluster  │
          │   (Regional)      │  replicate  │   (Regional)    │  replicate  │   (Regional)     │
          └─────────┬─────────┘             └────────┬────────┘             └─────────┬─────────┘
                    │                                │                                 │
                    └────────────────────────────────┼─────────────────────────────────┘
                                                     │
                                    ┌────────────────▼────────────────┐
                                    │     DynamoDB Global Tables      │
                                    │   (Multi-region replication)    │
                                    └────────────────┬────────────────┘
                                                     │
                                    ┌────────────────▼────────────────┐
                                    │      ID Generation Service      │
                                    │   (Snowflake / ULID / Zookeeper)│
                                    └─────────────────────────────────┘
```

### Component Responsibilities

| Component | Purpose | Technology |
|-----------|---------|------------|
| CDN | Cache redirects at edge, reduce origin load | CloudFront, Fastly |
| Global LB | Geographic routing, health checks | Route53, Anycast |
| Rate Limiter | Abuse prevention, fair usage | Redis + Token Bucket |
| App Servers | Business logic, stateless | Go/Rust for performance |
| Cache | Hot URL storage, reduce DB load | Redis Cluster |
| Database | Persistent URL storage | DynamoDB Global Tables |
| ID Generator | Collision-free unique IDs | Snowflake variant |

---

## 7. Deep Dive: ID Generation & Encoding

### The Problem
Generate globally unique, non-sequential, URL-safe short codes at 77+ writes/sec across multiple regions without coordination overhead.

### Option Analysis

| Approach | Pros | Cons | Collision Risk |
|----------|------|------|----------------|
| UUID + Hash | Simple, no coordination | Long output, hash collisions | Medium |
| Auto-increment | Simple | Sequential (security), single point | None |
| **Snowflake ID** | Distributed, sortable, no coordination | 64-bit, needs encoding | None ✅ |
| Random + Check | Unpredictable | DB round-trip, race conditions | High |
| Pre-generated Pool | Fast, no coordination | Pool management complexity | None |

### Recommended: Snowflake-style ID + Base62 Encoding

#### Snowflake ID Structure (64 bits)
```
┌─────────────────────────────────────────────────────────────────┐
│  1 bit  │      41 bits       │   10 bits   │     12 bits       │
│  sign   │    timestamp       │  machine ID │   sequence num    │
│   (0)   │   (ms since epoch) │  (1024 max) │   (4096/ms max)   │
└─────────────────────────────────────────────────────────────────┘

Capacity: 4096 IDs/ms × 1024 machines = 4M IDs/sec
```

#### Base62 Encoding
```python
CHARSET = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"

def encode_base62(num: int) -> str:
    if num == 0:
        return CHARSET[0]
    result = []
    while num:
        num, remainder = divmod(num, 62)
        result.append(CHARSET[remainder])
    return ''.join(reversed(result))

def decode_base62(s: str) -> int:
    num = 0
    for char in s:
        num = num * 62 + CHARSET.index(char)
    return num

# Example:
# Snowflake ID: 7159734958364737537
# Base62:       "aB9xK2mZ" (8 chars)
```

#### Why Not Base58?
Base58 excludes `0, O, I, l` for readability. Trade-off:
- Base58: 7 chars = 2.5T combinations (slightly more readable)
- Base62: 7 chars = 3.5T combinations (more compact)

**Decision:** Use Base62 for capacity; URLs are typically copy-pasted, not typed.

---

## 8. Deep Dive: Caching Strategy

### Multi-Layer Cache Architecture

```
Request → CDN Cache (TTL: 5min) → Regional Redis (TTL: 1hr) → Database
              ↓ miss                    ↓ miss                  ↓
           forward                   forward               authoritative
```

### Cache Policies

| Layer | TTL | Eviction | Hit Rate Target |
|-------|-----|----------|-----------------|
| CDN | 5 min | Time-based | 60-70% |
| Redis | 1 hour | LRU | 95%+ |

### Cache Invalidation
```python
async def delete_url(short_code: str):
    # 1. Mark inactive in DB (soft delete)
    await db.update(short_code, is_active=False)
    
    # 2. Invalidate regional cache
    await redis.delete(f"url:{short_code}")
    
    # 3. Publish invalidation event (fan-out to other regions)
    await pubsub.publish("cache_invalidate", short_code)
    
    # 4. CDN invalidation (async, eventual)
    await cdn.invalidate_path(f"/{short_code}")
```

### Thundering Herd Prevention
```python
async def get_url(short_code: str) -> str:
    # Try cache first
    cached = await redis.get(f"url:{short_code}")
    if cached:
        return cached
    
    # Acquire distributed lock to prevent thundering herd
    lock = await redis.set(f"lock:{short_code}", "1", nx=True, ex=5)
    if not lock:
        # Another request is fetching, wait and retry
        await asyncio.sleep(0.1)
        return await get_url(short_code)
    
    try:
        # Fetch from DB
        url = await db.get(short_code)
        if url:
            await redis.setex(f"url:{short_code}", 3600, url)
        return url
    finally:
        await redis.delete(f"lock:{short_code}")
```

---

## 9. Deep Dive: Database Sharding

### Sharding Strategy
**Partition Key:** `short_code` (hash-based distribution)

```
short_code: "aB9xK2m"
           ↓ hash
shard_id = hash("aB9xK2m") % num_shards
           ↓
Shard 7 of 16
```

### Why Not Shard by User ID?
- Redirects don't have user context
- Would require additional lookup
- Hot users would create hot shards

### Consistent Hashing for Scaling
```
Virtual nodes per physical shard: 150
Adding new shard: ~1/N data migration
Removing shard: redistribute to neighbors
```

### Read Replicas Configuration
```
Write path:  App → Primary (single region) → Async replication
Read path:   App → Nearest replica (any region)

Replication lag target: <100ms
Conflict resolution: Last-write-wins (timestamp)
```

---

## 10. Availability & Failure Handling

### Failure Scenarios & Mitigations

| Failure | Impact | Mitigation | RTO |
|---------|--------|------------|-----|
| Single app server | None | K8s auto-restart, LB health checks | <30s |
| Redis node | Degraded latency | Redis Cluster failover, read from replica | <10s |
| Entire region | Partial outage | GeoDNS failover to healthy region | <60s |
| Database primary | Write unavailable | Promote replica, DynamoDB auto-failover | <30s |
| ID generator | Can't create URLs | Multiple Snowflake workers, local fallback | <5s |

### Health Check Endpoints
```http
GET /health/live    → 200 (process alive)
GET /health/ready   → 200 (can serve traffic)
GET /health/deep    → 200 (all dependencies OK)
```

### Circuit Breaker Pattern
```python
@circuit_breaker(failure_threshold=5, recovery_timeout=30)
async def get_from_database(short_code: str):
    return await db.get(short_code)

# On repeated failures:
# - Circuit opens, fast-fail for 30s
# - Serve from cache only (degraded mode)
# - Alert on-call engineer
```

---

## 11. Monitoring & Observability

### Key Metrics (SLIs)

| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Redirect latency P99 | <50ms | >100ms for 5min |
| Availability | 99.99% | <99.9% over 1hr |
| Cache hit rate | >95% | <90% for 10min |
| Error rate (5xx) | <0.01% | >0.1% for 5min |
| URL creation success | >99.9% | <99% for 5min |

### Distributed Tracing
```
Request ID flows through:
CDN → LB → App → Cache → DB
          └── each hop logged with timing
```

### Dashboards
- Real-time: Request rate, latency percentiles, error rate
- Capacity: Storage growth, cache utilization, DB connections
- Business: URLs created, redirects/day, top URLs

---

## 12. Security Considerations

### URL Validation
```python
def validate_url(url: str) -> bool:
    # 1. Parse and validate structure
    parsed = urlparse(url)
    if parsed.scheme not in ('http', 'https'):
        return False
    
    # 2. Check against blocklist (malware, phishing)
    if is_blocklisted(parsed.netloc):
        return False
    
    # 3. Optional: Safe Browsing API check
    if not safe_browsing_check(url):
        return False
    
    return True
```

### Abuse Prevention
- Rate limiting by IP and API key
- CAPTCHA for anonymous high-volume creation
- URL scanning with Google Safe Browsing API
- Report mechanism for malicious URLs
- Honeypot short codes to detect enumeration

---

## 13. Cost Estimation (AWS)

### Monthly Cost Breakdown (at scale)
```
DynamoDB (25 TB, 10K WCU, 50K RCU):     $15,000
ElastiCache Redis (3 regions × r6g.xl): $3,000
EC2/EKS (50 instances across regions):  $8,000
CloudFront (500 TB transfer):           $40,000
Route53 + DNS:                          $500
Monitoring (CloudWatch, Datadog):       $2,000
───────────────────────────────────────────────
Total:                                  ~$70,000/month
```

---

## 14. Summary: Requirements Mapping

| Requirement | Solution | Component |
|-------------|----------|-----------|
| High Availability (99.99%) | Multi-region, auto-failover | Global Tables, GeoDNS |
| Low Latency (<50ms P99) | Edge caching, regional Redis | CDN, ElastiCache |
| Scalability (100K+ rps) | Horizontal scaling, sharding | K8s, DynamoDB |
| Durability (11 nines) | Cross-region replication | DynamoDB Global Tables |
| Security (unpredictable) | Snowflake IDs, not sequential | ID Generator |
| Readability | Base62 encoding | Encoding service |

---

## 15. Interview Discussion Points

### Trade-offs Made
1. **Eventual consistency** for reads (faster) vs strong consistency (correct)
2. **Base62** over Base58 (capacity vs readability)
3. **NoSQL** over SQL (scale vs ACID guarantees)
4. **CDN caching** (speed vs freshness for deleted URLs)

### Alternative Approaches Worth Mentioning
- Bloom filter for existence checks before DB lookup
- Pre-generated ID pools for even faster writes
- Separate read/write services (CQRS) at extreme scale
- Lambda@Edge for redirect logic (serverless at edge)

### Questions to Expect
- "How would you handle a URL that goes viral?" (CDN, auto-scaling)
- "What if we need click analytics?" (Kafka stream, separate analytics DB)
- "How to prevent spam/abuse?" (Rate limiting, ML-based detection)
- "What happens during region failover?" (Walk through RTO/RPO)
