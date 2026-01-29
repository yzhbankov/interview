# Distributed Rate Limiter System Design
## FAANG Senior Engineer Level

---

## 1. Problem Clarification & Scope

### Core Problem
Design a distributed rate limiting system that controls request throughput to protect APIs from abuse, ensure fair usage, and prevent system overload from traffic bursts or DDoS attacks.

### Clarifying Questions to Ask
- Where will the rate limiter be deployed? (API Gateway, service mesh, application layer)
- What's the client identification strategy? (IP, API key, user ID, combination)
- Single data center or globally distributed?
- Should rate limits be hard (strict reject) or soft (allow burst with degradation)?
- Do we need different limits per endpoint, user tier, or resource type?
- What's the acceptable false positive rate? (legitimate requests incorrectly rejected)
- Should blocked requests be queued or immediately rejected?

### Scope Definition

**In Scope:**
- Distributed rate limiting across multiple nodes
- Multiple rate limiting algorithms (fixed window, sliding window, token bucket)
- Sub-10ms latency for rate limit checks
- Dynamic rule configuration without restarts
- Multi-tenant support (different limits per client tier)

**Out of Scope (mention but don't design):**
- DDoS mitigation at network layer (L3/L4)
- Web Application Firewall (WAF) rules
- Request queuing/throttling (we reject, not delay)
- Billing/metering integration

---

## 2. Requirements

### Functional Requirements

| Requirement | Priority | Description |
|-------------|----------|-------------|
| Identify clients | P0 | By IP address, API key, user ID, or combination |
| Enforce rate limits | P0 | Block requests exceeding defined thresholds |
| Return proper errors | P0 | 429 status with retry-after and limit headers |
| Support multiple algorithms | P1 | Fixed window, sliding window, token bucket |
| Dynamic rule updates | P1 | Change limits without service restart |
| Per-endpoint limits | P2 | Different limits for different API endpoints |

### Non-Functional Requirements

| Requirement | Target | Rationale |
|-------------|--------|-----------|
| Availability | 99.99% | Rate limiter failure = API unprotected or blocked |
| Latency | P99 < 10ms | Must not add significant overhead to requests |
| Throughput | 1M+ checks/sec | Support high-traffic APIs |
| Consistency | Eventual (prefer availability) | Slight over-limit acceptable vs. blocking legitimate traffic |
| Accuracy | ±5% of limit | Trade-off for distributed performance |

### Failure Mode Decision
**Critical Trade-off:** What happens when rate limiter is unavailable?

| Option | Pros | Cons | Decision |
|--------|------|------|----------|
| Fail open (allow all) | Service stays available | No protection during outage | ✅ Default |
| Fail closed (block all) | Maintains protection | Service unavailable | For critical endpoints |

---

## 3. Capacity Estimation

### Traffic Assumptions
```
Daily Active Users:        100 million
Peak requests/sec:         1 million
Average requests/sec:      200,000
Rate limit checks/request: 1-3 (user + IP + endpoint)
```

### Memory Calculation

#### Per-Client Storage
```
Client identifier:    16 bytes (UUID or IPv6)
Request counter:      4 bytes (int32)
Window timestamp:     8 bytes (int64)
Bucket tokens:        4 bytes (float32)
Last refill time:     8 bytes (int64)
─────────────────────────────────
Total per client:     ~40 bytes
```

#### Total Memory (Worst Case)
```
Active clients (20% of 100M DAU):  20 million
Memory per client:                  40 bytes
Raw storage:                        800 MB
With overhead (2x for Redis):       ~1.6 GB
Per region (3 regions):             ~600 MB each
```

### Redis Cluster Sizing
```
Operations/sec:     1M checks × 2 ops (read + write) = 2M ops/sec
Redis capacity:     ~100K ops/sec per node
Nodes needed:       20+ nodes (with replication: 40+)
```

---

## 4. Rate Limiting Algorithms Deep Dive

### Algorithm Comparison

| Algorithm | Pros | Cons | Use Case |
|-----------|------|------|----------|
| **Fixed Window** | Simple, memory efficient | Boundary burst problem | Low-precision limits |
| **Sliding Window Log** | Precise | High memory (stores all timestamps) | Small-scale, audit needed |
| **Sliding Window Counter** | Good precision, efficient | Slight approximation | ✅ General purpose |
| **Token Bucket** | Allows controlled bursts | Slightly more complex | ✅ API rate limiting |
| **Leaky Bucket** | Smooth output rate | No burst allowance | Traffic shaping |

### Algorithm 1: Fixed Window Counter

```
Minute 1          │ Minute 2
──────────────────┼──────────────────
[req][req][ ][ ][ ]│[req][req][req]...
     ↑ limit=5     │     ↑ reset

Problem: 5 requests at 0:59 + 5 at 1:01 = 10 requests in 2 seconds
```

```python
def fixed_window_check(client_id: str, limit: int, window_sec: int) -> bool:
    key = f"rl:{client_id}:{current_minute()}"
    count = redis.incr(key)
    if count == 1:
        redis.expire(key, window_sec)
    return count <= limit
```

### Algorithm 2: Sliding Window Counter (Recommended)

Combines precision of sliding log with efficiency of fixed window.

```
Current window weight: 100%
Previous window weight: (overlap_time / window_size)

Example: At 1:15 with 1-minute window
- Current minute (1:00-2:00): 3 requests, weight = 0.25
- Previous minute (0:00-1:00): 7 requests, weight = 0.75
- Weighted count = (7 × 0.75) + (3 × 0.25) = 6.0 requests
```

```python
def sliding_window_check(client_id: str, limit: int, window_sec: int) -> bool:
    now = time.time()
    current_window = int(now // window_sec)
    previous_window = current_window - 1
    
    # Calculate time position within current window
    elapsed = now % window_sec
    previous_weight = 1 - (elapsed / window_sec)
    
    # Get counts from Redis
    current_key = f"rl:{client_id}:{current_window}"
    previous_key = f"rl:{client_id}:{previous_window}"
    
    current_count = int(redis.get(current_key) or 0)
    previous_count = int(redis.get(previous_key) or 0)
    
    # Weighted calculation
    weighted_count = (previous_count * previous_weight) + current_count
    
    if weighted_count >= limit:
        return False
    
    # Increment current window
    pipe = redis.pipeline()
    pipe.incr(current_key)
    pipe.expire(current_key, window_sec * 2)
    pipe.execute()
    
    return True
```

### Algorithm 3: Token Bucket (Recommended for APIs)

Allows controlled bursts while maintaining average rate.

```
Bucket: [●●●●●○○○○○]  (5 tokens, capacity 10)
         ↓
Request arrives → consume 1 token
         ↓
Bucket: [●●●●○○○○○○]  (4 tokens)
         ↓
Time passes → refill at rate R tokens/sec
         ↓
Bucket: [●●●●●●○○○○]  (6 tokens)
```

```python
def token_bucket_check(client_id: str, capacity: int, refill_rate: float) -> bool:
    """
    capacity: max tokens (burst size)
    refill_rate: tokens added per second
    """
    key = f"rl:bucket:{client_id}"
    now = time.time()
    
    # Atomic Lua script for consistency
    lua_script = """
    local key = KEYS[1]
    local capacity = tonumber(ARGV[1])
    local refill_rate = tonumber(ARGV[2])
    local now = tonumber(ARGV[3])
    
    local bucket = redis.call('HMGET', key, 'tokens', 'last_refill')
    local tokens = tonumber(bucket[1]) or capacity
    local last_refill = tonumber(bucket[2]) or now
    
    -- Refill tokens based on elapsed time
    local elapsed = now - last_refill
    local new_tokens = math.min(capacity, tokens + (elapsed * refill_rate))
    
    if new_tokens >= 1 then
        -- Consume one token
        redis.call('HMSET', key, 'tokens', new_tokens - 1, 'last_refill', now)
        redis.call('EXPIRE', key, 3600)
        return {1, new_tokens - 1}  -- allowed, remaining
    else
        return {0, 0}  -- rejected, no tokens
    end
    """
    
    result = redis.eval(lua_script, 1, key, capacity, refill_rate, now)
    return result[0] == 1
```

---

## 5. Core Entities & Data Model

### Entities

```
┌─────────────────────────────────────────────────────────┐
│ Client                                                  │
├─────────────────────────────────────────────────────────┤
│ identifier: string    (IP | user_id | api_key)         │
│ tier: enum           (free | pro | enterprise)          │
│ custom_limits: map   (endpoint → limit override)        │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│ RateLimitRule                                           │
├─────────────────────────────────────────────────────────┤
│ id: string                                              │
│ endpoint_pattern: string  ("/api/v1/*")                 │
│ client_tier: enum                                       │
│ algorithm: enum          (token_bucket | sliding_window)│
│ limit: int               (requests allowed)             │
│ window_seconds: int      (time window)                  │
│ burst_capacity: int      (for token bucket)             │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│ RateLimitState (stored in Redis)                        │
├─────────────────────────────────────────────────────────┤
│ key: string              (client_id:endpoint:window)    │
│ count: int               (current request count)        │
│ tokens: float            (remaining tokens)             │
│ last_update: timestamp                                  │
│ ttl: int                 (auto-expire)                  │
└─────────────────────────────────────────────────────────┘
```

### Redis Key Patterns
```
# Sliding window counter
rl:sw:{client_id}:{endpoint}:{window_id}  →  count (int)

# Token bucket
rl:tb:{client_id}:{endpoint}  →  {tokens: float, last_refill: timestamp}

# Client metadata (cached from config DB)
rl:client:{client_id}  →  {tier: string, limits: json}
```

---

## 6. API Design

### Internal Rate Limit Check (gRPC preferred for low latency)

```protobuf
service RateLimiter {
  rpc CheckRateLimit(RateLimitRequest) returns (RateLimitResponse);
  rpc ReportUsage(UsageReport) returns (Empty);  // async batch reporting
}

message RateLimitRequest {
  string client_id = 1;
  string endpoint = 2;
  int32 cost = 3;  // weighted requests (default: 1)
}

message RateLimitResponse {
  bool allowed = 1;
  int32 remaining = 2;
  int64 reset_at = 3;  // Unix timestamp
  int32 retry_after_ms = 4;  // if rejected
}
```

### Admin API (REST for rule management)

```http
# Get current rules
GET /api/v1/rules
Response: { rules: [...] }

# Update rule
PUT /api/v1/rules/{rule_id}
{
  "endpoint_pattern": "/api/v1/expensive/*",
  "limit": 10,
  "window_seconds": 60
}
Response 200: { rule: {...}, propagation_time_ms: 150 }

# Get client's current state
GET /api/v1/clients/{client_id}/state
Response: {
  "client_id": "user_123",
  "limits": {
    "/api/v1/*": { "remaining": 85, "reset_at": 1705312800 }
  }
}
```

### HTTP Response Headers (RFC 6585)
```http
HTTP/1.1 429 Too Many Requests
Content-Type: application/json
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1705312800
Retry-After: 30

{
  "error": "rate_limit_exceeded",
  "message": "Too many requests. Please retry after 30 seconds.",
  "retry_after": 30
}
```

---

## 7. High-Level Architecture

### Deployment Options Analysis

| Placement | Latency | Accuracy | Complexity | Recommendation |
|-----------|---------|----------|------------|----------------|
| In API Gateway | ✅ Lowest | ⚠️ Per-gateway | ✅ Simple | ✅ Primary |
| Sidecar (service mesh) | ✅ Low | ⚠️ Per-pod | ⚠️ Medium | Envoy/Istio |
| Centralized service | ⚠️ +5-10ms | ✅ Global accurate | ⚠️ Medium | Complex rules |
| In application | ⚠️ Varies | ❌ Per-instance | ❌ Scattered | Not recommended |

### Architecture Diagram

```
                                    ┌─────────────────────────────────────────────┐
                                    │           Config Service (K8s ConfigMap)   │
                                    │     Rules, tier definitions, overrides     │
                                    └─────────────────────┬───────────────────────┘
                                                          │ push on change
                                    ┌─────────────────────▼───────────────────────┐
                                    │              Rules Cache (Local)            │
                                    │         In-memory, refresh every 30s       │
                                    └─────────────────────┬───────────────────────┘
                                                          │
┌──────────┐              ┌─────────────────────────────────────────────────────────────────┐
│  Client  │──────────────│                        API Gateway                              │
└──────────┘              │  ┌─────────────────────────────────────────────────────────┐   │
                          │  │                  Rate Limiter Module                     │   │
                          │  │  1. Extract client ID (IP/API key/JWT)                  │   │
                          │  │  2. Match endpoint to rule                              │   │
                          │  │  3. Check Redis for current state                       │   │
                          │  │  4. Allow/Reject decision                               │   │
                          │  └─────────────────────────┬───────────────────────────────┘   │
                          └────────────────────────────┼───────────────────────────────────┘
                                                       │
                          ┌────────────────────────────┼────────────────────────────┐
                          │                            │                            │
                ┌─────────▼─────────┐        ┌────────▼────────┐        ┌─────────▼─────────┐
                │   Redis Primary   │◄──────►│  Redis Replica  │◄──────►│   Redis Replica   │
                │   (writes)        │  sync  │  (reads)        │  sync  │   (reads)         │
                └───────────────────┘        └─────────────────┘        └───────────────────┘
                          │
                          │ async replication
                          ▼
                ┌───────────────────────────────────────────────────────────────────┐
                │                    Redis Cluster (Other Regions)                  │
                │              Eventual consistency for global rate limits          │
                └───────────────────────────────────────────────────────────────────┘
```

### Request Flow

```
1. Request arrives at API Gateway
2. Extract client identifier:
   - Check Authorization header → user_id/api_key
   - Fallback to X-Forwarded-For → IP address
3. Lookup applicable rules (from local cache):
   - Match endpoint pattern
   - Apply client tier multipliers
4. Execute rate limit check (Redis):
   - Run Lua script atomically
   - Return allowed/rejected + metadata
5. If allowed:
   - Forward to upstream service
   - Add rate limit headers to response
6. If rejected:
   - Return 429 immediately
   - Include Retry-After header
```

---

## 8. Deep Dive: Distributed Synchronization

### The Distributed Rate Limiting Problem

With multiple API gateway instances, each checking Redis independently, we face:
- **Race conditions:** Two requests check simultaneously, both see "under limit"
- **Clock skew:** Different servers have slightly different time
- **Network partitions:** Redis unreachable from some nodes

### Solution 1: Atomic Lua Scripts (Recommended)

All rate limit logic executes atomically in Redis.

```lua
-- Sliding window counter with atomic check-and-increment
local key = KEYS[1]
local limit = tonumber(ARGV[1])
local window = tonumber(ARGV[2])
local now = tonumber(ARGV[3])

local current_window = math.floor(now / window)
local previous_window = current_window - 1
local elapsed_ratio = (now % window) / window

local current_key = key .. ":" .. current_window
local previous_key = key .. ":" .. previous_window

local current_count = tonumber(redis.call('GET', current_key) or 0)
local previous_count = tonumber(redis.call('GET', previous_key) or 0)

local weighted_count = (previous_count * (1 - elapsed_ratio)) + current_count

if weighted_count >= limit then
    local reset_time = (current_window + 1) * window
    return {0, math.ceil(weighted_count), reset_time - now}
end

redis.call('INCR', current_key)
redis.call('EXPIRE', current_key, window * 2)

return {1, limit - math.ceil(weighted_count) - 1, 0}
```

### Solution 2: Local + Global Rate Limiting

For extreme scale, combine local and global limits.

```
Global limit: 1000 req/min (enforced via Redis)
Local limit: 100 req/min per gateway instance (in-memory)

Request must pass BOTH limits.
Reduces Redis calls by 90%+ for high-volume clients.
```

```python
class HybridRateLimiter:
    def __init__(self):
        self.local_counters = {}  # client_id → {count, window}
        self.local_limit_ratio = 0.1  # 10% of global limit per instance
    
    def check(self, client_id: str, global_limit: int) -> bool:
        # Fast path: check local limit first
        local_limit = int(global_limit * self.local_limit_ratio)
        if not self._check_local(client_id, local_limit):
            return False
        
        # Slow path: check global Redis limit
        return self._check_redis(client_id, global_limit)
```

### Solution 3: Gossip Protocol for Cross-Region

For global rate limits across regions with eventual consistency.

```
Region A: 300 requests counted
Region B: 250 requests counted
Region C: 200 requests counted
─────────────────────────────────
Global estimate: 750 requests

Gossip interval: 1 second
Consistency: ±10% accuracy acceptable
```

---

## 9. Deep Dive: Handling Edge Cases

### Edge Case 1: Clock Skew
```python
# Use Redis server time, not client time
now = redis.time()[0]  # Returns server timestamp
```

### Edge Case 2: Redis Failure
```python
class ResilientRateLimiter:
    def check(self, client_id: str) -> bool:
        try:
            return self._redis_check(client_id)
        except RedisError:
            # Fail open: allow request but log
            metrics.increment("rate_limiter.redis_failure")
            
            # Optional: use local fallback
            return self._local_fallback_check(client_id)
```

### Edge Case 3: Hot Keys (Viral API Key)
```python
# Problem: Single popular API key creates Redis hot spot

# Solution: Key sharding
def get_redis_key(client_id: str, num_shards: int = 10) -> str:
    shard = hash(client_id + str(random.randint(0, num_shards-1))) % num_shards
    return f"rl:{client_id}:{shard}"

# Aggregate counts across shards periodically
```

### Edge Case 4: Distributed Denial of Wallet
```python
# Attacker uses many IPs to exhaust rate limit budget

# Solution: Hierarchical limits
limits = [
    {"scope": "ip", "limit": 100, "window": 60},
    {"scope": "api_key", "limit": 1000, "window": 60},
    {"scope": "global", "limit": 100000, "window": 60},  # System-wide
]

# Must pass ALL applicable limits
```

---

## 10. Configuration & Rule Management

### Rule Schema
```yaml
rate_limits:
  - name: "default_api"
    endpoint: "/api/v1/*"
    algorithm: "sliding_window"
    tiers:
      free:
        limit: 100
        window_seconds: 60
      pro:
        limit: 1000
        window_seconds: 60
      enterprise:
        limit: 10000
        window_seconds: 60
    
  - name: "expensive_endpoint"
    endpoint: "/api/v1/ml/inference"
    algorithm: "token_bucket"
    tiers:
      free:
        capacity: 10
        refill_rate: 0.1  # 6 per minute
      pro:
        capacity: 100
        refill_rate: 1.0  # 60 per minute
```

### Dynamic Rule Updates (No Restart)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Admin API   │────►│  Config DB   │────►│   Pub/Sub    │
└──────────────┘     └──────────────┘     └──────┬───────┘
                                                  │
                     ┌────────────────────────────┼────────────────────────────┐
                     │                            │                            │
           ┌─────────▼─────────┐        ┌────────▼────────┐        ┌─────────▼─────────┐
           │   Gateway Pod 1   │        │   Gateway Pod 2  │        │   Gateway Pod 3   │
           │  (rules cache)    │        │  (rules cache)   │        │  (rules cache)    │
           └───────────────────┘        └──────────────────┘        └───────────────────┘

Propagation time: < 1 second
```

---

## 11. Monitoring & Observability

### Key Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `rate_limiter.check.latency_p99` | Time to check limit | > 10ms |
| `rate_limiter.requests.allowed` | Requests passed | - |
| `rate_limiter.requests.rejected` | Requests blocked | Spike detection |
| `rate_limiter.redis.errors` | Redis failures | > 0.1% |
| `rate_limiter.rules.version` | Config version | Mismatch across pods |

### Dashboard Panels
1. **Request Rate:** Allowed vs Rejected over time
2. **Top Blocked Clients:** Identify abuse or misconfiguration
3. **Latency Percentiles:** P50, P95, P99 for rate limit checks
4. **Redis Health:** Connection pool, memory, ops/sec

### Alerting Rules
```yaml
alerts:
  - name: RateLimiterHighRejectRate
    expr: rate(rate_limiter_rejected_total[5m]) / rate(rate_limiter_total[5m]) > 0.1
    severity: warning
    
  - name: RateLimiterLatencyHigh
    expr: histogram_quantile(0.99, rate_limiter_latency_bucket) > 0.01
    severity: critical
```

---

## 12. Availability & Fault Tolerance

### Failure Scenarios

| Failure | Impact | Mitigation | Recovery |
|---------|--------|------------|----------|
| Redis primary down | Writes fail | Replica promotion (auto) | < 30s |
| All Redis down | No rate limiting | Fail open + alert | Manual |
| Config service down | No rule updates | Local cache continues | Degraded |
| Single gateway down | Partial capacity | LB health checks | < 10s |
| Network partition | Split brain | Eventual consistency | Self-healing |

### Graceful Degradation Modes
```python
class DegradationLevel(Enum):
    NORMAL = 1      # Full Redis-backed rate limiting
    LOCAL_ONLY = 2  # In-memory per-instance limits
    FAIL_OPEN = 3   # Allow all, log for post-analysis
    EMERGENCY = 4   # Block all except allowlist
```

---

## 13. Summary: Requirements Mapping

| Requirement | Solution | Component |
|-------------|----------|-----------|
| Low latency (<10ms) | Atomic Lua scripts, local caching | Redis + Gateway |
| High throughput (1M/sec) | Redis Cluster, sharding | Distributed Redis |
| Availability (99.99%) | Fail-open mode, replicas | Resilient design |
| Accuracy (±5%) | Sliding window algorithm | Algorithm choice |
| Dynamic rules | Pub/Sub propagation | Config service |
| Multi-tenant | Tier-based limits | Rule schema |

---

## 14. Interview Discussion Points

### Trade-offs Made
1. **Availability over consistency:** Allow slight over-limit vs blocking legitimate traffic
2. **Sliding window over fixed:** Better accuracy with minimal complexity increase
3. **Redis over custom solution:** Proven, but adds dependency
4. **Gateway placement over centralized:** Lower latency, but distributed state

### Alternative Approaches Worth Mentioning
- **Service mesh integration:** Envoy/Istio native rate limiting
- **Cell-based architecture:** Independent rate limiters per cell for isolation
- **Client-side rate limiting:** SDKs with local token buckets
- **Machine learning:** Adaptive limits based on traffic patterns

### Questions to Expect
- "How do you handle a client trying to game the system with rotating IPs?" (Hierarchical limits, API key enforcement)
- "What if Redis becomes a bottleneck?" (Local caching, Redis Cluster, key sharding)
- "How would you rate limit WebSocket connections?" (Connection-based vs message-based limits)
- "How do you test rate limiting in production?" (Shadow mode, canary rollouts)
