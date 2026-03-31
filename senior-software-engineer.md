# Interview Preparation Plan (12 Weeks) — Big Tech Senior SWE

**Target:** Senior Software Engineer at FAANG / Big Tech (L5+ equivalent)

**Schedule (4 sessions/week)**
- **Monday 10:30–11:30** — Coding / Algorithms
- **Wednesday 8:00–9:00** — System Design
- **Thursday 10:00–11:00** — Behavioral / Review
- **Saturday 9:00–10:30** — Extra Coding (DP, weak areas, re-solves) + OOD/Concurrency

**Problem target:** ~80 LeetCode problems across all major patterns by Week 12

## Resources

### Coding
- [LeetCode](https://leetcode.com/) — Practice problems
- [NeetCode 150](https://neetcode.io/practice) — Curated 150 problems covering all patterns
- [NeetCode Roadmap](https://neetcode.io/roadmap) — Visual topic map and video explanations
- [Big-O Cheat Sheet](https://www.bigocheatsheet.com/) — Time/space complexity reference
- [Algorithm Patterns Cheat Sheet](algorithm-patterns.md) — Code templates for 12 core patterns

### System Design
- [System Design Primer](https://github.com/donnemartin/system-design-primer) — Comprehensive guide
- [ByteByteGo](https://bytebytego.com/) — System design fundamentals
- [Designing Data-Intensive Applications](https://dataintensive.net/) — Book by Martin Kleppmann
- [System Design Templates](system-design/templates.md) — Reusable frameworks and component reference

### Behavioral
- [STAR Method Guide](https://www.themuse.com/advice/star-interview-method) — How to structure answers
- [Amazon Leadership Principles](https://www.amazon.jobs/content/en/our-workplace/leadership-principles) — Common behavioral themes
- [Tech Interview Handbook — Behavioral](https://www.techinterviewhandbook.org/behavioral-interview/) — Question bank and prep guide

### Object-Oriented Design
- [OOD Interview Questions](https://github.com/tssovi/grokking-the-object-oriented-design-interview) — Common OOD problems
- SOLID Principles — Single Responsibility, Open-Closed, Liskov Substitution, Interface Segregation, Dependency Inversion

### Concurrency & Multithreading
- [Java Concurrency in Practice](https://jcip.net/) — Foundational concepts (applicable to any language)
- Race conditions, deadlocks, producer-consumer, thread pools, locks vs lock-free structures

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

**Stories Every Senior Engineer Needs (10 minimum for Big Tech)**

| Theme | Question Types | # Stories Needed |
|-------|----------------|-----------------|
| **Technical Leadership** | Led architecture decision, introduced new technology, solved complex bug | 2 |
| **Conflict Resolution** | Disagreed with teammate/manager, handled difficult stakeholder | 1-2 |
| **Failure & Learning** | Made mistake, missed deadline, project failed | 1-2 |
| **Mentorship & Team Growth** | Helped junior grow, conducted code reviews, knowledge sharing, dealt with underperformer | 1-2 |
| **Ambiguity & Decision-Making** | Unclear requirements, changing priorities, made decisions with incomplete data | 1-2 |
| **Cross-Team Influence** | Drove adoption across teams, aligned competing priorities, pushed back on product decisions | 1-2 |
| **Impact & Scale** | Improved performance, saved costs, delivered key feature, worked on large-scale system | 1-2 |

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
**Goal:** Warm-up, establish baseline — Arrays & Hashing

- [x] Solve [Two Sum](https://leetcode.com/problems/two-sum/)
- [x] Solve without IDE (paper or notes first)
- [x] Implement clean solution
- [x] Write time complexity
- [x] Write space complexity
- [x] Describe one alternative approach
- [x] (Optional) Solve [Valid Anagram](https://leetcode.com/problems/valid-anagram/)
- [ ] Solve [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) — HashMap pattern reinforcement
- [ ] Solve [Group Anagrams](https://leetcode.com/problems/group-anagrams/) — HashMap with custom keys
- [ ] **Pattern note:** Write when to use HashMap vs sorting for lookup problems

---

### Wednesday — System Design
**Goal:** Learn design structure

- [x] Write design framework (requirements, APIs, data model, scaling)
- [x] Design **URL Shortener** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-tinyurl)
- [x] Draw components
- [x] Draw data flow
- [ ] List 5 key trade-offs
- [ ] Discuss hash collision strategies (base62 encoding vs pre-generated keys)
- [ ] Explain read-heavy optimization (caching layer, CDN for redirects)

---

### Thursday — Behavioral
**Goal:** Start STAR stories

- [x] Write STAR: Production incident
- [x] Write STAR: Conflict with teammate
- [x] Limit each story to 8–10 sentences
- [ ] Identify 1 weak answer

### Saturday — Extra Coding
**Goal:** Linked Lists fundamentals

- [ ] Solve [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) — Iterative and recursive
- [ ] Solve [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
- [ ] Solve [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) — Fast/slow pointers
- [ ] **Pattern note:** Write when to use fast/slow pointer technique

---

## Week 2

### Monday — Coding
**Goal:** Two Pointers & Sliding Window

- [x] Solve [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [x] Explain sliding window out loud — [Pattern Guide](https://leetcode.com/discuss/study-guide/657507/Sliding-Window-for-Beginners-Problems-or-Template-or-Sample-Solutions)
- [x] List edge cases
- [ ] Re-solve in under 20 minutes
- [ ] Solve [3Sum](https://leetcode.com/problems/3sum/) — Two pointers on sorted array
- [ ] Solve [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) — Greedy two pointers
- [ ] Solve [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) — Advanced sliding window (Hard)
- [ ] **Pattern note:** Write the sliding window template (expand right, shrink left, track answer)

---

### Wednesday — System Design
**Goal:** Rate limiting & distributed coordination

- [x] Design **Rate Limiter** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/design-of-a-rate-limiter)
- [x] Compare token bucket vs leaky bucket — [Article](https://www.figma.com/blog/an-alternative-approach-to-rate-limiting/)
- [x] Decide where rate limiting lives (gateway vs service)
- [ ] Discuss distributed rate limiting with Redis (race conditions, Lua scripts)
- [ ] Explain sliding window log vs fixed window counter trade-offs

---

### Thursday — Behavioral
**Goal:** Ownership & failure

- [x] Write STAR: Big mistake
- [x] Describe how it was fixed
- [x] Answer: What would you do differently?

### Saturday — Extra Coding
**Goal:** More Linked Lists + re-solves

- [ ] Solve [Reorder List](https://leetcode.com/problems/reorder-list/) — Combines find-middle + reverse + merge
- [ ] Solve [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) — Two pointers
- [ ] Re-solve Week 1 problems you found hardest without notes (target: < 20 min each)

---

## Week 3

### Monday — Coding
**Goal:** Trees & Binary Search

- [x] Solve [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [x] Implement BFS — [BFS Guide](https://leetcode.com/explore/learn/card/data-structure-tree/134/traverse-a-tree/931/)
- [x] Explain why DFS is less suitable
- [ ] Solve [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) — Inorder traversal / range validation
- [ ] Solve [Binary Search](https://leetcode.com/problems/binary-search/) — Master the template
- [ ] Solve [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/) — Modified binary search
- [ ] **Pattern note:** Write the binary search template (left, right, mid, when to move which pointer)

---

### Wednesday — System Design
**Goal:** Async processing & notification systems

- [x] Design **Notification System** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-web-crawler)
- [x] Describe fan-out strategy
- [x] Define retry handling
- [x] Handle deduplication
- [ ] Compare fan-out-on-write vs fan-out-on-read (and hybrid approach)
- [ ] Design priority queues for urgent vs non-urgent notifications
- [ ] Discuss exactly-once delivery guarantees and idempotency keys

---

### Thursday — Behavioral
**Goal:** Disagreement & influence

- [x] Answer: Disagree and commit
- [x] Explain how you influenced the decision
- [x] Describe the outcome

### Saturday — Extra Coding
**Goal:** Binary search deep dive + Trie introduction

- [ ] Solve [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) — Binary search on answer
- [ ] Solve [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] Solve [Implement Trie](https://leetcode.com/problems/implement-trie-prefix-tree/) — Prefix tree data structure
- [ ] **Pattern note:** Write "binary search on answer" template

---

## Week 4

### Monday — Coding
**Goal:** Recursion & Backtracking

- [x] Solve [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [x] Draw recursion tree
- [x] Identify base cases
- [ ] Solve [Subsets](https://leetcode.com/problems/subsets/) — Backtracking fundamentals
- [ ] Solve [Combination Sum](https://leetcode.com/problems/combination-sum/) — Backtracking with pruning
- [ ] Solve [Word Search](https://leetcode.com/problems/word-search/) — Backtracking on grid
- [ ] **Pattern note:** Write the backtracking template (choose → explore → un-choose)

---

### Wednesday — System Design
**Goal:** Storage systems & consistency

- [x] Design **File Storage Service** — [Dropbox Design](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-google-docs)
- [x] Separate metadata and blobs
- [x] Handle versioning
- [ ] Define access control
- [ ] Discuss chunking strategy for large files (dedup, delta sync)
- [ ] Explain conflict resolution for concurrent edits (OT vs CRDT)
- [ ] **Concept deep dive:** Study CAP theorem trade-offs — [CAP Explained](https://www.ibm.com/topics/cap-theorem)

---

### Thursday — Behavioral
**Goal:** Mentorship & team dynamics

- [x] Write STAR: Mentoring a junior engineer
- [x] Describe impact on the team
- [ ] Write STAR: Dealing with an underperformer or difficult team dynamic
- [ ] Write STAR: Pushed back on a product/business decision — explain data you used

### Saturday — Extra Coding
**Goal:** DP introduction (start early — hardest topic)

- [ ] Solve [House Robber](https://leetcode.com/problems/house-robber/) — Linear DP
- [ ] Solve [Unique Paths](https://leetcode.com/problems/unique-paths/) — Grid DP
- [ ] Solve [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) — Kadane's algorithm
- [ ] Study [DP Patterns Guide](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns) — Read first 3 patterns
- [ ] **Pattern note:** For each problem, write: state definition, recurrence, base case

---

## Week 5

### Monday — Coding
**Goal:** Graphs & Union-Find

- [x] Solve [Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [x] Explain graph modeling — [Graph Guide](https://leetcode.com/explore/learn/card/graph/)
- [x] Compare BFS vs DFS
- [ ] Solve [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/) — Multi-source BFS
- [ ] Solve [Redundant Connection](https://leetcode.com/problems/redundant-connection/) — Union-Find pattern
- [ ] **Pattern note:** Write Union-Find template (find with path compression, union by rank)

---

### Wednesday — System Design
**Goal:** Event-driven & streaming systems

- [x] Design **Ad Click Aggregation Service** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/distributed-logging)
- [x] Define message queue
- [ ] Describe consumers
- [ ] Handle backpressure
- [ ] Compare Kafka vs RabbitMQ for this use case — [Comparison](https://www.confluent.io/learn/kafka-vs-rabbitmq/)
- [ ] Design windowed aggregation (tumbling vs sliding windows)
- [ ] **Concept deep dive:** Study event sourcing and CQRS patterns

---

### Thursday — Behavioral
**Goal:** Leadership signals & cross-team influence

- [ ] Answer: Handling vague requirements
- [ ] Emphasize structure and decision-making
- [ ] Write STAR: Drove a cross-team initiative or influenced without authority
- [ ] Write STAR: Made a technical decision with incomplete information — trade-offs

### Saturday — Extra Coding
**Goal:** More DP + Graph shortest path

- [ ] Solve [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) — 2D DP
- [ ] Solve [Word Break](https://leetcode.com/problems/word-break/) — DP with string
- [ ] Solve [Network Delay Time](https://leetcode.com/problems/network-delay-time/) — Dijkstra's algorithm
- [ ] **Pattern note:** Write Dijkstra template with priority queue

---

## Week 6

### Monday — Coding
**Goal:** Dynamic Programming & Topological Sort

- [ ] Solve [Course Schedule](https://leetcode.com/problems/course-schedule/) — Topological sort / cycle detection
- [ ] Explain cycle detection — [Topological Sort](https://leetcode.com/discuss/study-guide/1786329/topological-sort-for-beginners)
- [ ] Identify failure cases
- [ ] Solve [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) — DP fundamentals
- [ ] Solve [Coin Change](https://leetcode.com/problems/coin-change/) — Classic DP (unbounded knapsack)
- [ ] Solve [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) — 1D DP with binary search optimization
- [ ] **Pattern note:** Write DP framework: 1) Define state, 2) Recurrence relation, 3) Base case, 4) Order of computation
- [ ] Study [DP Patterns Guide](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns) — Identify the 5 main DP patterns

---

### Wednesday — System Design
**Goal:** Real-world system & data pipeline

- [ ] Design **IoT Data Ingestion System**
- [ ] Handle throughput — [Time Series DB](https://www.influxdata.com/time-series-database/)
- [ ] Ensure idempotency
- [ ] Plan horizontal scaling
- [ ] Design the ingestion pipeline: devices → gateway → queue → processors → storage
- [ ] Discuss time-series partitioning and retention policies
- [ ] **Concept deep dive:** Study consistent hashing for partition assignment — [Guide](https://www.toptal.com/big-data/consistent-hashing)

---

### Thursday — Behavioral
**Goal:** Handling pressure & impact at scale

- [ ] Answer: Most stressful situation
- [ ] Explain prioritization and reasoning
- [ ] Write STAR: Improved system reliability/performance at scale — with metrics

### Saturday — Extra Coding
**Goal:** DP deep dive — knapsack patterns

- [ ] Solve [Coin Change](https://leetcode.com/problems/coin-change/) — Unbounded knapsack (re-solve if already done Monday)
- [ ] Solve [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) — 0/1 knapsack
- [ ] Solve [Target Sum](https://leetcode.com/problems/target-sum/) — DP with offset or HashMap
- [ ] Solve [Edit Distance](https://leetcode.com/problems/edit-distance/) — Classic 2D DP (Hard)
- [ ] **Pattern note:** Compare unbounded vs 0/1 knapsack — when to iterate coins inner vs outer

---

## Week 7 — Heap, Stack & Mock Interviews

### Monday — Coding
**Goal:** Heaps, Stacks & Intervals + Mock

- [ ] Solve [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) — Heap / bucket sort
- [ ] Solve [Merge Intervals](https://leetcode.com/problems/merge-intervals/) — Interval pattern
- [ ] Solve [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) — Stack fundamentals
- [ ] Solve [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) — Monotonic stack
- [ ] Timed mock (45 min): [Clone Graph](https://leetcode.com/problems/clone-graph/)
- [ ] 15 minutes reviewing mistakes and writing down patterns used

---

### Wednesday — System Design
**Goal:** Full mock — Chat / Real-time system

- [ ] Full mock: **Design a Chat System (WhatsApp/Slack)**
- [ ] Address: WebSocket connections, presence service, message storage
- [ ] Discuss: message ordering guarantees, read receipts, group chat fan-out
- [ ] Full mock: **Real-time Tracking System**
- [ ] Identify missed risks
- [ ] Identify overengineering
- [ ] **Practice:** Do both designs under 45-minute time constraint

---

### Thursday — Behavioral
- [ ] Answer 5 random behavioral questions — [Question List](https://www.techinterviewhandbook.org/behavioral-interview-questions/)
- [ ] Speak only (no writing)
- [ ] Practice "Why are you leaving?" and "Why this company?" — keep each under 60 seconds

### Saturday — Extra Coding
**Goal:** Design-a-data-structure problems (very common at Big Tech)

- [ ] Solve [Min Stack](https://leetcode.com/problems/min-stack/) — Stack with O(1) getMin
- [ ] Solve [Design HashMap](https://leetcode.com/problems/design-hashmap/) — Hash function + collision handling
- [ ] Solve [Design Twitter](https://leetcode.com/problems/design-twitter/) — Combines OOD + heap + HashMap
- [ ] **Pattern note:** For data structure design, always clarify: which operations, what complexity target

---

## Week 8 — Advanced Patterns & Polish

### Monday — Coding
**Goal:** Fill gaps & re-solve hardest problems

- [ ] Solve [LRU Cache](https://leetcode.com/problems/lru-cache/) — HashMap + Doubly Linked List (very common in interviews)
- [ ] Solve [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) — Binary search (Hard)
- [ ] Re-solve the 3 hardest problems from previous weeks without looking at notes
- [ ] Focus on explanation clarity — record yourself explaining one solution
- [ ] **Final review:** Go through all pattern notes written in weeks 1-7

---

### Wednesday — System Design
**Goal:** Review & prepare reusable templates

- [ ] Review top 3 system designs from previous weeks
- [ ] Prepare default architecture template — [Template](https://github.com/donnemartin/system-design-primer#system-design-interview-questions-with-solutions)
- [ ] Design **Search Autocomplete / Typeahead** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-the-typeahead-suggestion-system) — Trie + caching pattern
- [ ] Prepare a "go-to" component list you can draw from memory (see [system-design/templates.md](system-design/templates.md))
- [ ] **Final review:** Practice explaining any previous design in 5 minutes (elevator pitch)

---

### Thursday — Behavioral
- [ ] Finalize "Tell me about yourself" — [Guide](https://www.techinterviewhandbook.org/self-introduction/)
- [ ] Finalize top 5 STAR stories
- [ ] Practice answering "Where do you see yourself in 3-5 years?"

### Saturday — Extra Coding
**Goal:** OOD introduction (Amazon/Microsoft round)

- [ ] Design **Parking Lot** — Classes, inheritance, interfaces. Think SOLID principles
- [ ] Design **Elevator System** — State machine, scheduling algorithm, concurrency
- [ ] Study SOLID principles — Write one-sentence summary of each
- [ ] **Pattern note:** OOD approach: 1) Clarify use cases, 2) Identify core objects, 3) Define relationships, 4) Walk through scenarios

---

## Week 9 — Concurrency & Advanced DP

### Monday — Coding
**Goal:** Concurrency & multithreading (commonly asked at senior level)

- [ ] Study: Race conditions, deadlocks, producer-consumer pattern, thread safety
- [ ] Solve [Print in Order](https://leetcode.com/problems/print-in-order/) — Basic synchronization
- [ ] Solve [Print FooBar Alternately](https://leetcode.com/problems/print-foobar-alternately/) — Thread coordination
- [ ] Solve [The Dining Philosophers](https://leetcode.com/problems/the-dining-philosophers/) — Deadlock avoidance
- [ ] **Study note:** Write common concurrency bugs and how to prevent them (mutex, semaphore, condition variable)

---

### Wednesday — System Design
**Goal:** Storage internals — Distributed Key-Value Store

- [ ] Design **Distributed Key-Value Store** (like DynamoDB/Redis cluster)
- [ ] Discuss: consistent hashing, replication factor, quorum reads/writes (W + R > N)
- [ ] Address: node failure and recovery, anti-entropy with Merkle trees
- [ ] Discuss: gossip protocol for membership, vector clocks for conflict resolution
- [ ] Compare: strong consistency (Raft/Paxos) vs eventual consistency (Dynamo-style)

---

### Thursday — Behavioral
**Goal:** Senior-level leadership polish

- [ ] Refine all stories: ensure each has specific metrics and clear "I" ownership
- [ ] Practice: "Tell me about a system you built end-to-end" (covers architecture + execution)
- [ ] Practice: "How do you make technical decisions with incomplete data?"

### Saturday — Extra Coding
**Goal:** Advanced DP — intervals & trees

- [ ] Solve [Burst Balloons](https://leetcode.com/problems/burst-balloons/) — Interval DP (Hard)
- [ ] Solve [Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/) — Stack or DP
- [ ] Solve [Decode Ways](https://leetcode.com/problems/decode-ways/) — Linear DP with conditions
- [ ] Re-solve 2 DP problems from weeks 4-6 without notes

---

## Week 10 — News Feed & Full Mocks

### Monday — Coding
**Goal:** Mixed pattern timed practice (simulate real interviews)

- [ ] Timed (25 min): [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
- [ ] Timed (25 min): [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) (Hard)
- [ ] Timed (30 min): [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) (Hard)
- [ ] Review: Which patterns did you recognize? Which did you miss?

---

### Wednesday — System Design
**Goal:** Social media system — News Feed

- [ ] Design **News Feed / Timeline** (Facebook/Twitter)
- [ ] Discuss: fan-out-on-write vs fan-out-on-read vs hybrid approach for celebrities
- [ ] Design: ranking algorithm (chronological vs relevance-based)
- [ ] Address: caching strategy for timeline, pre-computed feed vs on-demand
- [ ] Handle: multimedia content delivery, CDN integration
- [ ] **Practice:** Present this design in exactly 35 minutes (leave 10 min for Q&A)

---

### Thursday — Behavioral
**Goal:** Full mock behavioral round

- [ ] Do full 45-min mock: answer 6-8 questions back-to-back (timer running)
- [ ] Record yourself or practice with a partner
- [ ] Self-review: Were answers under 3 min each? Did each have metrics?

### Saturday — Extra Coding
**Goal:** OOD round practice

- [ ] Design **Library Management System** — CRUD, search, reservations, late fees
- [ ] Design **Online Chess Game** — Game state, move validation, multiplayer
- [ ] For each: write class diagram, identify design patterns used (Strategy, Observer, Factory)

---

## Week 11 — Advanced System Design & Gaps

### Monday — Coding
**Goal:** Fill gaps — problems you've struggled with most

- [ ] Re-solve your 5 hardest problems from Weeks 1-10 without any notes
- [ ] Target: solve each in < 25 min
- [ ] For any you can't solve: study solution → write the pattern → schedule re-solve in Week 12
- [ ] Solve [Word Ladder](https://leetcode.com/problems/word-ladder/) — BFS on implicit graph (Hard)

---

### Wednesday — System Design
**Goal:** Design-your-own-system (most realistic senior-level question)

- [ ] Pick a system you've actually built professionally
- [ ] Redesign it from scratch using interview framework (45 min, timed)
- [ ] Identify: what would you do differently now? What trade-offs did you make?
- [ ] Prepare to discuss: why certain technologies were chosen, what you'd change at 100x scale
- [ ] Also prep: **API Gateway / Service Mesh** design — routing, auth, rate limiting, circuit breaking

---

### Thursday — Behavioral
**Goal:** Company-specific prep

- [ ] Research target company's values/principles (Amazon LPs, Google's Googleyness, Meta's core values)
- [ ] Map your 10+ stories to company-specific themes
- [ ] Prepare 2-3 thoughtful questions for each interview round ("reverse interview")
- [ ] Practice: "Why [company name]?" — specific, genuine, researched answer

### Saturday — Extra Coding
**Goal:** Concurrency + advanced data structures

- [ ] Solve [LFU Cache](https://leetcode.com/problems/lfu-cache/) — HashMap + doubly linked lists (Hard)
- [ ] Solve [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) — Two heaps
- [ ] Solve [Merge K Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) — Heap + linked list
- [ ] Study: Compare LRU vs LFU vs ARC cache eviction — when to use each

---

## Week 12 — Final Polish & Mock Interviews

### Monday — Coding
**Goal:** Full interview simulation

- [ ] Complete mock: 2 problems in 45 minutes (pick randomly from unsolved NeetCode 150)
- [ ] Speak your approach out loud the entire time
- [ ] Record and review: Did you talk through trade-offs? Edge cases? Complexity?
- [ ] Final review: Go through all pattern notes from [algorithm-patterns.md](algorithm-patterns.md)

---

### Wednesday — System Design
**Goal:** Full mock interview simulation

- [ ] Have someone (or ChatGPT/Claude) give you a random system design problem
- [ ] Complete full design in 40 minutes (leave 5 min for questions)
- [ ] Review: Did you cover all 7 phases? Did you discuss trade-offs? Failure modes?
- [ ] Quick review all designs from [system-design/templates.md](system-design/templates.md)

---

### Thursday — Behavioral
**Goal:** Final polish

- [ ] Record "Tell me about yourself" — must be exactly 60-90 seconds
- [ ] Run through all 10+ STAR stories — each under 3 minutes
- [ ] Prepare closing questions for each interview round
- [ ] Mental prep: Visualize interview day, plan logistics, get sleep

### Saturday — Extra Coding
**Goal:** Confidence builder — victory lap

- [ ] Re-solve 5 favorite problems (ones you know well) to build confidence
- [ ] Review your mistake log — any recurring patterns?
- [ ] Skim through all pattern notes one final time
- [ ] **You're ready.** Trust your preparation.

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
| Linked Lists | Week 1-2 (Sat) | [Linked List Guide](https://leetcode.com/explore/learn/card/linked-list/) |
| Two Pointers | Week 2 | [Patterns Guide](https://leetcode.com/discuss/study-guide/1688903/Solved-all-two-pointers-problems-in-100-days) |
| Sliding Window | Week 2 | [Template + Problems](https://leetcode.com/discuss/study-guide/657507/Sliding-Window-for-Beginners-Problems-or-Template-or-Sample-Solutions) |
| Binary Search | Week 3 | [Ultimate Template](https://leetcode.com/discuss/study-guide/786126/Python-Powerful-Ultimate-Binary-Search-Template) |
| Trees | Week 3-4 | [Tree Patterns](https://leetcode.com/explore/learn/card/data-structure-tree/) |
| Backtracking | Week 4 | [Backtracking Template](https://leetcode.com/discuss/study-guide/1405817/Backtracking-algorithm-%2B-problems-to-practice) |
| Graphs & Union-Find | Week 5-6 | [Graph for Beginners](https://leetcode.com/explore/learn/card/graph/) |
| Dynamic Programming | Week 4-6 (Sat), Week 6, 9 | [DP Study Guide](https://leetcode.com/discuss/study-guide/1433252/Dynamic-Programming-Patterns) |
| Heap & Priority Queue | Week 7 | [Heap Guide](https://leetcode.com/discuss/study-guide/1360400/Priority-Queue-oror-Problems-oror-Template-oror-All-in-One) |
| Monotonic Stack | Week 7 | [Stack Problems](https://leetcode.com/discuss/study-guide/2347639/A-comprehensive-guide-and-template-for-monotonic-stack-based-problems) |
| Concurrency | Week 9 | [Concurrency Problems](https://leetcode.com/problemset/concurrency/) |
| OOD / Data Structure Design | Week 7-8 (Sat) | [OOD Questions](https://github.com/tssovi/grokking-the-object-oriented-design-interview) |

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

**Stories You Must Have Ready (Big Tech expects 10+)**

- [ ] Tell me about yourself (60-90 seconds)
- [ ] Biggest technical challenge you've solved
- [ ] Time you failed / made a mistake — and what you learned
- [ ] Conflict with teammate or manager — how you resolved it
- [ ] Led a project or initiative end-to-end
- [ ] Mentored someone — specific growth you enabled
- [ ] Handled ambiguous requirements — how you created structure
- [ ] Pushed back on a product/business decision with data
- [ ] Drove a cross-team initiative or influenced without authority
- [ ] Dealt with an underperformer or difficult team dynamic
- [ ] Made a technical decision with incomplete information — trade-offs you weighed
- [ ] Improved system reliability / performance at scale — with metrics
- [ ] Why are you leaving current job?
- [ ] Why do you want this role?
- [ ] Where do you see yourself in 3-5 years?

---

## Weekly Review Process

Every **Friday (15 min)** — assess the week and adjust:

### Review Checklist

- [ ] Did I complete all 4 sessions this week?
- [ ] Which session was hardest? Why?
- [ ] What pattern/concept am I still weak on?
- [ ] Do I need to add extra practice for any topic?
- [ ] How many total problems have I solved? (target: ~7/week)

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

| Week | Problems Solved | Coding (1-5) | System Design (1-5) | Behavioral (1-5) | Notes |
|------|----------------|--------------|---------------------|-------------------|-------|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
| 6 | | | | | |
| 7 | | | | | |
| 8 | | | | | |
| 9 | | | | | |
| 10 | | | | | |
| 11 | | | | | |
| 12 | | | | | |

---

## Emergency Prep (If Interview is Sooner)

If you have less than 12 weeks, focus on **highest impact items**:

### 1 Week Available
- [ ] Solve: Two Sum, Reverse Linked List, LCA, Merge Intervals, LRU Cache, Coin Change, Number of Islands
- [ ] Design: URL Shortener only (nail the framework — hit all 7 phases)
- [ ] Behavioral: 5 STAR stories + "Tell me about yourself"
- [ ] Review [algorithm-patterns.md](algorithm-patterns.md) and [system-design/templates.md](system-design/templates.md)

### 2 Weeks Available
- [ ] Solve: Two Sum, 3Sum, Longest Substring, Level Order Traversal, Validate BST, Number of Islands, Coin Change, LRU Cache, Merge Intervals, Top K Frequent, Course Schedule
- [ ] Design: URL Shortener, Rate Limiter, News Feed (one per day)
- [ ] Behavioral: 8 STAR stories covering all themes + "Tell me about yourself"
- [ ] Study pattern recognition table + system design building blocks

### 4 Weeks Available
- [ ] Complete Weeks 1, 2, 5, 6 from this plan (all sessions including Saturday)
- [ ] Add mock interviews in final 3 days
- [ ] Review [algorithm-patterns.md](algorithm-patterns.md) and [system-design/templates.md](system-design/templates.md)
- [ ] Write all 10+ STAR stories — practice out loud

### 8 Weeks Available
- [ ] Complete Weeks 1-8 from this plan (all sessions including Saturday)
- [ ] Skip Weeks 9-10, go directly to Week 11-12 (polish and mocks)
- [ ] Ensure 60+ problems solved across all patterns
- [ ] Do at least 2 full mock interviews (coding + design + behavioral)

---

## Rules

- [ ] Stop exactly at the end of each time block
- [ ] No multitasking
- [ ] Notes over memorization
- [ ] Consistency over intensity
