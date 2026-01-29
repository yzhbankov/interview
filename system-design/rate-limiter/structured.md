# System Design Interview Preparation

## Session 2: Rate Limiter

---

## 1. Session Context & Goal

**Purpose:**

* Day 2 of system design interview preparation
* Practice designing a **Rate Limiter**
* Focus on requirements, scalability, and placement in a distributed system

**Problem Statement:**
Design a **rate limiter** that controls how many requests a client can make within a given time window. The system should:

* Prevent abuse
* Protect backend services
* Ensure fair usage
* Help mitigate DDoS-style traffic spikes

---

## 2. Assumptions & Scope

* Rate limiter is used for a **public API**
* Applies to requests coming through an **API Gateway**
* Supports both authenticated and unauthenticated users

---

## 3. Requirements

### 3.1 Functional Requirements

* Identify clients by:

    * User ID (authenticated)
    * API key
    * IP address (unauthenticated)
* Enforce request limits based on predefined rules
* Reject requests when limit is exceeded
* Return meaningful error responses

    * HTTP 429 (Too Many Requests)

---

### 3.2 Non-Functional Requirements

* **High availability** (preferred over strong consistency)
* **Low latency** rate-limit check (< 10 ms)
* **Scalability** up to **1M requests/sec**
* Eventual consistency for rule updates is acceptable

---

## 4. Scale & Capacity Assumptions

* ~100M daily active users
* Peak traffic: **1M requests/sec**

---

## 5. Memory Estimation (High-Level)

### Stored per Client

* Identifier (e.g., IPv6): ~16 bytes
* Timestamp / counter data: ~4–8 bytes

### Rough Estimation

* ~20 bytes per active user
* 100M users → ~2 GB memory (order-of-magnitude estimate)

**Conclusion:**

* All rate-limit state must be kept **in-memory**

---

## 6. Core Entities

### Client

* user_id / api_key / ip_address

### Rate Limit Rule

* Limit (e.g., 100 requests)
* Time window (e.g., 1 minute)
* Strategy (fixed window, sliding window, token bucket)

---

## 7. System Interface (Internal API)

### Check Request

`isRequestAllowed(client_id, endpoint_id)`

**Response:**

* allowed: boolean
* remaining_requests
* reset_timestamp

---

### Rule Management (Internal)

* Create / update rate-limit rules per endpoint
* Dynamic rule updates (eventual consistency)

---

## 8. High-Level Design (HLD)

### Components

* Client
* API Gateway
* Rate Limiter Service
* In-memory datastore (Redis)
* Backend microservices (A, B, C)

### Placement Decision

#### Option 1: Inside Each Microservice

**Pros:**

* Very low latency

**Cons:**

* Hard to synchronize
* Duplicated logic
* Operational complexity

#### Option 2: API Gateway (Chosen)

**Pros:**

* Centralized control
* Protects all services
* Easier to manage rules

---

## 9. Rate Limiting Algorithms

### 9.1 Fixed Window Counter

* Counts requests per fixed time window
* Simple implementation

**Drawback:**

* Burst issue at window boundaries

---

### 9.2 Sliding Window

* Counts requests in a moving time window
* Smooths traffic

**Tradeoff:**

* More memory & computation

---

### 9.3 Token Bucket / Leaky Bucket

* Tokens refill over time
* Allows controlled bursts

---

**Chosen for Initial Design:** Fixed Window (simplicity)

---

## 10. Data Storage Design

### Datastore Choice

* **Redis** (in-memory, low latency)

### Stored Data

* client_id → counter / bucket
* request count
* window start timestamp

### Notes

* Rate-limit rules stored in configuration or separate service
* Redis only stores **request state**, not rules

---

## 11. Request Flow

1. Client → API Gateway
2. Gateway → Rate Limiter
3. Rate Limiter checks Redis
4. If allowed → forward to backend service
5. If exceeded → return HTTP 429

---

## 12. User-Facing Response

### On Limit Exceeded

* HTTP 429 Too Many Requests
* Headers:

    * X-RateLimit-Limit
    * X-RateLimit-Remaining
    * X-RateLimit-Reset

---

## 13. Availability & Fault Tolerance

* Redis deployed as a distributed cluster
* Replication and failover enabled
* Rate limiter designed to fail-open or fail-safe (policy decision)
* Multi-region deployments for low latency

---

## 14. Scalability Considerations

* Horizontal scaling of API Gateways
* Redis sharding
* Regional rate-limit state to reduce latency
* Eventual consistency across regions

---

## 15. Summary & Reflection

* Clear understanding of rate limiter placement
* Tradeoffs between algorithms identified
* Redis chosen for low-latency state management
* Next improvement: deeper dive into token bucket & multi-region consistency

---

*End of Session 2*
