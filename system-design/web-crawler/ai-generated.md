# Distributed Web Crawler System Design
## FAANG Senior Engineer Level

---

## 1. Problem Clarification & Scope

### Core Problem
Design a distributed web crawler that systematically traverses the World Wide Web, downloads web pages, extracts content, and stores it for downstream applications like search engine indexing, LLM training data collection, or web archiving.

### Clarifying Questions to Ask
- What's the primary use case? (Search indexing, ML training, archival)
- Should we crawl the entire web or specific domains?
- What content types should we extract? (Text only, images, videos, all)
- What's the freshness requirement? (How often to re-crawl)
- Do we need to respect robots.txt and crawl delays?
- Should we render JavaScript or just fetch static HTML?
- What's the budget for infrastructure?
- Do we need real-time crawling or batch processing?

### Scope Definition

**In Scope:**
- Distributed crawling infrastructure for 5+ billion pages
- Text content extraction and storage
- URL frontier management and prioritization
- Politeness enforcement (robots.txt, rate limiting)
- Deduplication at URL and content level
- Fault tolerance and recovery
- Geographic distribution for latency optimization

**Out of Scope (mention but don't design):**
- JavaScript rendering (mention Puppeteer/Playwright if asked)
- Image/video processing pipelines
- Search indexing and ranking
- Natural language processing of content
- Anti-bot bypass techniques (respect site rules)

---

## 2. Requirements

### Functional Requirements

| Requirement | Priority | Description |
|-------------|----------|-------------|
| Crawl from seeds | P0 | Start crawling from provided seed URLs |
| Extract content | P0 | Parse HTML and extract text/links |
| Store content | P0 | Persist raw HTML and extracted text |
| URL discovery | P0 | Extract and queue new URLs from pages |
| Deduplication | P1 | Avoid storing duplicate content |
| Scheduled crawling | P1 | Support periodic re-crawling |
| Politeness | P1 | Respect robots.txt and crawl delays |
| URL prioritization | P2 | Prioritize important/fresh URLs |

### Non-Functional Requirements

| Requirement | Target | Rationale |
|-------------|--------|-----------|
| Scale | 5 billion pages | Approximate size of indexable web |
| Throughput | 60,000 pages/sec | Complete crawl in ~1 day |
| Storage | 10+ petabytes | 5B pages × 2MB average |
| Fault tolerance | No data loss | Resume from failures without restart |
| Latency | Minimize per-page | Geographic distribution |
| Politeness | 1 req/sec/domain | Industry standard, avoid bans |

### Failure Mode Decision
**Critical Trade-off:** What happens when a component fails?

| Component | Failure Impact | Mitigation | Decision |
|-----------|----------------|------------|----------|
| Crawler worker | Lost in-flight URLs | URL stays in queue until ACK | ✅ At-least-once |
| Extraction service | Unprocessed HTML | Retry from storage | ✅ Idempotent |
| Frontier queue | Lost URLs | Kafka persistence | ✅ Durable |
| DNS cache miss | Slower lookups | Fallback to DNS | ✅ Degraded |

---

## 3. Capacity Estimation

### Traffic Assumptions
```
Total web pages:           5 billion
Average page size:         2 MB (HTML + resources)
Crawl window:              1 day (86,400 seconds)
Pages per second:          5B / 86,400 ≈ 58,000 pages/sec
```

### Storage Calculation

#### Raw Storage
```
HTML storage:              5B × 2 MB = 10 PB
Extracted text:            5B × 50 KB = 250 TB
URL metadata:              5B × 200 bytes = 1 TB
─────────────────────────────────────────────
Total storage:             ~10.3 PB
```

#### With Compression (typical 5:1 ratio)
```
Compressed HTML:           ~2 PB
Compressed text:           ~50 TB
─────────────────────────────────────────────
Compressed total:          ~2.1 PB
```

### Bandwidth Calculation
```
Download bandwidth:        58,000 pages/sec × 2 MB = 116 GB/sec
                          = 928 Gbps

Per-machine capacity:      ~400 Mbps = 50 MB/sec
Machines needed:           116 GB/sec ÷ 50 MB/sec ≈ 2,300 machines
With overhead (1.5x):      ~3,500 crawler instances
```

### DNS Query Volume
```
Unique domains (estimate): 500 million
DNS queries/sec:           58,000 (worst case, no caching)
With 90% cache hit:        5,800 DNS queries/sec
```

---

## 4. Core Entities & Data Model

### Entities

```
┌─────────────────────────────────────────────────────────────┐
│ URL                                                         │
├─────────────────────────────────────────────────────────────┤
│ url: string              (primary key, normalized)          │
│ domain: string           (extracted for rate limiting)      │
│ priority: int            (crawl priority score)             │
│ last_crawled: timestamp  (for freshness)                    │
│ crawl_status: enum       (pending | crawling | done | error)│
│ content_hash: string     (for deduplication)                │
│ robots_rules: json       (cached robots.txt directives)     │
│ geo_region: string       (for geographic partitioning)      │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ WebPage                                                     │
├─────────────────────────────────────────────────────────────┤
│ url: string              (foreign key to URL)               │
│ html_path: string        (S3 path to raw HTML)              │
│ text_path: string        (S3 path to extracted text)        │
│ content_hash: string     (SHA-256 of content)               │
│ crawled_at: timestamp                                       │
│ http_status: int                                            │
│ content_type: string                                        │
│ outlinks: list<string>   (discovered URLs)                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Domain                                                      │
├─────────────────────────────────────────────────────────────┤
│ domain: string           (primary key)                      │
│ robots_txt: string       (cached content)                   │
│ crawl_delay: int         (seconds between requests)         │
│ last_request: timestamp  (for rate limiting)                │
│ ip_addresses: list<string>                                  │
│ geo_region: string                                          │
└─────────────────────────────────────────────────────────────┘
```

### Storage Key Patterns

```
# S3 paths (partitioned by date and domain hash)
s3://crawler-html/{date}/{domain_hash}/{url_hash}.html.gz
s3://crawler-text/{date}/{domain_hash}/{url_hash}.txt.gz

# Kafka topics (partitioned by geo region)
frontier.urls.{region}     # URLs to crawl
extraction.jobs.{region}   # HTML to process

# Redis keys
dns:{domain}              →  {ip, ttl, timestamp}
robots:{domain}           →  {rules_json, fetched_at}
rate:{domain}             →  {last_request_ts}
```

---

## 5. System Interface

### Crawler Worker Interface

```python
class CrawlerWorker:
    def fetch_url(self, url: str) -> CrawlResult:
        """
        Fetches a single URL and returns the result.

        Returns:
            CrawlResult with html_content, http_status, headers, outlinks
        """
        pass

    def check_politeness(self, url: str) -> PolitenessResult:
        """
        Checks if URL can be crawled based on robots.txt and rate limits.

        Returns:
            PolitenessResult with allowed, wait_seconds, reason
        """
        pass
```

### Extraction Service Interface

```protobuf
service ExtractionService {
  rpc ExtractContent(ExtractionRequest) returns (ExtractionResponse);
  rpc BatchExtract(BatchExtractionRequest) returns (BatchExtractionResponse);
}

message ExtractionRequest {
  string url = 1;
  string html_s3_path = 2;
}

message ExtractionResponse {
  string text_content = 1;
  repeated string outlinks = 2;
  string content_hash = 3;
  map<string, string> metadata = 4;  // title, description, etc.
}
```

### Scheduler Interface

```python
class CrawlScheduler:
    def initialize_crawl(self, seed_urls: List[str]) -> str:
        """Starts a new crawl job with seed URLs. Returns job_id."""
        pass

    def schedule_recrawl(self, freshness_days: int) -> str:
        """Schedules recrawl for pages older than freshness_days."""
        pass

    def get_crawl_status(self, job_id: str) -> CrawlStatus:
        """Returns current crawl progress and statistics."""
        pass
```

---

## 6. High-Level Architecture

### Architecture Diagram

```
                                    ┌─────────────────────────────────────┐
                                    │           Seed Storage              │
                                    │     (Initial URLs to crawl)         │
                                    └─────────────────┬───────────────────┘
                                                      │
                                    ┌─────────────────▼───────────────────┐
                                    │            Scheduler                │
                                    │   - Initialize crawl from seeds     │
                                    │   - Schedule periodic recrawls      │
                                    │   - Monitor crawl progress          │
                                    └─────────────────┬───────────────────┘
                                                      │
                    ┌─────────────────────────────────▼─────────────────────────────────┐
                    │                        Frontier Queue (Kafka)                      │
                    │   ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
                    │   │ Region A │  │ Region B │  │ Region C │  │ Region D │          │
                    │   │  Topic   │  │  Topic   │  │  Topic   │  │  Topic   │          │
                    │   └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘          │
                    └────────┼─────────────┼─────────────┼─────────────┼────────────────┘
                             │             │             │             │
           ┌─────────────────▼─────┐ ┌─────▼─────┐ ┌─────▼─────┐ ┌─────▼─────┐
           │   Crawler Workers     │ │  Workers  │ │  Workers  │ │  Workers  │
           │   (Region A)          │ │ (Region B)│ │ (Region C)│ │ (Region D)│
           │   ~500 instances      │ │   ~500    │ │   ~500    │ │   ~500    │
           └──────────┬────────────┘ └─────┬─────┘ └─────┬─────┘ └─────┬─────┘
                      │                    │             │             │
                      │         ┌──────────┴─────────────┴─────────────┘
                      │         │
        ┌─────────────▼─────────▼─────────────┐
        │         External Services           │
        │  ┌───────────┐    ┌───────────┐     │
        │  │    DNS    │    │ Web Pages │     │
        │  │  Servers  │    │ (Internet)│     │
        │  └─────┬─────┘    └───────────┘     │
        └────────┼────────────────────────────┘
                 │
        ┌────────▼────────┐
        │   DNS Cache     │
        │   (Redis)       │
        │   Per-region    │
        └─────────────────┘
                      │
                      ▼
        ┌─────────────────────────────────────────────────────────────────┐
        │                      Blob Storage (S3)                          │
        │   ┌─────────────────┐    ┌─────────────────┐                   │
        │   │   Raw HTML      │    │  Extracted Text │                   │
        │   │   (~2 PB)       │    │   (~50 TB)      │                   │
        │   └─────────────────┘    └─────────────────┘                   │
        └──────────────────────────────┬──────────────────────────────────┘
                                       │
        ┌──────────────────────────────▼──────────────────────────────────┐
        │                    Extraction Queue (Kafka)                      │
        └──────────────────────────────┬──────────────────────────────────┘
                                       │
        ┌──────────────────────────────▼──────────────────────────────────┐
        │                    Extraction Service                            │
        │   - Parse HTML                                                   │
        │   - Extract text content                                         │
        │   - Discover outlinks                                            │
        │   - Compute content hash                                         │
        │   - Handle crawler traps                                         │
        └──────────────────────────────┬──────────────────────────────────┘
                                       │
                    ┌──────────────────┴──────────────────┐
                    │                                     │
        ┌───────────▼───────────┐            ┌───────────▼───────────┐
        │   URL Metadata Store  │            │   Store Text → S3     │
        │   (DynamoDB)          │            │                       │
        │   - URL status        │            └───────────────────────┘
        │   - Last crawled      │
        │   - Content hash      │            ┌───────────────────────┐
        └───────────────────────┘            │  New URLs → Frontier  │
                                             │  Queue (loop back)    │
                                             └───────────────────────┘
```

### Request Flow

```
1. Scheduler reads seed URLs or URL metadata store
2. Scheduler pushes URLs to appropriate Kafka topic (by geo region)
3. Crawler worker pulls URL from regional topic
4. Worker checks politeness:
   a. Check robots.txt cache (Redis)
   b. Check domain rate limit (Redis)
   c. If blocked, requeue with delay
5. Worker resolves DNS:
   a. Check DNS cache (Redis)
   b. If miss, query DNS and cache result
6. Worker fetches page via HTTP
7. Worker stores raw HTML to S3
8. Worker pushes extraction job to Extraction Queue
9. Extraction service processes HTML:
   a. Extract text content
   b. Discover outlinks
   c. Compute content hash
   d. Check for crawler traps
10. Extraction service stores extracted text to S3
11. Extraction service updates URL metadata in DynamoDB
12. Extraction service pushes new URLs to Frontier Queue
13. Repeat from step 3
```

---

## 7. Deep Dive: Frontier Queue & URL Prioritization

### URL Priority Factors

| Factor | Weight | Description |
|--------|--------|-------------|
| PageRank | High | Link-based importance |
| Freshness | High | Time since last crawl |
| Domain authority | Medium | Overall domain quality |
| Change frequency | Medium | How often page updates |
| Depth from seed | Low | Prefer shallow pages |

### Priority Queue Implementation

```python
class URLFrontier:
    """
    Multi-level priority queue for URL scheduling.
    Uses Kafka with priority-based topic routing.
    """

    PRIORITY_TOPICS = {
        "high": "frontier.urls.{region}.high",
        "medium": "frontier.urls.{region}.medium",
        "low": "frontier.urls.{region}.low"
    }

    def enqueue(self, url: str, priority: int, region: str):
        topic = self._get_topic(priority, region)
        message = {
            "url": url,
            "enqueued_at": time.time(),
            "priority": priority
        }
        self.kafka.produce(topic, message)

    def dequeue(self, region: str) -> Optional[str]:
        # Try high priority first, then medium, then low
        for priority in ["high", "medium", "low"]:
            topic = self.PRIORITY_TOPICS[priority].format(region=region)
            message = self.kafka.consume(topic, timeout=100)
            if message:
                return message["url"]
        return None

    def _get_topic(self, priority: int, region: str) -> str:
        if priority >= 80:
            level = "high"
        elif priority >= 40:
            level = "medium"
        else:
            level = "low"
        return self.PRIORITY_TOPICS[level].format(region=region)
```

### Kafka Topic Design

```
# Geographic partitioning for latency optimization
frontier.urls.us-east.high      # High priority URLs in US East
frontier.urls.us-east.medium
frontier.urls.us-east.low
frontier.urls.eu-west.high
frontier.urls.eu-west.medium
frontier.urls.eu-west.low
# ... more regions

# Partition count per topic
Partitions: 100 per topic (allows 100 parallel consumers)

# Consumer groups
crawler-workers-us-east (500 workers)
crawler-workers-eu-west (500 workers)
```

---

## 8. Deep Dive: Politeness & Rate Limiting

### Robots.txt Handling

```python
class RobotsManager:
    """
    Manages robots.txt rules with caching.
    """

    CACHE_TTL = 86400  # 24 hours

    def __init__(self, redis_client):
        self.redis = redis_client
        self.parser = robotparser.RobotFileParser()

    def can_fetch(self, url: str, user_agent: str = "MyBot/1.0") -> bool:
        domain = self._extract_domain(url)
        rules = self._get_cached_rules(domain)

        if rules is None:
            rules = self._fetch_and_cache_rules(domain)

        self.parser.parse(rules.split('\n'))
        return self.parser.can_fetch(user_agent, url)

    def get_crawl_delay(self, domain: str) -> int:
        rules = self._get_cached_rules(domain)
        if rules:
            self.parser.parse(rules.split('\n'))
            delay = self.parser.crawl_delay("MyBot/1.0")
            return delay if delay else 1
        return 1  # Default 1 second

    def _get_cached_rules(self, domain: str) -> Optional[str]:
        return self.redis.get(f"robots:{domain}")

    def _fetch_and_cache_rules(self, domain: str) -> str:
        try:
            response = requests.get(
                f"https://{domain}/robots.txt",
                timeout=10
            )
            rules = response.text if response.status_code == 200 else ""
        except Exception:
            rules = ""  # Assume allowed if can't fetch

        self.redis.setex(f"robots:{domain}", self.CACHE_TTL, rules)
        return rules
```

### Domain Rate Limiter

```python
class DomainRateLimiter:
    """
    Ensures we don't overwhelm any single domain.
    Uses Redis for distributed rate limiting.
    """

    def __init__(self, redis_client, default_delay: int = 1):
        self.redis = redis_client
        self.default_delay = default_delay

    def acquire(self, domain: str, crawl_delay: int = None) -> float:
        """
        Attempts to acquire permission to crawl domain.
        Returns wait time in seconds (0 if can proceed immediately).
        """
        delay = crawl_delay or self.default_delay
        key = f"rate:{domain}"
        now = time.time()

        # Atomic check-and-set with Lua script
        lua_script = """
        local key = KEYS[1]
        local delay = tonumber(ARGV[1])
        local now = tonumber(ARGV[2])

        local last_request = tonumber(redis.call('GET', key) or 0)
        local wait_time = math.max(0, (last_request + delay) - now)

        if wait_time == 0 then
            redis.call('SET', key, now)
            redis.call('EXPIRE', key, delay * 2)
        end

        return wait_time
        """

        wait_time = self.redis.eval(lua_script, 1, key, delay, now)
        return wait_time
```

---

## 9. Deep Dive: Deduplication

### URL Normalization

```python
class URLNormalizer:
    """
    Normalizes URLs to detect duplicates with different representations.
    """

    def normalize(self, url: str) -> str:
        parsed = urlparse(url)

        # Lowercase scheme and host
        scheme = parsed.scheme.lower()
        host = parsed.netloc.lower()

        # Remove default ports
        if ':80' in host and scheme == 'http':
            host = host.replace(':80', '')
        if ':443' in host and scheme == 'https':
            host = host.replace(':443', '')

        # Remove trailing slash from path
        path = parsed.path.rstrip('/') or '/'

        # Sort query parameters
        query_params = parse_qs(parsed.query)
        sorted_query = urlencode(sorted(query_params.items()), doseq=True)

        # Remove fragment
        normalized = urlunparse((scheme, host, path, '', sorted_query, ''))

        return normalized
```

### Content Deduplication with SimHash

```python
class ContentDeduplicator:
    """
    Detects near-duplicate content using SimHash.
    """

    SIMILARITY_THRESHOLD = 3  # Hamming distance threshold

    def __init__(self, redis_client):
        self.redis = redis_client

    def compute_simhash(self, text: str) -> int:
        """
        Computes 64-bit SimHash for text content.
        """
        tokens = self._tokenize(text)
        v = [0] * 64

        for token in tokens:
            token_hash = self._hash64(token)
            for i in range(64):
                if token_hash & (1 << i):
                    v[i] += 1
                else:
                    v[i] -= 1

        simhash = 0
        for i in range(64):
            if v[i] > 0:
                simhash |= (1 << i)

        return simhash

    def is_duplicate(self, url: str, content: str) -> bool:
        """
        Checks if content is near-duplicate of existing page.
        """
        simhash = self.compute_simhash(content)
        domain = self._extract_domain(url)

        # Check against existing hashes for this domain
        existing_hashes = self.redis.smembers(f"simhash:{domain}")

        for existing in existing_hashes:
            distance = self._hamming_distance(simhash, int(existing))
            if distance <= self.SIMILARITY_THRESHOLD:
                return True

        # Store new hash
        self.redis.sadd(f"simhash:{domain}", simhash)
        return False

    def _hamming_distance(self, a: int, b: int) -> int:
        return bin(a ^ b).count('1')
```

---

## 10. Deep Dive: Crawler Traps

### Trap Detection Strategies

| Trap Type | Detection Method | Mitigation |
|-----------|------------------|------------|
| Infinite calendars | URL pattern matching | Block date-parametrized URLs |
| Session IDs | Query param analysis | Strip session parameters |
| Circular links | Path tracking | Max depth per domain |
| Dynamic content | Content hash | Skip if hash unchanged |
| Parameter spam | URL length/complexity | Limit query parameters |

### Trap Detection Implementation

```python
class CrawlerTrapDetector:
    """
    Detects and prevents crawler traps.
    """

    MAX_PATH_DEPTH = 15
    MAX_QUERY_PARAMS = 5
    MAX_URL_LENGTH = 2000

    # Patterns that often indicate traps
    TRAP_PATTERNS = [
        r'/calendar/\d{4}/\d{2}/\d{2}',  # Calendar URLs
        r'/page/\d+$',                     # Infinite pagination
        r'[?&](session|sid|jsessionid)=',  # Session IDs
        r'/search\?.*q=',                  # Search result pages
    ]

    def __init__(self, redis_client):
        self.redis = redis_client
        self.trap_patterns = [re.compile(p) for p in self.TRAP_PATTERNS]

    def is_trap(self, url: str, domain: str) -> Tuple[bool, str]:
        """
        Checks if URL is likely a crawler trap.
        Returns (is_trap, reason).
        """
        # Check URL length
        if len(url) > self.MAX_URL_LENGTH:
            return True, "URL too long"

        parsed = urlparse(url)

        # Check path depth
        path_depth = parsed.path.count('/')
        if path_depth > self.MAX_PATH_DEPTH:
            return True, f"Path depth {path_depth} exceeds max"

        # Check query parameter count
        params = parse_qs(parsed.query)
        if len(params) > self.MAX_QUERY_PARAMS:
            return True, f"Too many query parameters"

        # Check against trap patterns
        for pattern in self.trap_patterns:
            if pattern.search(url):
                return True, f"Matches trap pattern"

        # Check domain-specific crawl count
        domain_count = self.redis.incr(f"crawl_count:{domain}")
        self.redis.expire(f"crawl_count:{domain}", 86400)

        if domain_count > 100000:  # Max 100k pages per domain per day
            return True, "Domain crawl limit exceeded"

        return False, ""

    def should_follow_link(self, base_url: str, link_url: str) -> bool:
        """
        Determines if a discovered link should be followed.
        """
        # Skip non-http(s) links
        if not link_url.startswith(('http://', 'https://')):
            return False

        # Skip common non-content extensions
        skip_extensions = {'.pdf', '.jpg', '.png', '.gif', '.css', '.js', '.zip'}
        if any(link_url.lower().endswith(ext) for ext in skip_extensions):
            return False

        # Check for trap
        domain = self._extract_domain(link_url)
        is_trap, _ = self.is_trap(link_url, domain)

        return not is_trap
```

---

## 11. Deep Dive: Extraction Service

### HTML Content Extraction

```python
class ContentExtractor:
    """
    Extracts meaningful content from HTML pages.
    """

    # Tags that typically contain main content
    CONTENT_TAGS = ['article', 'main', 'section', 'div']

    # Tags to remove (navigation, ads, etc.)
    REMOVE_TAGS = ['nav', 'header', 'footer', 'aside', 'script',
                   'style', 'noscript', 'iframe', 'svg']

    def extract(self, html: str, url: str) -> ExtractionResult:
        """
        Extracts text content and links from HTML.
        """
        soup = BeautifulSoup(html, 'lxml')

        # Remove unwanted elements
        for tag in soup.find_all(self.REMOVE_TAGS):
            tag.decompose()

        # Extract title
        title = soup.title.string if soup.title else ""

        # Extract meta description
        meta_desc = ""
        meta_tag = soup.find('meta', attrs={'name': 'description'})
        if meta_tag:
            meta_desc = meta_tag.get('content', '')

        # Extract main text content
        text_content = self._extract_text(soup)

        # Extract links
        links = self._extract_links(soup, url)

        # Compute content hash
        content_hash = hashlib.sha256(text_content.encode()).hexdigest()

        return ExtractionResult(
            title=title,
            description=meta_desc,
            text=text_content,
            links=links,
            content_hash=content_hash
        )

    def _extract_text(self, soup) -> str:
        """
        Extracts and cleans text content.
        """
        # Try to find main content area
        main_content = (
            soup.find('article') or
            soup.find('main') or
            soup.find(class_=re.compile(r'content|article|post'))
        )

        if main_content:
            text = main_content.get_text(separator=' ', strip=True)
        else:
            text = soup.body.get_text(separator=' ', strip=True) if soup.body else ""

        # Clean up whitespace
        text = re.sub(r'\s+', ' ', text)

        return text.strip()

    def _extract_links(self, soup, base_url: str) -> List[str]:
        """
        Extracts and normalizes all links.
        """
        links = []

        for anchor in soup.find_all('a', href=True):
            href = anchor['href']

            # Convert relative to absolute
            absolute_url = urljoin(base_url, href)

            # Normalize
            normalized = URLNormalizer().normalize(absolute_url)

            links.append(normalized)

        # Deduplicate while preserving order
        return list(dict.fromkeys(links))
```

---

## 12. Monitoring & Observability

### Key Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `crawler.pages_per_second` | Crawl throughput | < 50,000 |
| `crawler.fetch_latency_p99` | Page fetch time | > 5s |
| `crawler.error_rate` | Failed fetches | > 5% |
| `frontier.queue_depth` | URLs pending crawl | > 10M (backlog) |
| `extraction.processing_rate` | Pages extracted/sec | < 40,000 |
| `storage.write_rate` | S3 writes/sec | Monitor |
| `dns.cache_hit_rate` | DNS cache efficiency | < 80% |
| `politeness.blocked_rate` | Requests blocked by limits | Monitor |

### Dashboard Panels

1. **Crawl Progress:** Pages crawled over time, cumulative and rate
2. **Geographic Distribution:** Crawl rate by region
3. **Error Breakdown:** HTTP errors by status code
4. **Queue Health:** Frontier depth per priority level
5. **Top Domains:** Most crawled domains
6. **Latency Distribution:** Fetch time histogram

### Alerting Rules

```yaml
alerts:
  - name: CrawlThroughputLow
    expr: rate(crawler_pages_total[5m]) < 45000
    severity: warning

  - name: FrontierBacklogHigh
    expr: frontier_queue_depth > 50000000
    severity: warning

  - name: ExtractionLag
    expr: extraction_lag_seconds > 300
    severity: critical

  - name: HighErrorRate
    expr: rate(crawler_errors_total[5m]) / rate(crawler_pages_total[5m]) > 0.1
    severity: critical
```

---

## 13. Availability & Fault Tolerance

### Failure Scenarios

| Failure | Impact | Mitigation | Recovery |
|---------|--------|------------|----------|
| Crawler worker crash | Lost in-flight URLs | Kafka offset not committed | Auto-requeue |
| Kafka broker down | Reduced throughput | Replication factor 3 | Auto-failover |
| S3 unavailable | Can't store content | Retry with backoff | Queue backs up |
| DNS cache (Redis) down | Slower lookups | Fallback to DNS | Degraded mode |
| Extraction service down | Unprocessed content | Kafka retention | Catch-up processing |

### Recovery Strategies

```python
class ResilientCrawler:
    """
    Crawler with built-in fault tolerance.
    """

    def __init__(self):
        self.kafka = KafkaConsumer(
            'frontier.urls',
            enable_auto_commit=False,  # Manual commit after success
            auto_offset_reset='earliest'
        )

    def crawl_loop(self):
        for message in self.kafka:
            url = message.value['url']

            try:
                result = self._crawl_with_retry(url)
                self._store_result(result)

                # Only commit after successful processing
                self.kafka.commit()

            except Exception as e:
                # Don't commit - message will be redelivered
                logger.error(f"Failed to crawl {url}: {e}")
                metrics.increment("crawler.errors")

                # Optional: send to dead letter queue after N retries
                if self._get_retry_count(url) > 3:
                    self._send_to_dlq(url, str(e))

    def _crawl_with_retry(self, url: str, max_retries: int = 3) -> CrawlResult:
        """
        Attempts to crawl URL with exponential backoff.
        """
        for attempt in range(max_retries):
            try:
                return self._fetch(url)
            except (ConnectionError, Timeout) as e:
                if attempt == max_retries - 1:
                    raise
                wait_time = (2 ** attempt) + random.random()
                time.sleep(wait_time)
```

---

## 14. Summary: Requirements Mapping

| Requirement | Solution | Component |
|-------------|----------|-----------|
| Scale (5B pages) | Geographic partitioning, 2000+ workers | Distributed architecture |
| Throughput (60K/sec) | Parallel workers, Kafka | Crawler workers + queue |
| Storage (10 PB) | S3 blob storage | Compressed HTML/text |
| Fault tolerance | At-least-once processing | Kafka + manual commits |
| Politeness | robots.txt cache, rate limiting | Redis + domain limiter |
| Deduplication | URL normalization, SimHash | Extraction service |
| Efficiency | DNS caching, geo-distribution | Redis + regional workers |

---

## 15. Interview Discussion Points

### Trade-offs Made

1. **Breadth-first over depth-first:** Better coverage, less risk of traps
2. **Eventual consistency:** Allow duplicate fetches rather than blocking
3. **Separate fetch and extract:** Better fault isolation, but higher latency
4. **Geographic partitioning:** Lower latency, but complex coordination

### Alternative Approaches Worth Mentioning

- **Mercator architecture:** Classic web crawler design with URL frontier
- **Serverless crawling:** AWS Lambda for bursty workloads
- **Browser-based crawling:** Puppeteer/Playwright for JS-heavy sites
- **Focused crawling:** ML-based relevance scoring for specific topics

### Questions to Expect

- "How do you handle JavaScript-rendered content?" (Headless browser, or skip and flag)
- "What if a major site blocks your crawler?" (Rotate IPs, respect robots.txt, contact site owner)
- "How do you prioritize fresh content?" (Adaptive scheduling based on change frequency)
- "How would you design this for real-time news crawling?" (Push-based, smaller scale, lower latency)
- "How do you handle internationalization?" (Language detection, regional workers)

### Complexity Extensions

1. **Incremental crawling:** Only re-fetch changed content
2. **Adaptive scheduling:** Learn optimal crawl frequency per page
3. **Content quality scoring:** Prioritize high-quality content
4. **Distributed coordination:** Cross-region consistency for global sites

---

*End of Web Crawler System Design Guide*
