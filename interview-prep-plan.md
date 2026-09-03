# Master Preparation Plan — Senior SWE / Team Lead / Staff Engineer (10 Weeks)

This is the **top-level schedule**. Every other file in this repo is a track that this plan
routes you into week by week.

| Track | Where the material lives | Weekly load |
|-------|--------------------------|-------------|
| Coding / algorithms | [`senior-software-engineer.md`](senior-software-engineer.md), [`algorithm-patterns.md`](algorithm-patterns.md) | 2 × 60 min |
| System design | [`staff-system-design.md`](staff-system-design.md), [`system-design/`](system-design/) | 1 × 90 min + 1 × 60 min |
| Leadership & behavioral | [`leadership-behavioral.md`](leadership-behavioral.md), [`star-stories/`](star-stories/) | 1 × 60 min |
| Mock / integration | this file, Part 4 | 1 × 90 min (Sat) |

**Assumptions this plan is built on** — change them and the plan still works, but adjust Part 5:

- ~7 hours/week over 10 weeks (~70 hours total).
- You are targeting **all three** of Senior SWE, Team Lead / EM-lite, and Staff Engineer roles,
  and you will not know which one a given loop is actually calibrated to until you're in it.
- You already pass the senior *knowledge* bar (framework, estimation, DB choice) — see
  [System Design Templates](system-design/templates.md). The gap is altitude, leadership
  evidence, and coding fluency decay.

> **The single most important idea in this plan.** For these three roles you are not learning
> three different bodies of knowledge. You are learning **one body of knowledge delivered at
> three altitudes.** The same "design a rate limiter" question is scored completely differently
> depending on the level on the req. Part 1 is how you tell which altitude you are being asked
> for, and Part 2 drills switching between them on demand.

---

## Part 0 — Which loop are you actually in?

Before prep, know what rounds each target throws at you. The rounds differ far more than the
titles suggest.

| Round | Senior SWE | Team Lead / TLM | Staff Engineer |
|-------|-----------|-----------------|----------------|
| Coding (algorithms) | 2 rounds, full bar | 1 round, still a real bar — do **not** assume it's a formality | 1–2 rounds, often pragmatic/API-flavored rather than pure LeetCode |
| System design | 1 round | 1 round, senior bar | 1–2 rounds, **staff bar** (see `staff-system-design.md` Part 0) |
| Project deep dive | rare | common | **almost always** — 45 min on one project you led |
| Behavioral / values | 1 round | 2 rounds (one is people-management) | 1–2 rounds, focused on influence and scope |
| People / delivery management | — | **dedicated round**: hiring, 1:1s, underperformance, planning | — |
| Cross-team influence / "staff-ness" | — | partly | **dedicated round or heavily probed** |
| Hiring-manager chat | yes | yes, and it's an evaluation | yes, and it sets your level |

**Practical consequence:** the two rounds most people under-prepare for are the
**project deep dive** and the **people/delivery round**. Neither is covered by LeetCode or by
system design practice. Both are covered in [`leadership-behavioral.md`](leadership-behavioral.md)
and scheduled in Weeks 4 and 7 below.

### The one-sentence bar per role

- **Senior:** *"Give this person a hard, well-defined problem and they will land it well."*
- **Team Lead:** *"Give this person a team and a fuzzy goal and the team will deliver, and be
  better for it."*
- **Staff:** *"Give this person a fuzzy, cross-team problem and they will define it, get others
  to agree, and land it — mostly through other people."*

Every story and every design answer you prepare should be labelled with which of those three
sentences it demonstrates. If a story demonstrates none of them, cut it.

---

## Part 1 — The altitude dial

The same question, three answers. Drill this until switching is deliberate rather than accidental.

| Prompt | Senior answer | Team Lead answer | Staff answer |
|--------|--------------|------------------|--------------|
| "Design a rate limiter." | Token bucket in Redis, correct, handles races, discusses trade-offs when asked | Same design, plus: who owns it, how it rolls out to 30 client teams, what the migration and support burden is | Same design, plus: *should this be a shared platform at all?*, blast radius, fail-open vs fail-closed as a **policy decision with a named owner**, tenant isolation, cost |
| "Tell me about a conflict." | I disagreed, presented data, we aligned, feature shipped | I mediated between two engineers, protected delivery, changed the process so it doesn't recur | I resolved a disagreement between two *teams* with opposing incentives, by reframing the decision around a metric both owned |
| "What was your biggest technical decision?" | Chose X over Y for this service, with reasons | Chose X over Y, and got a team of 6 productive on it inside a quarter | Chose X over Y for an org, wrote the doc that made it the default, and stated the condition under which we'd reverse it |
| "How do you handle ambiguity?" | Ask clarifying questions until it's defined | Break it into a delivery plan with milestones and owners | *Propose* the definition, write it down, get sign-off, and name what you're explicitly not doing |

**The tell for altitude in every one of these:** senior answers describe *what I built*, lead
answers describe *how the team delivered*, staff answers describe *how the decision was made and
who else changed behavior because of it*.

### Two failure modes to actively avoid

1. **Over-shooting.** Answering a senior req with pure strategy and no depth reads as "can't
   code / hasn't built anything recently." Always land the technical detail first, *then* add
   the altitude layer.
2. **Under-shooting.** Answering a staff req with a flawless senior answer is the most common
   staff rejection. The design was right; the scoping, the trade-off ownership, and the
   organizational awareness were absent.

**Rule of thumb: answer at the level on the job description, plus a visible half-step above.**
The half-step is what gets you the higher offer; the correct base level is what gets you the offer at all.

---

## Part 2 — Weekly session structure

| Day | Slot | Track | Output |
|-----|------|-------|--------|
| **Mon** | 60 min | Coding | 3 problems, one pattern note |
| **Tue** | 60 min | Coding | 3 problems, one re-solve from memory |
| **Wed** | 90 min | System design — primitive study + timed problem | Component card + 45-min solo design |
| **Thu** | 60 min | Leadership / behavioral | 1–2 written STAR stories, or one drill |
| **Fri** | 15 min | Review | Fill the tracker (Part 6), pick one fix for next week |
| **Sat** | 90 min | Mock | Recorded/timed, alternating type |

**Non-negotiable rules**

- Never read a reference solution before your own timed attempt. The value is entirely in the gap.
- Every design and behavioral answer gets **spoken out loud**, not just written. These are
  verbal exams; silent prep systematically overestimates readiness.
- Record at least one Saturday mock per month and watch it. It is unpleasant and it is the
  highest-value 90 minutes in this plan.

---

## Part 3 — The 10-week schedule

Each week: **theme**, then the four tracks. Design weeks map 1:1 onto
[`staff-system-design.md`](staff-system-design.md) Part 3, so read that file's week N alongside
this file's week N.

---

### Week 1 — Baseline and calibration

**Goal:** measure, don't study. You cannot aim the next 9 weeks without a baseline.

- [ ] **Mon — Coding:** Arrays & hashing, timed. Two Sum, Group Anagrams, Product of Array Except Self. Note where fluency has decayed.
- [ ] **Tue — Coding:** Two pointers & sliding window. 3Sum, Longest Substring Without Repeating Characters, Container With Most Water.
- [ ] **Wed — Design:** `staff-system-design.md` Week 1. Component cards: Postgres, DynamoDB (already written — *review and self-quiz*, don't re-read). Timed 45-min: **URL shortener**, no notes.
- [ ] **Thu — Leadership:** [`leadership-behavioral.md`](leadership-behavioral.md) § Story Portfolio. Map your 6 existing stories onto the 14-slot matrix. **Output: a written gap list.** This is the single most important behavioral task in the plan.
- [ ] **Sat — Mock:** Coding mock, 45 min, 2 mediums back-to-back, out loud.
- [ ] **Baseline scores to record:** rubric score on the URL shortener (`staff-system-design.md` Part 0), coding problems solved in time, behavioral gaps found.

---

### Week 2 — Replication, consensus, and influence

- [ ] **Mon — Coding:** Linked lists. Reverse, Merge Two Sorted, Cycle Detection, Remove Nth From End.
- [ ] **Tue — Coding:** Stacks & queues. Valid Parentheses, Min Stack, Daily Temperatures (monotonic stack), Evaluate RPN.
- [ ] **Wed — Design:** `staff-system-design.md` Week 2 — replication, consistency, consensus. Timed: **distributed job scheduler (exactly-once cron)**. Staff move: the three-line trade-off pattern.
- [ ] **Thu — Leadership:** Write 2 stories — **influence without authority** and **driving alignment on a contentious technical decision**. Use the scoping ladder in `leadership-behavioral.md` § Drill 2.
- [ ] **Sat — Mock:** System design mock — job scheduler, cold, 45 min.

---

### Week 3 — Partitioning, and conflict at scale

- [ ] **Mon — Coding:** Trees. Level Order Traversal, Validate BST, Lowest Common Ancestor, Diameter.
- [ ] **Tue — Coding:** Tree DFS variants + Serialize/Deserialize Binary Tree.
- [ ] **Wed — Design:** `staff-system-design.md` Week 3 — partitioning, sharding, hot keys. Timed: **rate limiter as shared multi-tenant infrastructure for 30 teams**. Staff move: blast radius.
- [ ] **Thu — Leadership:** Rewrite your existing *conflict* and *tech stack decision* stories one altitude up (see Part 1 table). Then write the **"I was wrong and changed my mind"** story — an under-used, very high-signal answer.
- [ ] **Sat — Mock:** Behavioral mock — 5 questions, 2 minutes each, recorded. Watch it back.

---

### Week 4 — Caching, and the people/delivery round

**This is the team-lead-critical week.** Even if you are targeting staff, do it — staff loops
probe mentorship and delivery too.

- [ ] **Mon — Coding:** Graphs. Number of Islands, Clone Graph, Course Schedule (topological sort).
- [ ] **Tue — Coding:** Union-Find + Word Ladder (BFS on implicit graph).
- [ ] **Wed — Design:** `staff-system-design.md` Week 4 — caching and the read path. Timed: **news feed / home timeline**. Component cards: Redis, CDN.
- [ ] **Thu — Leadership:** [`leadership-behavioral.md`](leadership-behavioral.md) § People & Delivery Round. Prepare answers for: underperformance, delegation, hiring bar, prioritizing with a PM who wants everything, protecting a team from thrash. **Your hiring/onboarding story is a strong base — extend it.**
- [ ] **Sat — Mock #1 (external if possible):** Full system design mock with a real person. Pramp / interviewing.io / a peer.

---

### Week 5 — Streaming, idempotency, and delivery under pressure

- [ ] **Mon — Coding:** Heaps. Kth Largest, Find Median from Data Stream, Top K Frequent, Merge K Sorted Lists.
- [ ] **Tue — Coding:** Intervals. Merge Intervals, Insert Interval, Meeting Rooms II, Non-overlapping Intervals.
- [ ] **Wed — Design:** `staff-system-design.md` Week 5 — messaging, streaming, idempotency. Timed: **ad click aggregator** (you have notes — measure altitude, not knowledge). Cards: Kafka, Flink.
- [ ] **Thu — Leadership:** Two stories — **delivery under pressure / cutting scope** (extend your timeline-underestimation story) and **saying no to a stakeholder**. Both must end with a number.
- [ ] **Sat — Mock:** Coding mock — 1 hard, 35 min.

---

### Week 6 — Real-time systems, and technical strategy

- [ ] **Mon — Coding:** Binary search + variants. Search in Rotated Sorted Array, Find Minimum in Rotated Array, Median of Two Sorted Arrays, Koko Eating Bananas (binary search on answer).
- [ ] **Tue — Coding:** 1-D DP. Climbing Stairs, House Robber, Coin Change, Word Break, LIS.
- [ ] **Wed — Design:** `staff-system-design.md` Week 6 — real-time, stateful connections, fanout. Timed: **chat / messaging**. Cards: WebSocket layer, pub/sub.
- [ ] **Thu — Leadership:** Write the **technical strategy** story: a multi-quarter direction you set, the doc you wrote, who adopted it, and how you measured adoption. If you don't have one at org scale, write the honest team-scale version — *do not inflate it*, inflated scope collapses under follow-up questions.
- [ ] **Sat — Mock:** System design mock — chat, cold.

---

### Week 7 — Search/geo/OLAP, and the project deep dive

**This is the staff-critical week.** The project deep dive is the round where staff offers are
won and lost, and it is almost never practiced.

- [ ] **Mon — Coding:** 2-D DP. Unique Paths, Longest Common Subsequence, Edit Distance.
- [ ] **Tue — Coding:** Backtracking. Subsets, Permutations, Combination Sum, Word Search, N-Queens.
- [ ] **Wed — Design:** `staff-system-design.md` Week 7 — search, geo, analytics. Timed: **Uber / proximity service**. Cards: Elasticsearch, geo index, OLAP store.
- [ ] **Thu — Leadership:** [`leadership-behavioral.md`](leadership-behavioral.md) § Project Deep Dive. Build the full 45-minute package for **one** project: context, your specific role, architecture diagram you can redraw from memory, three decisions with alternatives, what went wrong, what you'd change, measured impact.
- [ ] **Sat — Mock #2:** **Deep dive mock.** Have someone interrupt you with "why not X?" every five minutes. This is the actual format.

---

### Week 8 — Multi-region, failure, cost, and failure stories

- [ ] **Mon — Coding:** Tries + design problems. Implement Trie, Word Search II, LRU Cache.
- [ ] **Tue — Coding:** Practical/API-flavored coding — this is what staff loops increasingly ask. Implement: an in-process rate limiter, a retry with exponential backoff and jitter, a bounded LRU with TTL. Clean interfaces, tests, no LeetCode tricks.
- [ ] **Wed — Design:** `staff-system-design.md` Week 8 — multi-region, failure, cost. Timed: **retry storm / thundering herd remediation** and **multi-region active-active**.
- [ ] **Thu — Leadership:** Failure track. Refine your **production incident** story to staff altitude: not just "I fixed it" but what you changed structurally so the class of failure ended, and what it cost. Add: a **postmortem culture** answer and a **decision I made with bad data** answer.
- [ ] **Sat — Mock:** Behavioral + hiring-manager mock, 45 min, including "what are your questions for me?"

---

### Week 9 — ML/AI design, and team building

- [ ] **Mon — Coding:** Timed mixed set — 3 random mediums, 60 min, no hints.
- [ ] **Tue — Coding:** Concurrency & OOD. Design a thread-safe bounded queue; design a parking lot / elevator with clean interfaces. Verbalize the class boundaries.
- [ ] **Wed — Design:** `staff-system-design.md` Week 9 — ML/AI system design. Timed: **recommendation system** and one of **RAG pipeline** or **LLM serving**. Expect one of these in roughly half of 2026 loops.
- [ ] **Thu — Leadership:** Team building. Hiring bar and interview calibration; onboarding (you have this one — sharpen the metric); growing an engineer to promotion; improving a broken process. Team-lead loops score these directly; staff loops probe them as "how do you scale yourself."
- [ ] **Sat — Mock:** ML/AI system design mock, or a second external design mock.

---

### Week 10 — Migration, platform, polish, and closing

- [ ] **Mon — Coding:** Re-solve the 5 problems you got wrong or slow in Weeks 1–9, from memory.
- [ ] **Tue — Coding:** 2 mediums in 45 minutes under strict time. That's the senior bar; confirm you clear it.
- [ ] **Wed — Design:** `staff-system-design.md` Week 10 — migration and evolution. Timed: **migrate 50 TB across databases with zero downtime**, and **design an internal platform other teams adopt**. Migration questions are the purest staff signal there is.
- [ ] **Thu — Leadership:** Final polish. Tell-me-about-yourself (3 versions: senior / lead / staff — see `leadership-behavioral.md` § Openers). Your questions for each interviewer type. Leveling and compensation framing.
- [ ] **Sat — Mock #3:** Full simulated loop if you can arrange it: coding → design → behavioral, back to back, in one morning. Stamina is a real and testable variable.

---

## Part 4 — Mock schedule

| Week | Mock type | Why here |
|------|-----------|----------|
| 1 | Coding | Baseline |
| 2 | System design | Baseline at staff altitude |
| 3 | Behavioral, recorded | Catch verbal habits early, while there's time to fix them |
| 4 | **External design mock** | First outside signal |
| 5 | Coding, 1 hard | Stamina under a single deep problem |
| 6 | System design | Mid-point altitude check |
| 7 | **Project deep dive** | The under-practiced round |
| 8 | Behavioral + hiring manager | Level-setting conversation |
| 9 | ML/AI design or 2nd external | Coverage of the newest round type |
| 10 | **Full loop simulation** | Stamina and context switching |

At least **three** of these should be with another human. Self-mocks systematically flatter you:
they never interrupt, never ask the follow-up you dread, and never look bored.

---

## Part 5 — Compressed variants

### If you have 4 weeks

Run Weeks 1, 4, 7, 10 in order. You lose depth in primitives but keep: baseline, the
people/delivery round, the project deep dive, and the full-loop simulation.

### If you have 2 weeks

- Design: URL shortener, news feed, chat, one migration problem — at staff altitude, using the
  Part 0 rubric in `staff-system-design.md`.
- Coding: the 15 must-know problems in `senior-software-engineer.md` § Must-Know Problems.
- Behavioral: the 8 core stories in `leadership-behavioral.md` § Minimum Viable Portfolio.
- Deep dive: build the one project package. Non-negotiable for lead/staff loops.

### If you have 1 week

Deep dive package + 8 stories + 2 designs (URL shortener, news feed) + 5 coding problems.
Prioritize verbal rehearsal over new material. At this range, polish beats coverage.

---

## Part 6 — Progress tracker

Fill this every Friday in 15 minutes. Confidence is 1–5; design score is out of 24 from the
`staff-system-design.md` Part 0 rubric.

| Week | Coding (1–5) | Design score /24 | Behavioral (1–5) | Stories written | Mock done | Weakest thing this week |
|------|--------------|------------------|------------------|-----------------|-----------|-------------------------|
| 1 | | | | | | |
| 2 | | | | | | |
| 3 | | | | | | |
| 4 | | | | | | |
| 5 | | | | | | |
| 6 | | | | | | |
| 7 | | | | | | |
| 8 | | | | | | |
| 9 | | | | | | |
| 10 | | | | | | |

### Readiness gates

You are ready for a given target when **all** of its rows are true:

| | Senior | Team Lead | Staff |
|---|---|---|---|
| Coding | 2 mediums in 45 min, clean, out loud | 1 medium in 25 min | 1 medium in 25 min + one practical/API problem |
| Design | 14+/24 on an unseen problem | 14+/24 | **16+/24 on three consecutive unseen problems**, one of them Tier C |
| Stories | 8 stories, each ≤ 2 min, each with a number | 12 stories incl. all 4 people-management ones | 12 stories, at least 4 at cross-team scope |
| Deep dive | can describe a project clearly | 45 min, survives interruption | 45 min, survives hostile "why not X?" at every decision |
| Altitude | — | can answer the Part 1 table's lead column cold | can switch altitude on demand, mid-answer |

If a gate fails two weeks running, that's not a study problem — change the *format* of practice
(add a human, add a timer, add a recording), because the missing element is almost always
performance under observation rather than knowledge.

---

## Part 7 — Before the loop starts

- [ ] Confirm the **level on the req** with the recruiter, explicitly. Ask: "What level is this
      role calibrated to, and what does the design round expect at that level?" This one question
      determines your altitude for the whole loop.
- [ ] Ask which rounds are in the loop and whether there is a project deep dive.
- [ ] Have the deep-dive project chosen and drawn before the first round.
- [ ] Have 3 questions ready per interviewer type (`leadership-behavioral.md` § Questions to Ask).
- [ ] Sleep. Stamina is measurable in your Week 10 mock and it is measurable to them too.
