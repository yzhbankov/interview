# System Design Interview Templates

Reusable templates and component references for system design interviews.

---

## Universal Framework (45 min)

| Phase | Time | Deliverable |
|-------|------|-------------|
| **1. Requirements** | 5 min | Functional (3-5 bullet points) + Non-functional (scale, latency, availability) |
| **2. Estimations** | 5 min | DAU, QPS (read/write), storage, bandwidth |
| **3. API Design** | 5 min | 3-5 REST endpoints with request/response |
| **4. Data Model** | 5 min | Entities, relationships, DB choice with justification |
| **5. High-Level Design** | 10 min | Architecture diagram with data flow arrows |
| **6. Deep Dive** | 10 min | 1-2 critical components in detail |
| **7. Bottlenecks & Scaling** | 5 min | Failure modes, scaling strategy, trade-offs |

---

## Back-of-the-Envelope Estimation Defaults

| Metric | Default Assumption |
|--------|-------------------|
| DAU | 10M (adjust per problem) |
| Read:Write ratio | 10:1 (read-heavy) or 1:1 (write-heavy) |
| Average request size | 1 KB |
| Average media size | 1 MB (image), 50 MB (video) |
| QPS per server | ~10K (web), ~1K (DB with joins) |
| Storage per year | QPS × avg_size × 86400 × 365 |

**Latency reference:**
| Operation | Latency |
|-----------|---------|
| L1 cache | 1 ns |
| L2 cache | 10 ns |
| RAM | 100 ns |
| SSD random read | 100 μs |
| HDD seek | 10 ms |
| Same datacenter round trip | 0.5 ms |
| Cross-continent round trip | 150 ms |

**Scale reference:**
| Number | Name | Shorthand |
|--------|------|-----------|
| 10^3 | Thousand | KB |
| 10^6 | Million | MB |
| 10^9 | Billion | GB |
| 10^12 | Trillion | TB |

---

## Reusable Architecture Patterns

### Pattern 1: Read-Heavy Service
```
Client → CDN → Load Balancer → API Servers → Cache (Redis) → Database (read replicas)
```
**Use for:** URL shortener, news feed read path, product catalog
**Key decisions:** Cache invalidation strategy, replication lag tolerance

### Pattern 2: Write-Heavy / Streaming
```
Client → Load Balancer → API Servers → Message Queue (Kafka) → Stream Processors → Database + Analytics
```
**Use for:** Ad click tracking, logging, IoT ingestion, metrics collection
**Key decisions:** Partitioning strategy, exactly-once vs at-least-once, windowing

### Pattern 3: Real-Time Bidirectional
```
Client ↔ WebSocket Servers (stateful) → Presence Service + Message Queue → Storage
             ↓
       Connection Manager (tracks which user is on which server)
```
**Use for:** Chat, collaborative editing, live notifications, gaming
**Key decisions:** Connection management, message ordering, offline handling

### Pattern 4: Search / Recommendation
```
Client → API → Search Service (Elasticsearch) ← Indexing Pipeline ← Database
                     ↓
              Ranking/ML Service
```
**Use for:** Typeahead, product search, content recommendations
**Key decisions:** Index update frequency, ranking algorithm, relevance vs recency

### Pattern 5: File / Media Processing
```
Client → Upload Service → Object Storage (S3)
              ↓
       Message Queue → Processing Workers (transcode, resize, scan)
              ↓
       CDN ← Processed Storage
```
**Use for:** Instagram, YouTube, Dropbox, document processing
**Key decisions:** Chunked uploads, processing pipeline, CDN caching

---

## Component Deep Dive Notes

### Load Balancer
- **L4 (transport):** Fast, routes by IP/port (HAProxy, AWS NLB)
- **L7 (application):** Routes by URL/headers, can terminate SSL (Nginx, AWS ALB)
- **Algorithms:** Round robin, least connections, weighted, IP hash
- **Health checks:** Active (ping) vs passive (monitor errors)

### Caching
- **Cache-aside (lazy):** App checks cache first, loads from DB on miss, writes to cache
- **Write-through:** App writes to cache, cache writes to DB (consistent but slower writes)
- **Write-behind:** App writes to cache, cache async writes to DB (fast but risk of loss)
- **Eviction:** LRU (most common), LFU, TTL-based
- **Cache stampede:** Use locking, early expiry jitter, or background refresh

### Message Queue
| Feature | Kafka | RabbitMQ | SQS |
|---------|-------|----------|-----|
| Model | Log-based | Message broker | Managed queue |
| Ordering | Per partition | Per queue | Best effort (FIFO available) |
| Replay | Yes | No | No |
| Throughput | Very high | Medium | Medium |
| Best for | Event streaming, logs | Task queues, RPC | Simple async, serverless |

### Database Replication
- **Single-leader:** One writer, many readers. Simple but write bottleneck
- **Multi-leader:** Multiple writers. Good for multi-region but conflict resolution needed
- **Leaderless:** Quorum reads/writes (W + R > N). High availability, eventual consistency
- **Replication lag:** Can cause stale reads. Mitigation: read-your-writes, monotonic reads

### Sharding Strategies
- **Range-based:** Good for sequential access, bad for hotspots
- **Hash-based:** Even distribution, loses range queries
- **Consistent hashing:** Easy to add/remove nodes with minimal redistribution
- **Shard key choice:** Must consider query patterns, data distribution, growth

---

## Common System Designs — Quick Outlines

### URL Shortener
Core: Hash function → base62 encode → store mapping. Cache heavy reads. Counter or pre-generated IDs.

### Rate Limiter
Core: Token bucket or sliding window. Redis for distributed state. Lua scripts for atomicity.

### Chat System
Core: WebSockets for real-time. Message queue for delivery. Separate online/offline paths. Fanout for groups.

### News Feed
Core: Fan-out-on-write for most users, fan-out-on-read for celebrities. Timeline cache per user. Ranking service.

### Notification System
Core: Event triggers → notification service → per-channel workers (push, SMS, email). Priority queue. Dedup by idempotency key.

### Search Autocomplete
Core: Trie or prefix index. Cache top results per prefix. Async index updates. Client-side debouncing.

### File Storage (Dropbox)
Core: Chunk files, store in object storage. Metadata DB for tree structure. Delta sync. Block-level dedup.

### Distributed Cache
Core: Consistent hashing for partition. Replication for availability. LRU eviction. Gossip protocol for membership.

---

## Trade-Off Discussion Starters

Use these in the final minutes of any design interview:

| Trade-off | When to discuss |
|-----------|----------------|
| **Consistency vs Availability** (CAP) | Any distributed database choice |
| **Latency vs Throughput** | Batch processing vs real-time |
| **Push vs Pull** | Notifications, feeds, data sync |
| **SQL vs NoSQL** | Any data model decision |
| **Monolith vs Microservices** | Service boundary discussions |
| **Strong vs Eventual consistency** | Any multi-region or replicated system |
| **Sync vs Async processing** | Any write path with heavy computation |
| **Precision vs Recall** | Search, recommendations, fraud detection |
| **Simplicity vs Scalability** | When interviewer asks "what would you do differently at 100x scale" |

---

## Non-Functional Requirements Checklist

Always address these (even briefly) for every design:

- [ ] **Scalability** — How does it handle 10x growth? What's the bottleneck?
- [ ] **Availability** — Target uptime? What happens when a component fails?
- [ ] **Latency** — P50 and P99 targets? Where are the slow paths?
- [ ] **Consistency** — Strong or eventual? What are the consequences of stale data?
- [ ] **Durability** — Can you lose data? Replication factor? Backup strategy?
- [ ] **Security** — Auth, encryption at rest/in transit, rate limiting?
- [ ] **Monitoring** — Key metrics, alerting, debugging capability?
