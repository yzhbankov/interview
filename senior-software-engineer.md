# Interview Preparation Plan (8 Weeks)

**Target:** Senior Software Engineer at Big Tech (FAANG-level)

**Schedule**
- **Monday 10:30–11:30** — Coding / Algorithms
- **Tuesday 10:30–11:30** — Coding / Algorithms (second session)
- **Wednesday 8:00–9:00** — System Design
- **Thursday 10:00–11:00** — Behavioral / Review
- **Saturday 10:00–11:30** — Weekly mock interview (coding or system design, alternating)

> **Why 5 sessions?** Big tech interviews require ~100+ problems solved and 8-10 system designs practiced. 3 hrs/week is not enough to build pattern fluency. The Saturday mock builds interview stamina early — don't wait until Week 7.

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
**Goal:** Warm-up, establish baseline — Arrays & Hashing

- [x] Solve [Two Sum](https://leetcode.com/problems/two-sum/)
- [x] Solve without IDE (paper or notes first)
- [x] Implement clean solution
- [x] Write time complexity
- [x] Write space complexity
- [x] Describe one alternative approach
- [x] (Optional) Solve [Valid Anagram](https://leetcode.com/problems/valid-anagram/)
- [x] Solve [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) — HashMap pattern reinforcement
- [x] Solve [Group Anagrams](https://leetcode.com/problems/group-anagrams/) — HashMap with custom keys
- [x] **Pattern note:** Write when to use HashMap vs sorting for lookup problems

### Tuesday — Coding
**Goal:** Arrays continued — prefix sums, Kadane's

- [x] Solve [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/) — prefix/suffix pattern
- [x] Solve [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) — Kadane's algorithm
- [x] Solve [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) — single pass min tracking

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

### Saturday — Mock
- [ ] Timed coding mock (45 min): solve 2 medium problems back-to-back, explain out loud

---

## Week 2

### Monday — Coding
**Goal:** Two Pointers & Sliding Window

- [x] Solve [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [x] Explain sliding window out loud — [Pattern Guide](https://leetcode.com/discuss/study-guide/657507/Sliding-Window-for-Beginners-Problems-or-Template-or-Sample-Solutions)
- [x] List edge cases
- [x] Re-solve in under 20 minutes
- [x] Solve [3Sum](https://leetcode.com/problems/3sum/) — Two pointers on sorted array
- [x] Solve [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) — Greedy two pointers
- [ ] Solve [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) — Advanced sliding window (Hard)
- [ ] **Pattern note:** Write the sliding window template (expand right, shrink left, track answer)

### Tuesday — Coding
**Goal:** Linked Lists — critical gap for big tech

- [x] Solve [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) — Must-know iterative + recursive
- [x] Solve [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/) — Merge pattern
- [x] Solve [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) — Floyd's cycle detection (fast/slow pointers)
- [x] Solve [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) — Two-pointer gap technique
- [ ] **Pattern note:** Write linked list pointer manipulation patterns (prev/curr/next)

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

### Saturday — Mock
- [ ] Timed system design mock (45 min): Design a URL Shortener from scratch without notes

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

### Tuesday — Coding
**Goal:** More Trees — these come up constantly at Google/Meta

- [ ] Solve [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) — Recursive fundamentals
- [ ] Solve [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) — DFS base case practice
- [ ] Solve [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) — Hard, very common at big tech
- [ ] Solve [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) — Inorder traversal application

---

### Wednesday — System Design
**Goal:** Async processing & notification systems

- [x] Design **Notification System** — [Guide](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers/system-design-the-notification-service)
- [x] Describe fan-out strategy
- [x] Define retry handling
- [x] Handle deduplication
- [ ] Compare fan-out-on-write vs fan-out-on-read (and hybrid approach)
- [ ] Design priority queues for urgent vs non-urgent notifications
- [ ] Discuss exactly-once delivery guarantees and idempotency keys

> **Note:** The original guide link was pointing to the web crawler design — fixed above.

---

### Thursday — Behavioral
**Goal:** Disagreement & influence

- [x] Answer: Disagree and commit
- [x] Explain how you influenced the decision
- [x] Describe the outcome

### Saturday — Mock
- [ ] Timed coding mock (45 min): 1 medium tree problem + 1 medium binary search problem, no hints

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

### Tuesday — Coding
**Goal:** Strings & Intervals — high frequency at big tech

- [ ] Solve [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) — Two pointers on strings
- [ ] Solve [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) — Expand from center
- [ ] Solve [Merge Intervals](https://leetcode.com/problems/merge-intervals/) — Sorting + merge pattern
- [ ] Solve [Insert Interval](https://leetcode.com/problems/insert-interval/) — Interval manipulation

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
**Goal:** Mentorship

- [x] Write STAR: Mentoring a junior engineer
- [x] Describe impact on the team
- [ ] Write STAR: Led a technical decision that others initially disagreed with
- [ ] Prepare "Why do you want to work at [target company]?" answer

### Saturday — Mock
- [ ] Timed system design mock (45 min): Design a Rate Limiter from scratch without notes

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

### Tuesday — Coding
**Goal:** Graph shortest path & topological sort — Google/Meta favorites

- [ ] Solve [Course Schedule](https://leetcode.com/problems/course-schedule/) — Topological sort / cycle detection
- [ ] Solve [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) — Build the ordering
- [ ] Solve [Network Delay Time](https://leetcode.com/problems/network-delay-time/) — Dijkstra's algorithm (weighted graph)
- [ ] Solve [Clone Graph](https://leetcode.com/problems/clone-graph/) — BFS/DFS with HashMap for visited
- [ ] **Pattern note:** Write Dijkstra template (priority queue + distances dict)

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
**Goal:** Leadership signals

- [ ] Answer: Handling vague requirements
- [ ] Emphasize structure and decision-making
- [ ] Write STAR: Time you drove a project with unclear scope to completion
- [ ] Prepare answers for "What is your biggest weakness?" (have a genuine, non-cliché answer)

### Saturday — Mock
- [ ] Timed coding mock (45 min): 1 graph + 1 backtracking problem, explain approach before coding

---

## Week 6

### Monday — Coding
**Goal:** Dynamic Programming — the hardest pattern, needs dedicated focus

- [ ] Solve [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) — DP fundamentals
- [ ] Solve [Coin Change](https://leetcode.com/problems/coin-change/) — Classic DP (unbounded knapsack)
- [ ] Solve [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) — 1D DP with binary search optimization
- [ ] **Pattern note:** Write DP framework: 1) Define state, 2) Recurrence relation, 3) Base case, 4) Order of computation
- [ ] Study [DP Patterns Guide](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns) — Identify the 5 main DP patterns

### Tuesday — Coding
**Goal:** DP continued — 2D DP and string DP (very common at big tech)

- [ ] Solve [Unique Paths](https://leetcode.com/problems/unique-paths/) — Grid DP
- [ ] Solve [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) — 2D string DP
- [ ] Solve [Edit Distance](https://leetcode.com/problems/edit-distance/) — Hard, classic big tech problem
- [ ] Solve [House Robber](https://leetcode.com/problems/house-robber/) — Decision DP (take or skip)
- [ ] Solve [Word Break](https://leetcode.com/problems/word-break/) — DP + HashSet

---

### Wednesday — System Design
**Goal:** News Feed — one of the most asked designs at Meta/Google/Amazon

- [ ] Design **News Feed System (Instagram/Twitter)** — [Guide](https://github.com/donnemartin/system-design-primer#design-the-twitter-timeline-and-search)
- [ ] Compare fan-out-on-write vs fan-out-on-read vs hybrid
- [ ] Design the feed ranking/scoring service
- [ ] Handle celebrity problem (users with millions of followers)
- [ ] Discuss cache warming and feed pre-computation
- [ ] Address feed pagination and real-time updates
- [ ] **Concept deep dive:** Study consistent hashing for partition assignment — [Guide](https://www.toptal.com/big-data/consistent-hashing)

---

### Thursday — Behavioral
**Goal:** Handling pressure & cross-team collaboration

- [ ] Answer: Most stressful situation — explain prioritization and reasoning
- [ ] Write STAR: Time you had to coordinate across multiple teams
- [ ] Write STAR: Time you had to push back on unrealistic deadlines
- [ ] Prepare "Questions to ask the interviewer" list (5-10 strong questions)

### Saturday — Mock
- [ ] Timed system design mock (45 min): Design a News Feed from scratch

---

## Week 7 — Heap, Stack, OOD & Mock Interviews

### Monday — Coding
**Goal:** Heaps & Stacks — pattern mastery

- [ ] Solve [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) — Heap / bucket sort
- [ ] Solve [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) — Two heaps (Hard, common at Amazon/Google)
- [ ] Solve [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) — Stack fundamentals
- [ ] Solve [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) — Monotonic stack
- [ ] Solve [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/) — Monotonic stack (Hard)

### Tuesday — Coding
**Goal:** LRU Cache & Object-Oriented Design — Amazon/Google favorites

- [ ] Solve [LRU Cache](https://leetcode.com/problems/lru-cache/) — HashMap + Doubly Linked List (asked at almost every big tech company)
- [ ] Solve [Min Stack](https://leetcode.com/problems/min-stack/) — Design with constraints
- [ ] Solve [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/) — Data structure design
- [ ] Timed mock (45 min): pick 2 random mediums from previous weeks, solve without notes
- [ ] 15 minutes reviewing mistakes and writing down patterns used

---

### Wednesday — System Design
**Goal:** Full mock — Chat system + Distributed Key-Value Store

- [ ] Full mock: **Design a Chat System (WhatsApp/Slack)**
- [ ] Address: WebSocket connections, presence service, message storage
- [ ] Discuss: message ordering guarantees, read receipts, group chat fan-out
- [ ] Full mock: **Design a Distributed Key-Value Store**
- [ ] Cover: consistent hashing, replication, conflict resolution (vector clocks), gossip protocol
- [ ] Discuss: read/write quorum (W + R > N), tunable consistency
- [ ] **Practice:** Do both designs under 45-minute time constraint each

---

### Thursday — Behavioral
- [ ] Answer 5 random behavioral questions — [Question List](https://www.techinterviewhandbook.org/behavioral-interview-questions/)
- [ ] Speak only (no writing) — record yourself and review
- [ ] Practice "Tell me about yourself" — time to under 90 seconds
- [ ] Practice "Why are you leaving your current job?" — keep positive, forward-looking

### Saturday — Mock
- [ ] Full mock interview simulation: 1 coding (45 min) + 1 system design (45 min) + 1 behavioral (30 min) back-to-back

---

## Week 8 — Advanced Patterns & Polish

### Monday — Coding
**Goal:** Hard problems & re-solves — build confidence on hard-tier

- [ ] Solve [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) — Binary search (Hard)
- [ ] Solve [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) — Two pointers / monotonic stack (Hard, extremely common)
- [ ] Re-solve the 3 hardest problems from previous weeks without looking at notes
- [ ] Focus on explanation clarity — record yourself explaining one solution

### Tuesday — Coding
**Goal:** Final pattern review & speed drills

- [ ] Re-solve 5 problems (one per category: array, tree, graph, DP, design) in under 20 min each
- [ ] **Final review:** Go through all pattern notes written in weeks 1-7
- [ ] Review [algorithm-patterns.md](algorithm-patterns.md) end-to-end
- [ ] Write a 1-page "cheat sheet" of your personal weak spots and how to handle them

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
- [ ] Finalize top 6-8 STAR stories (must cover: leadership, failure, conflict, mentorship, ambiguity, impact)
- [ ] Prepare "Questions to ask the interviewer" — have 3-5 per round
- [ ] Practice every story aloud, time each to under 2 minutes

### Saturday — Final Mock
- [ ] Full mock interview day: simulate a real big tech on-site loop
  - [ ] Round 1: Coding (45 min) — 2 medium/hard problems
  - [ ] Round 2: System Design (45 min) — random design from the plan
  - [ ] Round 3: Behavioral (30 min) — 4-5 random behavioral questions
  - [ ] Debrief: write down top 3 things to improve before real interview

---

## Big Tech Interview-Specific Notes

### What Big Tech Expects at Senior Level
Big tech (Google, Meta, Amazon, Apple, Microsoft) evaluates seniors differently than startups:

| Dimension | What They Look For |
|-----------|-------------------|
| **Coding** | Not just solving — clear communication, optimal solution, clean code, testing edge cases |
| **System Design** | Driving the conversation, making trade-offs with justification, going deep on components |
| **Behavioral** | Leadership without authority, influence, handling ambiguity, scaling yourself through others |
| **Bar** | You should be able to solve 2 medium problems in 45 min, or 1 hard in 35 min |

### Must-Know Problems (if you solve nothing else, solve these)
These problems appear repeatedly across FAANG interviews:

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 1 | [Two Sum](https://leetcode.com/problems/two-sum/) | HashMap | Easy |
| 2 | [LRU Cache](https://leetcode.com/problems/lru-cache/) | HashMap + DLL | Medium |
| 3 | [Merge Intervals](https://leetcode.com/problems/merge-intervals/) | Sorting | Medium |
| 4 | [Number of Islands](https://leetcode.com/problems/number-of-islands/) | BFS/DFS | Medium |
| 5 | [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Sliding Window | Medium |
| 6 | [Coin Change](https://leetcode.com/problems/coin-change/) | DP | Medium |
| 7 | [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) | Tree DFS | Medium |
| 8 | [Course Schedule](https://leetcode.com/problems/course-schedule/) | Topological Sort | Medium |
| 9 | [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) | Linked List | Easy |
| 10 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Two Pointers / Stack | Hard |
| 11 | [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | Tree BFS/DFS | Hard |
| 12 | [Word Break](https://leetcode.com/problems/word-break/) | DP | Medium |
| 13 | [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) | Two Heaps | Hard |
| 14 | [3Sum](https://leetcode.com/problems/3sum/) | Two Pointers | Medium |
| 15 | [Edit Distance](https://leetcode.com/problems/edit-distance/) | 2D DP | Hard |

### Must-Know System Designs (top 8 for senior interviews)
1. URL Shortener (covered Week 1)
2. Rate Limiter (covered Week 2)
3. News Feed / Timeline (covered Week 6)
4. Chat System / Messaging (covered Week 7)
5. Distributed Key-Value Store (covered Week 7)
6. Search Autocomplete / Typeahead (covered Week 8)
7. Notification System (covered Week 3)
8. File Storage / Dropbox (covered Week 4)

### Company-Specific Tips

| Company | Focus Areas |
|---------|-------------|
| **Google** | Coding (2 rounds), system design depth, Googleyness (collaboration, humility) |
| **Meta** | Coding speed (2 problems in 35 min), system design (scale-focused), behavioral (Meta values) |
| **Amazon** | Leadership Principles (every round), system design (practical), coding (1-2 problems) |
| **Apple** | Domain expertise, coding, system design, team fit (very culture-focused) |
| **Microsoft** | Coding (whiteboard-style), system design, behavioral, hire/no-hire per round |

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

**Stories You Must Have Ready (aim for all green by Week 7)**

- [x] Tell me about yourself (60-90 seconds)
- [ ] Biggest technical challenge
- [x] Time you failed / made a mistake *(Session 2 — project timeline)*
- [x] Conflict with teammate or manager *(Session 1)*
- [ ] Led a project or initiative
- [x] Mentored someone *(Week 4)*
- [x] Handled ambiguous requirements *(Session 3 — QA Platform)*
- [ ] Why are you leaving current job?
- [ ] Why do you want this role? (customize per company)
- [ ] Time you improved system performance or saved costs
- [ ] Time you had to make a decision with incomplete information
- [ ] Time you pushed back on a decision and were right (or wrong)

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
- [ ] Solve: Two Sum, Valid Anagram, LCA, Merge Intervals, LRU Cache
- [ ] Design: URL Shortener only (nail the framework)
- [ ] Behavioral: 3 STAR stories + "Tell me about yourself"
- [ ] Review all pattern notes in [algorithm-patterns.md](algorithm-patterns.md)

### 2 Weeks Available
- [ ] Solve: Two Sum, 3Sum, Longest Substring, Level Order Traversal, Number of Islands, Coin Change, LRU Cache
- [ ] Design: URL Shortener, Rate Limiter
- [ ] Behavioral: 5 STAR stories covering all themes
- [ ] Study pattern recognition table + system design building blocks

### 4 Weeks Available
- [ ] Complete Weeks 1, 2, 5, 7 from this plan
- [ ] Add Week 6 Monday (DP) — most commonly tested
- [ ] Focus on mock interviews in final week
- [ ] Review [algorithm-patterns.md](algorithm-patterns.md) and [system-design/templates.md](system-design/templates.md)

---

## Rules

- [ ] Stop exactly at the end of each time block
- [ ] No multitasking
- [ ] Notes over memorization
- [ ] Consistency over intensity
