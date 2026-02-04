# System Design Interview Preparation

## Session 3: Web Crawler

---

## 1. Session Context & Goal

**Purpose:**

* Week 3 of system design interview preparation
* Practice designing a **Web Crawler**
* Focus on scalability, fault tolerance, and distributed systems

**Problem Statement:**
Design a **web crawler** that systematically crawls the entire World Wide Web, extracts content, and stores it for downstream use cases such as:

* Search engine indexing
* LLM training data collection
* Web archiving

---

## 2. Assumptions & Scope

* Crawler starts from a pool of seed URLs
* Processes URLs recursively, discovering new links
* Focuses on text data extraction (not images/video)
* Unlimited compute resources available
* Must complete full crawl within one day

---

## 3. Requirements

### 3.1 Functional Requirements

* Crawl full web starting from seed URLs
* Extract text data from web pages
* Store extracted data for downstream processing
* Support scheduled/periodic crawling
* Avoid duplicate content storage

---

### 3.2 Non-Functional Requirements

* **Fault tolerant:** System should not lose data on failures; can resume from where it left off
* **Politeness:** Respect robots.txt; limit request rate per domain (~1 req/sec)
* **Scalable:** Handle 5 billion web pages
* **Efficient:** Complete full crawl under one day

---

## 4. Scale & Capacity Assumptions

* Total web pages: ~5 billion
* Average page size: ~2 MB
* Crawl window: 1 day (86,400 seconds)

---

## 5. Capacity Estimation

### Storage Calculation

* 5 billion pages × 2 MB = **10 petabytes** total storage

### Throughput Calculation

* 5 billion pages / 86,400 seconds ≈ **60,000 pages/second**
* 60,000 pages × 2 MB = **~120 GB/second** bandwidth

### Machine Estimation

* Single instance throughput: ~400 Mbps (50 MB/s)
* Required instances: 120 GB/s ÷ 50 MB/s ≈ **2,000+ machines**

---

## 6. Core Entities

### URL/Seed

* URL string
* Geolocation hash
* Last crawled timestamp
* robots.txt rules

### Web Page Content

* URL (key)
* HTML content
* Extracted text
* Content hash (for deduplication)

---

## 7. System Interface

### Input

* Set of seed URLs

### Output

* Stored text data (extracted from web pages)

---

## 8. Data Flow

1. Pull URL from Frontier Queue
2. Resolve DNS → get IP address
3. Fetch HTML from web page
4. Store HTML in blob storage (S3)
5. Extract text and URLs from HTML
6. Store extracted text
7. Push discovered URLs to Frontier Queue
8. Repeat

---

## 9. High-Level Design (HLD)

### Components

* **Scheduler:** Initializes crawl, populates Frontier Queue from seeds or URL storage
* **Frontier Queue:** Distributed queue (Kafka) holding URLs to crawl
* **Crawler Workers:** Distributed workers that fetch HTML and store raw data
* **Extraction Service:** Processes HTML, extracts text and URLs
* **DNS Cache:** Redis cluster for caching DNS lookups
* **Blob Storage:** S3 for HTML and text data
* **URL Metadata Store:** DynamoDB for URL metadata (last crawled, robots.txt, geolocation)

### Architecture Flow

```
Seeds Storage → Scheduler → Frontier Queue (Kafka)
                                ↓
                         Crawler Workers (distributed)
                                ↓
                         DNS Cache (Redis) ← DNS Service
                                ↓
                         Fetch from Web Pages
                                ↓
                         Store HTML → S3
                                ↓
                         Extraction Service
                                ↓
                    ┌──────────┴──────────┐
                    ↓                     ↓
            Store Text → S3       Push URLs → Frontier Queue
                    ↓
            URL Metadata → DynamoDB
```

---

## 10. Technology Choices

### Frontier Queue: Kafka

**Why not SQS:**
* SQS limited to ~3,000 messages/second
* Message size limitations
* Account-level queue limits

**Why Kafka:**
* Supports millions/billions of messages per second
* Distributed and persistent
* Supports topic partitioning by geolocation

### Blob Storage: S3

* Handles petabyte-scale data
* Cost-effective for large objects

### URL Metadata: DynamoDB

* Highly scalable NoSQL
* Efficient key-based lookups
* Supports composite keys (URL + geolocation)

### DNS Cache: Redis Cluster

* In-memory for low latency
* Distributed by geolocation

---

## 11. Politeness Implementation

* Store robots.txt rules with URL metadata
* Track last crawled timestamp per domain
* Crawler checks rules before fetching
* Limit to ~1 request per second per domain
* Skip URLs that violate politeness rules

---

## 12. Fault Tolerance

### Distributed Workers
* 2,000+ workers across multiple geographic regions
* Regional distribution reduces network latency

### DNS Redundancy
* Multiple DNS providers
* Round-robin or failover approach

### Separation of Concerns
* Crawler workers: Fetch and store HTML only
* Extraction service: Separate service for processing
* If worker fails, URL remains in queue for retry

### Kafka Persistence
* Messages persisted to disk
* No data loss on worker failure

---

## 13. Scalability Considerations

### Geographic Distribution

* Partition Frontier Queue by geolocation (Kafka topics)
* Crawlers consume from local region's topic
* Reduces network latency significantly

### DNS Caching

* Redis cache distributed by region
* Each regional crawler cluster has its own cache

### Deduplication

* Calculate content hash for each page
* Only store if hash differs from existing content
* Prevents redundant storage

---

## 14. Crawler Traps

### Types of Traps

* **Calendar traps:** Infinite date-based URLs
* **Circular links:** Page A → Page B → Page A
* **Dynamic content:** URL parameters generate infinite pages
* **Query parameter variations:** Same content, different URLs

### Mitigation Strategies

* Limit crawl depth per domain
* Time-based limits per domain
* URL normalization
* Content hash comparison
* Breadth-first search (preferred over depth-first)

---

## 15. Graph Traversal Strategy

* Web is a directed graph
* **Breadth-first search** preferred:
    * Ensures coverage across many domains
    * Avoids getting stuck in deep site hierarchies
    * Better for discovering diverse content

---

## 16. Summary & Reflection

* Designed scalable web crawler for 5 billion pages
* Key insight: Separate fetching from extraction for fault tolerance
* Geographic partitioning critical for performance
* Kafka essential for handling message volume
* Politeness and trap avoidance prevent abuse and infinite loops
* Next improvement: Deep dive into URL prioritization and freshness strategies

---

*End of Session 3*
