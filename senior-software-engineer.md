# Interview Preparation Plan (8 Weeks)

**Schedule**
- **Monday 10:30–11:30** — Coding / Algorithms
- **Wednesday 8:00–9:00** — System Design
- **Thursday 10:00–11:00** — Behavioral / Review

## Resources

### Coding
- [LeetCode](https://leetcode.com/) — Practice problems
- [NeetCode](https://neetcode.io/) — Curated problem lists and video explanations
- [Big-O Cheat Sheet](https://www.bigocheatsheet.com/) — Time/space complexity reference

### System Design
- [System Design Primer](https://github.com/donnemartin/system-design-primer) — Comprehensive guide
- [ByteByteGo](https://bytebytego.com/) — System design fundamentals
- [Designing Data-Intensive Applications](https://dataintensive.net/) — Book by Martin Kleppmann

### Behavioral
- [STAR Method Guide](https://www.themuse.com/advice/star-interview-method) — How to structure answers
- [Amazon Leadership Principles](https://www.amazon.jobs/content/en/our-workplace/leadership-principles) — Common behavioral themes

---

## Daily Guidance

### Monday — Coding Session Guide

**Before You Start (5 min)**
1. Close all distractions (Slack, email, phone)
2. Open a plain text editor or paper — no IDE autocomplete
3. Set a timer for the full hour

**Problem Solving Process (45 min)**

| Phase | Time | What to Do |
|-------|------|------------|
| **1. Understand** | 5 min | Read problem twice. Identify inputs, outputs, constraints. Ask yourself: "What are the edge cases?" |
| **2. Examples** | 5 min | Work through 2-3 examples by hand. Include edge case (empty input, single element, duplicates) |
| **3. Approach** | 10 min | Verbalize your approach OUT LOUD as if interviewer is listening. State brute force first, then optimize |
| **4. Code** | 15 min | Write clean code with meaningful variable names. No IDE — write it like you would on a whiteboard |
| **5. Test** | 5 min | Trace through your code with an example. Check edge cases |
| **6. Analyze** | 5 min | State time and space complexity. Explain why |

**After Solving (10 min)**
1. If stuck > 20 min, look at hints (not solution)
2. After solving, read the editorial and top solutions
3. Note the pattern (sliding window, two pointers, BFS, etc.)
4. Write one sentence: "This problem teaches me..."

**Common Mistakes to Avoid**
- Jumping to code before understanding the problem
- Not talking through your thinking
- Ignoring edge cases
- Writing messy variable names (avoid `i`, `j`, `temp` when better names exist)

**Speaking Template**
> "Let me make sure I understand the problem... [restate]. I'm thinking of using [approach] because [reason]. The time complexity would be O(n) because [explanation]. Let me code this up..."

---

### Wednesday — System Design Session Guide

**Before You Start (5 min)**
1. Have paper/whiteboard ready for drawing
2. Open the design guide link for reference (read AFTER your attempt)
3. Set timer for 55 minutes

**Design Process (50 min)**

| Phase | Time | What to Do |
|-------|------|------------|
| **1. Requirements** | 5 min | List functional requirements (what it does) and non-functional requirements (scale, latency, availability) |
| **2. Estimations** | 5 min | Calculate: users, requests/sec, storage needed, bandwidth. Use round numbers |
| **3. API Design** | 5 min | Define 3-5 core endpoints. Include request/response format |
| **4. Data Model** | 10 min | Design database schema. Decide SQL vs NoSQL. Define key entities and relationships |
| **5. High-Level Design** | 10 min | Draw boxes: clients, load balancer, services, database, cache. Show data flow with arrows |
| **6. Deep Dive** | 10 min | Pick 1-2 components to detail. Discuss trade-offs |
| **7. Scaling** | 5 min | Address bottlenecks. Add caching, sharding, replication as needed |

**After Design (5 min)**
1. Read the guide/solution
2. List 3 things you missed
3. Write trade-offs you would discuss

**Key Questions to Always Answer**
- How does data flow through the system?
- What happens when a component fails?
- How do you handle 10x traffic?
- Where are the bottlenecks?
- What are you trading off (consistency vs availability, latency vs throughput)?

**Non-Functional Requirements Checklist**
- [ ] Scalability — Can it handle growth?
- [ ] Availability — What's the uptime target? (99.9% = 8.7 hrs downtime/year)
- [ ] Latency — What's acceptable response time?
- [ ] Consistency — Strong or eventual?
- [ ] Durability — Can you lose data?

**Speaking Template**
> "Before I dive in, let me clarify the requirements... For scale, I'm assuming [X users, Y requests/sec]. Let me start with the API design... Now for the high-level architecture... The trade-off here is [X vs Y], and I chose [X] because..."

---

### Thursday — Behavioral Session Guide

**Before You Start (5 min)**
1. Review the STAR framework
2. Have your notes from previous sessions
3. Set timer for 55 minutes

**STAR Framework**

| Component | Time | What to Include |
|-----------|------|-----------------|
| **Situation** | 2 sentences | Context: company, team, project, timeline |
| **Task** | 1-2 sentences | Your specific responsibility. Use "I", not "we" |
| **Action** | 4-5 sentences | Concrete steps YOU took. Be specific about technical decisions |
| **Result** | 2-3 sentences | Quantifiable outcome. What you learned |

**Story Preparation Process (45 min)**

1. **Draft Stories (30 min)** — Write 2-3 STAR stories for the week's theme
2. **Refine (10 min)** — Cut to 8-10 sentences each. Remove filler words
3. **Practice Aloud (5 min)** — Say one story out loud. Time yourself (aim for 2 minutes)

**After Writing (10 min)**
1. Read story aloud — does it sound natural?
2. Check: Did you use specific numbers/metrics?
3. Check: Is the "I" clear vs "we"?
4. Identify one weak point to improve next week

**Stories Every Senior Engineer Needs**

| Theme | Question Types |
|-------|----------------|
| **Technical Leadership** | Led architecture decision, introduced new technology, solved complex bug |
| **Conflict Resolution** | Disagreed with teammate/manager, handled difficult stakeholder |
| **Failure & Learning** | Made mistake, missed deadline, project failed |
| **Mentorship** | Helped junior grow, conducted code reviews, knowledge sharing |
| **Ambiguity** | Unclear requirements, changing priorities, incomplete information |
| **Impact** | Improved performance, saved costs, delivered key feature |

**Common Mistakes to Avoid**
- Being too vague ("I helped improve the system")
- Using "we" instead of "I"
- No concrete metrics or outcomes
- Stories longer than 2-3 minutes
- Not explaining WHY you made decisions

**Speaking Template**
> "Let me tell you about a time when [situation]. I was responsible for [task]. The approach I took was [action 1], then [action 2], and finally [action 3]. As a result, [outcome with metric]. What I learned from this was..."

**"Tell Me About Yourself" Template (60-90 seconds)**
> "I'm a [role] with [X years] of experience in [domain]. Currently at [company], I [main responsibility]. A recent highlight was [achievement]. I'm excited about [what draws you to this role]. I'm looking for [what you want in next role]."

---

## Week 1

### Monday — Coding
**Goal:** Warm-up, establish baseline

- [x] Solve [Two Sum](https://leetcode.com/problems/two-sum/)
- [x] Solve without IDE (paper or notes first)
- [x] Implement clean solution
- [x] Write time complexity
- [x] Write space complexity
- [x] Describe one alternative approach
- [ ] (Optional) Solve [Valid Anagram](https://leetcode.com/problems/valid-anagram/)

---

### Wednesday — System Design
**Goal:** Learn design structure

- [ ] Write design framework (requirements, APIs, data model, scaling)
- [ ] Design **URL Shortener** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-tinyurl)
- [ ] Draw components
- [ ] Draw data flow
- [ ] List 5 key trade-offs

---

### Thursday — Behavioral
**Goal:** Start STAR stories

- [ ] Write STAR: Production incident
- [ ] Write STAR: Conflict with teammate
- [ ] Limit each story to 8–10 sentences
- [ ] Identify 1 weak answer

---

## Week 2

### Monday — Coding
**Goal:** Arrays & strings

- [ ] Solve [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [ ] Explain sliding window out loud — [Pattern Guide](https://leetcode.com/discuss/study-guide/657507/Sliding-Window-for-Beginners-Problems-or-Template-or-Sample-Solutions)
- [ ] List edge cases
- [ ] Re-solve in under 20 minutes

---

### Wednesday — System Design
**Goal:** Rate limiting

- [ ] Design **Rate Limiter** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/design-of-a-rate-limiter)
- [ ] Compare token bucket vs leaky bucket — [Article](https://www.figma.com/blog/an-alternative-approach-to-rate-limiting/)
- [ ] Decide where rate limiting lives (gateway vs service)

---

### Thursday — Behavioral
**Goal:** Ownership & failure

- [ ] Write STAR: Big mistake
- [ ] Describe how it was fixed
- [ ] Answer: What would you do differently?

---

## Week 3

### Monday — Coding
**Goal:** Trees

- [ ] Solve [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [ ] Implement BFS — [BFS Guide](https://leetcode.com/explore/learn/card/data-structure-tree/134/traverse-a-tree/931/)
- [ ] Explain why DFS is less suitable

---

### Wednesday — System Design
**Goal:** Data-heavy system

- [ ] Design **Notification System** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-web-crawler)
- [ ] Describe fan-out strategy
- [ ] Define retry handling
- [ ] Handle deduplication

---

### Thursday — Behavioral
**Goal:** Disagreement & influence

- [ ] Answer: Disagree and commit
- [ ] Explain how you influenced the decision
- [ ] Describe the outcome

---

## Week 4

### Monday — Coding
**Goal:** Recursion

- [ ] Solve [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] Draw recursion tree
- [ ] Identify base cases

---

### Wednesday — System Design
**Goal:** Storage systems

- [ ] Design **File Storage Service** — [Dropbox Design](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-google-docs)
- [ ] Separate metadata and blobs
- [ ] Handle versioning
- [ ] Define access control

---

### Thursday — Behavioral
**Goal:** Mentorship

- [ ] Write STAR: Mentoring a junior engineer
- [ ] Describe impact on the team

---

## Week 5

### Monday — Coding
**Goal:** Graphs

- [ ] Solve [Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [ ] Explain graph modeling — [Graph Guide](https://leetcode.com/explore/learn/card/graph/)
- [ ] Compare BFS vs DFS

---

### Wednesday — System Design
**Goal:** Event-driven systems

- [ ] Design **Metrics / Logging Pipeline** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/distributed-logging)
- [ ] Define message queue (Kafka-like) — [Kafka Basics](https://kafka.apache.org/documentation/)
- [ ] Describe consumers
- [ ] Handle backpressure

---

### Thursday — Behavioral
**Goal:** Leadership signals

- [ ] Answer: Handling vague requirements
- [ ] Emphasize structure and decision-making

---

## Week 6

### Monday — Coding
**Goal:** Graph dependencies

- [ ] Solve [Course Schedule](https://leetcode.com/problems/course-schedule/)
- [ ] Explain cycle detection — [Topological Sort](https://leetcode.com/discuss/study-guide/1786329/topological-sort-for-beginners)
- [ ] Identify failure cases

---

### Wednesday — System Design
**Goal:** Real-world system

- [ ] Design **IoT Data Ingestion System**
- [ ] Handle throughput — [Time Series DB](https://www.influxdata.com/time-series-database/)
- [ ] Ensure idempotency
- [ ] Plan horizontal scaling

---

### Thursday — Behavioral
**Goal:** Handling pressure

- [ ] Answer: Most stressful situation
- [ ] Explain prioritization and reasoning

---

## Week 7 — Mock Interviews

### Monday — Coding
- [ ] Timed solve: [Clone Graph](https://leetcode.com/problems/clone-graph/)
- [ ] 45 minutes solving
- [ ] 15 minutes reviewing mistakes

---

### Wednesday — System Design
- [ ] Full mock: **Real-time Tracking System**
- [ ] Identify missed risks
- [ ] Identify overengineering

---

### Thursday — Behavioral
- [ ] Answer 5 random behavioral questions — [Question List](https://www.techinterviewhandbook.org/behavioral-interview-questions/)
- [ ] Speak only (no writing)

---

## Week 8 — Polish

### Monday — Coding
- [ ] Re-solve hardest problem
- [ ] Focus on explanation clarity

---

### Wednesday — System Design
- [ ] Review top 3 system designs
- [ ] Prepare default architecture template — [Template](https://github.com/donnemartin/system-design-primer#system-design-interview-questions-with-solutions)

---

### Thursday — Behavioral
- [ ] Finalize "Tell me about yourself" — [Guide](https://www.techinterviewhandbook.org/self-introduction/)
- [ ] Finalize top 5 STAR stories

---

## When You Get Stuck

### Coding Problems — Troubleshooting

**Stuck Decision Tree**

```
Can't understand the problem?
  └─> Re-read constraints. Draw input/output examples.
      Still stuck? → Read LeetCode Discuss for clarification (not solutions)

Don't know which approach to use?
  └─> Ask: "What pattern does this remind me of?"
      └─> Use Pattern Recognition Table below
      └─> Try brute force first — optimize after

Code doesn't work?
  └─> Trace through with smallest example by hand
  └─> Add print statements at each step
  └─> Check off-by-one errors (loop bounds, indices)

Time Limit Exceeded?
  └─> Current complexity is too high
  └─> Look for unnecessary repeated work
  └─> Consider: HashMap, sorting, two pointers, binary search

Can't optimize further?
  └─> After 25 min, read hints (not solution)
  └─> After 35 min, read solution, then re-implement without looking
```

**Pattern Recognition Table**

| If you see... | Think about... | Learn more |
|---------------|----------------|------------|
| "Find pair/subarray with sum X" | Two pointers, HashMap | [Two Pointers Guide](https://leetcode.com/discuss/study-guide/1688903/Solved-all-two-pointers-problems-in-100-days) |
| "Contiguous subarray" | Sliding window | [Sliding Window](https://leetcode.com/discuss/study-guide/657507/Sliding-Window-for-Beginners-Problems-or-Template-or-Sample-Solutions) |
| "Find min/max in sorted or range" | Binary search | [Binary Search Guide](https://leetcode.com/discuss/study-guide/786126/Python-Powerful-Ultimate-Binary-Search-Template) |
| "Tree traversal" | BFS (level), DFS (path) | [Tree Patterns](https://leetcode.com/discuss/study-guide/1337373/Tree-question-pattern-oror2021-placement) |
| "Graph connectivity" | BFS/DFS, Union-Find | [Graph Guide](https://leetcode.com/discuss/study-guide/655708/Graph-For-Beginners-Problems-or-Pattern-or-Sample-Solutions) |
| "Dependencies/ordering" | Topological sort | [Topological Sort](https://leetcode.com/discuss/study-guide/1786329/topological-sort-for-beginners) |
| "All combinations/permutations" | Backtracking | [Backtracking Template](https://leetcode.com/discuss/study-guide/1405817/Backtracking-algorithm-%2B-problems-to-practice) |
| "Optimal substructure" | Dynamic programming | [DP Patterns](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns) |
| "K largest/smallest" | Heap | [Heap Guide](https://leetcode.com/discuss/study-guide/1360400/Priority-Queue-oror-Problems-oror-Template-oror-All-in-One) |
| "String matching" | Trie, KMP | [String Algorithms](https://leetcode.com/discuss/study-guide/1723337/String-Algorithm-oror-Pattern-oror-Template) |

**Deep Dive Resources by Topic**

| Topic | When to Study | Resources |
|-------|---------------|-----------|
| Arrays & Hashing | Week 1-2 | [NeetCode Arrays](https://neetcode.io/roadmap) |
| Two Pointers | Week 2 | [Patterns Guide](https://leetcode.com/discuss/study-guide/1688903/Solved-all-two-pointers-problems-in-100-days) |
| Sliding Window | Week 2 | [Template + Problems](https://leetcode.com/discuss/study-guide/657507/Sliding-Window-for-Beginners-Problems-or-Template-or-Sample-Solutions) |
| Binary Search | Week 2-3 | [Ultimate Template](https://leetcode.com/discuss/study-guide/786126/Python-Powerful-Ultimate-Binary-Search-Template) |
| Trees | Week 3-4 | [Tree Patterns](https://leetcode.com/explore/learn/card/data-structure-tree/) |
| Graphs | Week 5-6 | [Graph for Beginners](https://leetcode.com/explore/learn/card/graph/) |
| Dynamic Programming | If needed | [DP Study Guide](https://leetcode.com/discuss/study-guide/1433252/Dynamic-Programming-Patterns) |
| Backtracking | If needed | [Backtracking Template](https://leetcode.com/discuss/study-guide/1405817/Backtracking-algorithm-%2B-problems-to-practice) |

**Recovery Actions**

| Problem | Action |
|---------|--------|
| Failed same pattern 2+ times | Stop. Study the pattern guide for 30 min before next attempt |
| Solved but took > 45 min | Re-solve tomorrow without looking. Target: < 25 min |
| Couldn't solve at all | Study solution → wait 2 days → re-solve from scratch |
| Keep making same mistake | Create a "mistake log" — write pattern and fix |

---

### System Design — Troubleshooting

**Stuck Decision Tree**

```
Don't know where to start?
  └─> Always start with requirements (functional + non-functional)
  └─> Ask: "What are the core use cases?"

Can't estimate scale?
  └─> Use defaults: 1M DAU, 10:1 read/write ratio
  └─> Work backwards from storage/bandwidth

Don't know which database?
  └─> Use Database Selection Guide below

Can't think of components?
  └─> Use Building Blocks Reference below

Design feels incomplete?
  └─> Walk through a request end-to-end
  └─> Ask: "What happens when X fails?"
```

**Database Selection Guide**

| Need | Choose | Examples |
|------|--------|----------|
| ACID transactions, complex queries | SQL (PostgreSQL, MySQL) | User accounts, orders, payments |
| High write throughput, flexible schema | NoSQL Document (MongoDB) | Product catalogs, content management |
| Simple key-value, caching | Redis, Memcached | Sessions, rate limiting, caching |
| Time-series data | InfluxDB, TimescaleDB | Metrics, IoT sensor data |
| Graph relationships | Neo4j | Social networks, recommendations |
| Full-text search | Elasticsearch | Search engines, log analysis |
| Wide-column, massive scale | Cassandra, HBase | Analytics, event logging |

**Building Blocks Reference**

| Component | When to Use | Key Considerations |
|-----------|-------------|-------------------|
| **Load Balancer** | Multiple servers | Round robin vs least connections, health checks |
| **CDN** | Static content, global users | Cache invalidation, edge locations |
| **Cache** | Repeated reads | TTL, invalidation strategy, cache-aside vs write-through |
| **Message Queue** | Async processing | At-least-once vs exactly-once, ordering |
| **API Gateway** | Multiple services | Rate limiting, auth, routing |
| **Database Replication** | High availability | Leader-follower, sync vs async |
| **Sharding** | Data too large for one node | Shard key selection, rebalancing |
| **Consistent Hashing** | Distributed cache/storage | Virtual nodes, replication |

**Common Design Mistakes & Fixes**

| Mistake | Fix |
|---------|-----|
| Jumping to solution | Spend 5 min on requirements first |
| Single point of failure | Add redundancy to every critical component |
| No caching | Cache heavy reads at application and/or CDN layer |
| Synchronous everything | Use message queues for non-critical paths |
| Ignoring failure modes | Discuss retry, circuit breaker, fallback for each component |
| Over-engineering | Start simple, scale only when you explain WHY |

**Deep Dive Resources by Topic**

| Topic | Resources |
|-------|-----------|
| Caching | [Caching Strategies](https://codeahoy.com/2017/08/11/caching-strategies-and-how-to-choose-the-right-one/) |
| Message Queues | [Kafka vs RabbitMQ](https://www.confluent.io/learn/kafka-vs-rabbitmq/) |
| Database Sharding | [Sharding Guide](https://www.digitalocean.com/community/tutorials/understanding-database-sharding) |
| CAP Theorem | [CAP Explained](https://www.ibm.com/topics/cap-theorem) |
| Microservices | [Microservices Patterns](https://microservices.io/patterns/index.html) |
| Load Balancing | [Load Balancing Algorithms](https://samwho.dev/load-balancing/) |

---

### Behavioral — Troubleshooting

**Stuck Decision Tree**

```
Can't think of a story?
  └─> Use Story Mining Prompts below
  └─> ANY project counts — even small ones

Story feels weak/boring?
  └─> Add specific metrics
  └─> Focus on YOUR decisions, not team's
  └─> Emphasize the "why" behind actions

Story is too long?
  └─> Cut situation to 2 sentences max
  └─> Remove unnecessary technical details
  └─> Focus: What did YOU do? What was the result?

Don't have a "failure" story?
  └─> Reframe: What would you do differently?
  └─> Small mistakes count (missed deadline, wrong tech choice)

Can't quantify results?
  └─> Use relative terms: "reduced by 50%", "cut in half"
  └─> Soft metrics work: "team adopted my approach", "became the standard"
```

**Story Mining Prompts**

Use these prompts to find stories from your experience:

| Category | Questions to Ask Yourself |
|----------|---------------------------|
| **Technical Leadership** | When did you make a technical decision others disagreed with? When did you introduce a new tool/library? When did you debug something nobody else could fix? |
| **Conflict** | When did you disagree with your manager? When did two teams want different things? When did you have to say no to someone? |
| **Failure** | When did something you built break in production? When did you miss a deadline? When did you make a wrong technical choice? |
| **Mentorship** | When did you help someone grow? When did you onboard a new team member? When did you do a code review that taught something? |
| **Ambiguity** | When did you start a project with unclear requirements? When did priorities change mid-project? When did you have to make decisions with incomplete info? |
| **Impact** | When did you improve performance significantly? When did you save the company money/time? When did you ship something users loved? |

**Story Strengthening Techniques**

| Weak Story Element | How to Strengthen |
|--------------------|-------------------|
| "The team did X" | Change to "I suggested X, and the team adopted it" |
| "It improved performance" | Change to "Response time dropped from 2s to 200ms" |
| "I helped with the project" | Change to "I designed the API and implemented the caching layer" |
| "We had a problem" | Change to "Our system was dropping 10% of requests during peak hours" |
| "It was successful" | Change to "We shipped on time and reduced support tickets by 40%" |

**Stories You Must Have Ready**

- [ ] Tell me about yourself (60-90 seconds)
- [ ] Biggest technical challenge
- [ ] Time you failed / made a mistake
- [ ] Conflict with teammate or manager
- [ ] Led a project or initiative
- [ ] Mentored someone
- [ ] Handled ambiguous requirements
- [ ] Why are you leaving current job?
- [ ] Why do you want this role?

---

## Weekly Review Process

Every **Friday (15 min)** — assess the week and adjust:

### Review Checklist

- [ ] Did I complete all 3 sessions this week?
- [ ] Which session was hardest? Why?
- [ ] What pattern/concept am I still weak on?
- [ ] Do I need to add extra practice for any topic?

### Adjustment Actions

| Situation | Action |
|-----------|--------|
| Missed a session | Make it up on weekend, don't skip |
| Struggled with coding pattern | Add 2 extra problems of that pattern next week |
| System design felt incomplete | Re-read guide, list what you missed |
| Behavioral story was weak | Rewrite with stronger metrics |
| Feeling confident | Add one harder problem or design |
| Feeling overwhelmed | Reduce scope — focus on core problems only |

### Progress Tracker

| Week | Coding Confidence (1-5) | System Design Confidence (1-5) | Behavioral Confidence (1-5) | Notes |
|------|-------------------------|--------------------------------|-----------------------------|-------|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
| 6 | | | | |
| 7 | | | | |
| 8 | | | | |

---

## Emergency Prep (If Interview is Sooner)

If you have less than 8 weeks, focus on **highest impact items**:

### 1 Week Available
- [ ] Solve: Two Sum, Valid Anagram, LCA
- [ ] Design: URL Shortener only
- [ ] Behavioral: 3 STAR stories + "Tell me about yourself"

### 2 Weeks Available
- [ ] Solve: Two Sum, Longest Substring, Level Order Traversal, Number of Islands
- [ ] Design: URL Shortener, Rate Limiter
- [ ] Behavioral: 5 STAR stories covering all themes

### 4 Weeks Available
- [ ] Complete Weeks 1, 2, 5, 7 from this plan
- [ ] Skip Weeks 3, 4, 6
- [ ] Focus on mock interviews in final week

---

## Rules

- [ ] Stop exactly at the end of each time block
- [ ] No multitasking
- [ ] Notes over memorization
- [ ] Consistency over intensity
