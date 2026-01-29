# System Design Interview Preparation

## Session 1: URL Shortener

---

## 1. Session Context & Goals

**Purpose:**

* First system design interview practice session
* Learn a clear structure for answering system design questions
* Understand difficulty, timing, and areas to focus on
* Track progress over time

**Task:** Design a **URL Shortener** system

**Time Allocation (Target):**

* Requirements: ~5 min
* Estimations: ~5 min
* API Design: ~5 min
* Data Model: ~10 min
* High-Level Design (HLD): ~10 min
* Deep Dive: ~10 min
* Scaling & Availability: ~5 min

---

## 2. Problem Statement

Build a system that:

* Converts long URLs into short URLs
* Redirects short URLs back to original URLs

---

## 3. Requirements

### 3.1 Functional Requirements

* Generate a unique short URL for a given long URL
* Redirect short URL to the original URL
* Delete a short URL
* Update a short URL
* Support expiration time for URLs

### 3.2 Non-Functional Requirements

* **High availability** (downtime causes broken redirects)
* **Low latency** for redirection
* **Readability & typeability** (easy to read/type URLs)
* **Security / Unpredictability** (no sequential or guessable URLs)

---

## 4. Capacity & Resource Estimations

### Assumptions

* URLs stored for up to **5 years**
* **100M daily active users**
* **200M new short URLs per month**
* Average storage per URL record: **500 bytes**
* Read-heavy workload (redirects >> writes)

### Storage Estimation

* 200M URLs/month × 12 months × 5 years ≈ **12B records**
* Total storage ≈ **~6 TB**

### Traffic Estimation

* URL creation: ~77 requests/sec
* Redirects: ~100× writes ≈ **7,700 redirects/sec**

### Bandwidth Estimation

* Writes: ~37 KB/sec
* Redirects: ~3–4 MB/sec

### Cache / Memory Estimation

* Assume **80/20 rule** (20% URLs handle 80% traffic)
* Cache ~20% of hot URLs
* Memory needed ≈ **~67 GB RAM**

---

## 5. API Design (REST)

### Create Short URL

`POST /shorten`

**Request Body:**

* original_url
* expiry_date

**Headers:**

* api_key (user identity)

---

### Update Short URL

`PUT /shorten/{short_url}`

---

### Delete Short URL

`DELETE /shorten/{short_url}`

---

### Redirect

`GET /{short_url}`

* Returns HTTP 302 redirect

---

## 6. Data Model

### URL Mapping Entity

* id (internal numeric ID)
* short_url
* original_url
* user_id
* expiry_date

### Notes

* User metadata stored in a separate collection/table
* Optional audit fields (created_at, updated_at) if needed

---

## 7. High-Level Design (HLD)

### Main Components

* Client
* Load Balancer
* Web Servers
* Application Servers
* Rate Limiter
* Cache (Redis / Memcached)
* Database (NoSQL)

### Request Flow (Redirect)

1. Client → Load Balancer
2. Load Balancer → Web Server
3. Rate limiter check
4. App server checks cache
5. Cache miss → DB lookup
6. Cache update
7. Redirect response

---

## 8. Deep Dive: URL Encoding Strategy

### Goals

* Short
* Readable
* Non-confusing
* Unpredictable

### Approach

* Generate random **Base-10 numeric ID**
* Convert ID to **Base58** (avoids confusing characters like 0/O, I/l)

### Encoding

* Base-10 → Base-58 conversion

### Decoding

* Base-58 → Base-10
* Lookup by numeric ID in DB

---

## 9. Database Choice & Scaling

### Database

* **NoSQL (e.g., MongoDB)**
* Supports horizontal scaling

### Scaling Strategies

* Horizontal sharding
* Consistent hashing
* Read replicas for heavy read traffic

---

## 10. Availability & Reliability

* Multi-region deployment
* Load balancing
* Database backups
* Failover support
* Rate limiting for protection

---

## 11. How Requirements Are Met

| Requirement  | Solution          |
| ------------ | ----------------- |
| Availability | Multi-region + LB |
| Low latency  | Cache layer       |
| Readability  | Base58 encoding   |
| Security     | Randomized IDs    |
| Scalability  | Sharded NoSQL DB  |

---

## 12. Summary & Reflection

* Covered full system design flow
* Need to improve time management
* Good first iteration
* Next focus: clearer estimations, cleaner HLD, deeper scaling discussion

---

*End of Session 1*
