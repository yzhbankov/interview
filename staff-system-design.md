# System Design Track — Senior / Team Lead / Staff

The **system design track** of [`interview-prep-plan.md`](interview-prep-plan.md). It targets the
**Staff / L6 / E6 / IC5** bar and dials *down* to the senior and team-lead bars on demand —
see [Using this plan at three altitudes](#using-this-plan-at-three-altitudes) below.

It assumes you already pass the senior bar (you can run the 7-phase framework, estimate, and pick
a database with justification — see [System Design Templates](system-design/templates.md)), and it
teaches the *delta*: what a staff candidate does that a strong senior does not, plus what a team
lead adds that neither does.

**Time budget:** the Wednesday 90-minute block in [`weeks/`](weeks/) plus the design mocks on
Saturdays (Weeks 1, 4, 8). This file is the reference; the schedule is there.

---

## Part 0 — What Is Actually Being Scored

Read this section first, and re-read it before every mock. Most staff-level rejections are
not technical gaps; they are altitude gaps — a correct senior-level answer delivered to a
staff-level question. But the reverse error is just as costly: a strategy-heavy answer with no
depth, given to a senior req, reads as someone who has stopped building.

### Using this plan at three altitudes

Run the same weekly material for any of the three targets; only the emphasis and the pass mark
change.

| | Senior SWE | Team Lead / TLM | Staff Engineer |
|---|---|---|---|
| **Pass mark on the Part 0 rubric** | 14+/24, nothing at 0 on rows 1–4 | 14+/24, plus a delivery plan | **16+/24 across three consecutive unseen problems** |
| **Rows that carry the most weight** | 2, 3, 4 (quantification, depth, trade-offs) | 4, 6 (trade-offs, operability) plus staffing and sequencing | 1, 4, 5, 6 (scoping, trade-offs, blast radius, evolution) |
| **What to add to every answer** | one real deep dive with internals | *who builds this, in what order, with what team shape, and what's the risk to the date* | *should we build this at all, what breaks at the next order of magnitude, and what does it cost* |
| **Closing sentence** | "Here's the design and its main bottleneck." | "Here's the design, and here's what I'd have three engineers do in the first six weeks." | "Here's the design, here's the phase I'd build first, and here's the condition that would make me reverse the core decision." |

**The team-lead lens** is the one this repo previously had no coverage of. Every week, after
finishing session C, spend five minutes answering these four questions about the design you just
produced — written, not just thought:

1. **Sequencing** — what is the first shippable slice, and what does it prove?
2. **Team shape** — how many engineers, split how, and what's the coordination cost between them?
3. **Risk to the date** — which unknown most threatens the timeline, and how do you de-risk it in week 1?
4. **Support cost** — once this is live, who is on call for it, and what wakes them up at 3am?

Those four questions are the entire content of a team-lead design round beyond the senior bar.

### The senior → staff delta

| Dimension | Senior (L5) answer | Staff (L6) answer |
|-----------|-------------------|-------------------|
| **Problem framing** | Asks clarifying questions, accepts the scope given | *Proposes* the scope: "There are three products hiding in here. I'll design the one that carries the risk, and tell you why I'm deferring the others." |
| **Requirements** | Elicits functional + non-functional requirements | Attaches a **number and a business consequence** to each NFR: "p99 under 200ms — past that, checkout conversion drops, so this is a revenue SLO, not a comfort target." |
| **Trade-offs** | Discusses trade-offs when prompted | Names the trade-off *before* being asked, states which way they're going, and states the condition that would reverse the decision |
| **Time horizon** | 6–12 months; designs for stated scale | 1–2 years; designs for the scale *after* the next order of magnitude, and says what breaks first when it arrives |
| **Complexity** | Solves the hard version | Asks whether the hard version is required: "Do we actually need cross-shard transactions, or can we make the boundary align with the entity?" Then justifies the simpler design |
| **Failure** | Handles component failure (replica dies, cache dies) | Reasons about **blast radius**, correlated failure, gray failure, retry storms, and what the 3am pager text says |
| **Operations** | Mentions monitoring | Specifies the SLI, the alert threshold, the dashboard, the rollout strategy, and the kill switch |
| **Cost** | Rarely mentioned | Produces an order-of-magnitude cost figure and identifies the dominant line item |
| **Evolution** | Designs the end state | Designs the **migration**: how you get from today's system to this one with no downtime, in shippable increments, and how you roll back at each step |
| **Org awareness** | Designs a system | Designs a system **with team boundaries in mind**: what each service's owning team is, which interfaces are contracts, where the on-call load lands |

### The six things a staff candidate does unprompted

1. **Scopes the problem down out loud**, with a stated reason, and gets buy-in.
2. **Quantifies before designing** — writes the QPS, storage, and fanout numbers on the board and refers back to them when justifying each choice.
3. **Declares the interesting part early**: "The hard part of this system isn't storage, it's [X]. I'm going to spend most of my time there."
4. **Volunteers the failure mode** of their own design before the interviewer probes it.
5. **Gives a cost and an ops story** without being asked.
6. **Ends with what they'd build first** — a phased plan, not a monolithic architecture.

### Self-scoring rubric (use after every practice problem)

Score 0–2 on each. **Staff bar is 16+/24, with nothing at 0. Senior and team-lead bar is 14+/24.** Team-lead candidates additionally score a 7th row: *sequencing and team shape* — 0 = not mentioned, 1 = phased plan, 2 = phased plan with team split, coordination cost, and the week-1 de-risking step.

| # | Signal | 0 | 1 | 2 |
|---|--------|---|---|---|
| 1 | Scoping & prioritization | Took the problem as stated | Asked good questions | Proposed and justified a scope cut |
| 2 | Quantification | Hand-waved scale | Estimated on request | Numbers drove design decisions |
| 3 | Depth in ≥2 components | Stayed at box level | One real deep dive | Two+ deep dives with internals and failure behavior |
| 4 | Trade-off ownership | Listed options, no decision | Chose with reason | Chose, stated reversal condition, named what was given up |
| 5 | Failure & blast radius | Component-level only | Discussed degradation | Correlated failure, retry storms, isolation, kill switches |
| 6 | Operability, cost, evolution | Absent | Mentioned | Concrete SLIs, cost figure, phased migration plan |

**Sources for the bar itself:**
- [5 Keys to Staff-Level System Design Interviews — Hello Interview](https://www.hellointerview.com/blog/staff-level-system-design)
- [What Is Expected at Each Level — Hello Interview](https://www.hellointerview.com/blog/the-system-design-interview-what-is-expected-at-each-level)
- [The Staff Engineer's System Design Playbook (L6+) — Design Gurus](https://designgurus.substack.com/p/the-staff-engineers-system-design)
- [How Staff Engineers Answer System Design Questions Differently](https://designgurus.substack.com/p/l4-vs-l6-system-design-interviews)
- [Staff Software Engineer Interview Guide — IGotAnOffer](https://igotanoffer.com/en/advice/staff-software-engineer-interview)
- [System Design Prep Guide for Senior/Staff Engineers — StaffEngPrep](https://staffengprep.com/system-design-interview-prep-guide/)

---

## Part 1 — Resource Library

### Tier 1 — Primary (use these weekly)

| Resource | What it's for | Cost |
|----------|---------------|------|
| [Hello Interview — System Design in a Hurry](https://www.hellointerview.com/learn/system-design/in-a-hurry/introduction) | The best free structured course. Core concepts, key technologies, patterns, and delivery framework | Free |
| [Hello Interview — Problem Breakdowns](https://www.hellointerview.com/learn/system-design/problem-breakdowns/) | Written walkthroughs with explicit "what senior vs staff looks like" callouts per problem | Free (some premium) |
| [Hello Interview — Guided Practice](https://www.hellointerview.com/practice) | Step-by-step practice with AI feedback per phase, tuned by FAANG interviewers | Freemium |
| [Designing Data-Intensive Applications](https://dataintensive.net/) — Kleppmann | The single most important book. Staff-level depth on replication, partitioning, transactions, consensus, stream processing | Book |
| [Jordan Has No Life — Systems Design 2.0](https://www.youtube.com/playlist?list=PLjTveVh7FakLdTmm42TMxbN8PvVn5g4KJ) | Free video series that reasons from DDIA primitives rather than reciting architectures. Best free deep-dive content | Free |
| [Amazon Builders' Library](https://aws.amazon.com/builders-library/) | Operational maturity: timeouts, retries with jitter, load shedding, health checks, deployment safety. This is where staff-level ops answers come from | Free |
| [Google SRE Book + SRE Workbook](https://sre.google/books/) | SLI/SLO/error budget vocabulary, overload handling, cascading failure, addressing gray failure | Free |

### Tier 2 — Breadth and problem volume

| Resource | Use |
|----------|-----|
| [System Design Primer](https://github.com/donnemartin/system-design-primer) | Vocabulary sweep and estimation refresher |
| [ByteByteGo](https://bytebytego.com/) | Alex Xu's course; Vol. 2 problems (payments, hotel booking, ad click, digital wallet) are closest to staff-flavored |
| [Awesome Scalability](https://github.com/binhnguyennus/awesome-scalability) | Curated real engineering-blog postmortems and architecture write-ups, by topic |
| [Design Gurus — System Design Interview Guide 2026](https://designgurus.io/blog/complete-guide-sys-design) | Framework refresher and question bank |
| [Exponent — System Design Interview Guide](https://www.tryexponent.com/blog/system-design-interview-guide) | Company-specific loop breakdowns (Meta vs Google vs Amazon differences) |
| [Martin Fowler — Architecture](https://martinfowler.com/architecture/) | Vocabulary for evolution, strangler fig, sacrificial architecture — directly useful for migration questions |
| [The Pragmatic Engineer](https://newsletter.pragmaticengineer.com/) | Real-world scaling and incident write-ups; good for "have you seen this at scale" credibility |

### Tier 3 — Foundations (background reading)

| Resource | Use |
|----------|-----|
| [MIT 6.5840 Distributed Systems](https://pdos.csail.mit.edu/6.824/) | Lectures + paper list + Raft labs. The paper list alone is the best distributed systems curriculum available free |
| [Martin Kleppmann — Distributed Systems lectures (Cambridge)](https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB) | 8 hours of clean explanations of consistency, consensus, logical time |
| [Papers We Love — Distributed Systems](https://github.com/papers-we-love/papers-we-love/tree/main/distributed_systems) | Canonical papers with context |
| [Raft: In Search of an Understandable Consensus Algorithm](https://raft.github.io/) | Paper + visualization. Know leader election, log replication, and why leases matter |
| [Dynamo: Amazon's Highly Available Key-value Store](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) | Quorums, vector clocks, hinted handoff, consistent hashing |
| [Kafka design docs](https://kafka.apache.org/documentation/#design) | Log-structured messaging, partitions, ISR, exactly-once semantics |
| [Jepsen analyses](https://jepsen.io/analyses) | What consistency claims actually mean when tested. Excellent for trade-off credibility |

### Tier 4 — ML / AI system design (expect one in most senior+ loops in 2026)

| Resource | Use |
|----------|-----|
| [Machine Learning System Design Interview Guide — Exponent](https://www.tryexponent.com/blog/machine-learning-system-design-interview-guide) | Framework for ML design rounds |
| [alirezadir/machine-learning-interviews — MLSD](https://github.com/alirezadir/machine-learning-interviews/blob/main/src/MLSD/ml-system-design.md) | Free, comprehensive ML system design template and problem set |
| [AI/ML System Design Interview Roadmap 2026 — Design Gurus](https://www.designgurus.io/blog/prepare-for-ai-ml-system-design-interview-2026) | What's new in 2026 loops: RAG, LLM serving, GPU capacity, eval |
| [Designing Machine Learning Systems](https://www.oreilly.com/library/view/designing-machine-learning-systems/9781098107956/) — Chip Huyen | Feature stores, training/serving skew, data drift, monitoring |
| [AI Engineering](https://www.oreilly.com/library/view/ai-engineering/9781098166298/) — Chip Huyen | LLM serving, RAG architecture, evaluation, cost control |

### Tier 5 — Mocks (non-optional)

| Resource | Notes |
|----------|-------|
| [interviewing.io](https://interviewing.io/) | Anonymous mocks with real FAANG interviewers; recordings + written feedback. Highest signal, paid |
| [Hello Interview — mock interviews](https://www.hellointerview.com/premium) | Mocks with FAANG interviewers, staff-level calibration |
| [Pramp](https://www.pramp.com/) | Free peer-to-peer. Lower signal on staff calibration, but good for reps and delivery |
| Colleagues at staff+ level | Best free option. Ask specifically: "score me on scoping, blast radius, and cost" |

**Target: 6+ mocks before your first real loop, at least 2 with a real staff+ interviewer.**

---

## Part 2 — Problem Catalog

Work these in the order [`weeks/`](weeks/) assigns. Existing notes and the **practice log** —
which problems you've done, who drove them, and whether you've done each solo at the staff bar —
live in [`system-design/`](system-design/). Add `structured.md` notes for new problems.

### Tier A — Already practiced; redo at the staff bar

Five from solo recordings in the spring, six from mock sessions with a friend over the summer
(URL shortener, job scheduler, rate limiter, news feed, ad click aggregator, WhatsApp). Re-do
them **as the candidate, cold, no notes**, then score with the rubric — the gap will be almost
entirely in rows 4–6 (trade-off ownership, blast radius, ops/cost/migration). Weeks 1, 4 and 8.

| Problem | Staff-level angle to add |
|---------|--------------------------|
| [URL shortener](system-design/url-shortener/) | Key generation without coordination; what happens when the counter service is partitioned; cost per billion redirects; CDN cache invalidation on link deletion (legal takedowns) |
| [Rate limiter](system-design/rate-limiter/) | Distributed counter accuracy vs latency; the failure mode when Redis is unavailable (fail open or closed — and who decides); per-tenant fairness; running it as shared infra across teams |
| [Web crawler](system-design/web-crawler/) | Politeness as a multi-tenant scheduling problem; recrawl prioritization as an economic decision; trap detection; cost of storage vs freshness |
| [Ad click aggregator](system-design/ad-click-aggregator/) | Exactly-once vs at-least-once and its *billing* consequence; late/out-of-order events and watermarking; reconciliation batch job as the source of truth; fraud filtering stage |
| [Dropbox / file sync](system-design/dropbox/) | Conflict resolution semantics; chunk dedup economics; sync protocol under flaky mobile networks; the migration story for changing chunk size |
| Distributed job scheduler | Exactly-once execution via leases + idempotency; clock skew; thundering herd at the top of the hour; what the operator sees when a run is late |
| News feed | Fanout hybrid with a cost model attached; celebrity threshold as a number; ranking service degradation; the "add an LLM summary" extension |
| WhatsApp / chat | Deploying a stateful connection tier; ordering guarantees per conversation; multi-region (Week 3); the 3am page |

### Tier B — Core staff problems (Uber in Week 2; the rest are mock material)

| Problem | Primary skill tested |
|---------|---------------------|
| **Distributed job scheduler / cron** | Exactly-once execution, leases, clock skew, thundering herd, at-least-once + idempotency |
| **Payment / ledger system** | Strong consistency, double-entry bookkeeping, idempotency keys, reconciliation, sagas, auditability |
| **Notification / fanout system** | Multi-channel delivery, dedup, rate limits per user, priority queues, backpressure, cost per message |
| **Chat / messaging (WhatsApp, Slack)** | Stateful connections, ordering guarantees, presence, fanout on read vs write, offline delivery |
| **News feed / timeline (Twitter, Instagram)** | Fanout strategy hybrid, celebrity problem, ranking integration, cache warming |
| **Real-time collaborative editor (Google Docs)** | OT vs CRDT, causality, offline merge, cursor presence |
| **Metrics / observability platform (Datadog)** | High-cardinality time series, downsampling, retention tiering, query cost isolation |
| **Search autocomplete + full search (typeahead)** | Index build pipeline, trie vs inverted index, real-time index updates, relevance |
| **Uber / proximity service** | Geospatial indexing (geohash, S2, quadtree), matching under contention, driver state machine |
| **Ticketmaster / seat reservation** | Reservation locks under extreme contention, virtual waiting room, fairness, overselling policy |
| **Multi-tenant API gateway / control plane** | Isolation, noisy neighbors, quota enforcement, config propagation, safe rollout |
| **Video streaming / upload pipeline (YouTube)** | Transcode fan-out, ABR ladder cost, CDN economics, resumable upload |

### Tier C — Staff-flavored problems (Weeks 3 and 7)

These are the ones that separate levels. Practice them explicitly; they are rarely in
standard question banks.

| Problem | What it tests |
|---------|---------------|
| **"Our monolith's Postgres is at 90% CPU and we're doubling in 6 months"** | Diagnosis before design, incremental extraction, strangler fig, read replicas → CQRS → sharding, rollback at each step |
| **"Migrate 50TB from MySQL to a distributed store with zero downtime"** | Dual-write vs CDC, backfill + verify, shadow reads, cutover, reconciliation, rollback |
| **"Design a platform 30 engineering teams will build on"** | Interface contracts, multi-tenancy, self-service, guardrails vs gates, deprecation policy, org boundaries |
| **"This system costs $4M/year. Cut it in half."** | Cost modeling, tiered storage, compute right-sizing, cache hit-rate economics, what quality you trade |
| **"Make this system multi-region"** | Failure domains, data residency, active-active vs active-passive, conflict resolution, RTO/RPO, cross-region cost |
| **"We had a 4-hour outage from a retry storm. Redesign so it can't recur."** | Backpressure, circuit breakers, jittered retry budgets, load shedding, graceful degradation, isolation |

### Tier D — ML / AI design (Weeks 5 and 6; expect one in most senior+ loops)

| Problem | Focus |
|---------|-------|
| **Recommendation / ranking system** | The retrieval → pre-rank → rank funnel with latency budget per stage (~1ms / ~5ms / ~20ms), feature store, training/serving skew, online vs offline eval |
| **RAG system over enterprise documents** | Chunking, embedding pipeline, vector store choice, hybrid retrieval, reranking, prompt assembly, freshness, permission-aware retrieval, eval, cost per query |
| **LLM serving platform** | GPU capacity and batching, KV cache, streaming, routing across model tiers, quotas, fallback on provider failure, cost per token guardrails |
| **Feed ranking with an ML model in the path** | Where the model sits, degradation when it's down, shadow deploys, feature freshness, A/B infrastructure |
| **Abuse / fraud detection** | Online + offline hybrid, label delay, feedback loops, precision/recall as a *business* trade-off |
| **"Add an AI assistant / RAG / agent to this existing system"** | The 2026 follow-up to any classic design: where the model call sits (sync vs async), fallback when it's down, cost per call × DAU, what you cache, how you evaluate it. Practiced as the Week 4 extension |
| **Agent workflow platform** | Tool-call orchestration, step idempotency and retries, budget/step limits, human-in-the-loop gates, audit trail, isolation between tenants |

---

## Part 3 — The Schedule

**The schedule lives in [`weeks/`](weeks/) — one page per week, 8 weeks from 12 Oct 2026.**
The v1 10-week schedule is in [`archive/plan-v1-sep-2026/`](archive/plan-v1-sep-2026/).

Every Wednesday follows the same shape:

| Block | Length | Output |
|-------|--------|--------|
| **Study** | 20 min | The week's primitive; a one-page component card when it's a new store or tier (Part 4) |
| **Timed problem** | 45 min | Solo, paper, no notes |
| **Staff move** | 15 min | The week's staff behavior, written: trade-off lines, blast radius, cost model, scope cuts, postmortem, reversibility |
| **Score** | 10 min | Rubric out of 24, three named misses, *then* the reference breakdown |

**Rule: never read the reference before your own attempt.** The value is entirely in the gap.

**If you are also targeting team lead:** close the staff move with the four-question lead lens
(sequencing, team shape, risk to the date, support cost) from
[Using this plan at three altitudes](#using-this-plan-at-three-altitudes). Five minutes, written.

---

## Part 4 — Component Cards

Build one card per primitive as you go. By the end of the plan you'll have ~15. These are what you draw
on for deep dives, and writing them is what converts reading into recall.

**Card template (one page, hand-written is fine):**

```
COMPONENT: <name>
Data model:        <how it stores things>
Consistency:       <what it guarantees, and under what config>
Partitioning:      <how it splits data; what a bad key does>
Replication:       <sync/async, failover behavior, RPO>
Failure modes:     <what breaks, what the symptom looks like>
Scale ceiling:     <the number where it stops working>
Cost driver:       <the dominant line item>
Ops:               <what you monitor; what pages you>
Choose it when:    <2-3 conditions>
Don't when:        <2-3 conditions>
Alternative:       <closest substitute + the one reason to switch>
```

**Filled cards so far:** [system-design/components/](system-design/components/) — PostgreSQL, DynamoDB,
plus a side-by-side decision table.

**Minimum card set:**

| Category | Cards |
|----------|-------|
| Relational | Postgres/MySQL, read replicas, Vitess/sharded MySQL |
| Key-value / wide-column | DynamoDB, Cassandra, Redis |
| Blob | S3 / object storage (+ tiering) |
| Messaging | Kafka, SQS/Pub-Sub, delay queues |
| Stream processing | Flink / Kafka Streams |
| Search | Elasticsearch / OpenSearch |
| Analytics | ClickHouse / Druid / BigQuery-class warehouse |
| Cache / edge | Redis, CDN |
| Coordination | ZooKeeper/etcd, Raft, distributed locks and leases |
| Geo | S2 / geohash index |
| ML | Feature store, vector DB, model serving tier |
| Cross-cutting | Load balancer, API gateway, circuit breaker, load shedder |

---

## Part 5 — Delivery Script for Staff Interviews

Adapt the 7-phase framework in [templates.md](system-design/templates.md) with these staff-level insertions.

### Opening (first 5 minutes) — the highest-leverage part of the interview

> "Let me make sure I'm solving the right problem. As stated, this contains [A], [B], and [C].
> [B] is where the scale and the risk are, so I'd like to design [B] properly and treat [A] and
> [C] as interfaces I'll define but not build out. Does that match what you want to see?"

Then, before drawing anything:

> "Two numbers will drive most of this design: [X] and [Y]. Let me compute them now so my
> choices are grounded."

### Declaring the interesting part (~minute 12)

> "The storage here is not hard — it's [obvious choice] and I'll say why in one line. The hard
> part is [X], because [specific reason tied to a number]. I'm going to spend the bulk of my
> time there."

This single move is the clearest staff signal available, because it demonstrates judgment
about what matters rather than exhaustive coverage.

### Trade-off pattern (use every time, ~20 seconds each)

> "I'm choosing [X] over [Y]. What I'm giving up is [Z]. I'd switch to [Y] if [specific
> condition] — for example if write volume crossed [number] or if we needed [property]."

### Volunteering failure (before being asked)

> "Let me stress this myself. If [component] fails, the symptom is [X], the blast radius is
> [scope], and we degrade to [behavior] rather than failing hard. The thing I'd actually worry
> about is [subtler correlated failure], because [reason]."

### Cost (~minute 35, unprompted)

> "Rough cost: the dominant line item is [X] at order [$N]/month, driven by [driver].
> If we needed to halve it, the lever is [change], and it costs us [quality trade-off]."

### Closing (last 3 minutes) — phased plan, not a summary

> "If I were building this, phase 1 is [smallest thing that delivers value and validates the
> risky assumption] — that ships in a quarter. Phase 2 adds [X] once we see [signal].
> Phase 3 is [the full design] and we only need it if [condition]. The riskiest assumption in
> the whole design is [X], and phase 1 is deliberately shaped to test it early."

### Handling a challenge

Do **not** immediately design the more complex thing. First:

> "Let me check whether we actually need that. It would be required if [condition]. Given our
> [number/constraint], I don't think we're there — but if you want me to assume we are, here's
> how it changes: [change]."

This is the behavior most cited as the senior/staff distinguisher: staff candidates interrogate
whether complexity is warranted; seniors reflexively solve for it.

---

## Part 6 — Red Flags to Eliminate

| Red flag | Fix |
|----------|-----|
| Drawing boxes before computing numbers | Estimate first, always, even roughly |
| Naming technologies without justification ("I'll use Kafka") | One clause of *why*, tied to a requirement |
| Listing trade-offs without deciding | Always decide, and state the reversal condition |
| Perfect design with no failure discussion | Volunteer failure analysis unprompted |
| No cost, no ops, no migration | These three are staff table stakes in 2026 |
| Solving the hardest version by reflex | Ask whether the complexity is required |
| Silent thinking | Narrate; an unspoken good idea scores zero |
| Ignoring the interviewer's hints | A hint is a scoring rubric item being handed to you — take it |
| Designing the end state only | End with a phased plan and the riskiest assumption |
| No team/ownership awareness | Name service boundaries as team boundaries |

---

## Part 7 — Progress

Design scores go in the master [tracker](interview-prep-plan.md#part-6--progress-tracker), one row
per week. The per-problem record — what you've designed, who drove it, whether you've done it solo
at the staff bar — is the [practice log](system-design/README.md#practice-log).

**Readiness gate:** 16+/24 on three consecutive unseen problems, at least one Tier C and one
Tier D, without reading notes.

---

## Sources

- [5 Keys to Staff-Level System Design Interviews — Hello Interview](https://www.hellointerview.com/blog/staff-level-system-design)
- [The System Design Interview: What Is Expected at Each Level — Hello Interview](https://www.hellointerview.com/blog/the-system-design-interview-what-is-expected-at-each-level)
- [System Design in a Hurry — Hello Interview](https://www.hellointerview.com/learn/system-design/in-a-hurry/introduction)
- [Guided Practice — Hello Interview](https://www.hellointerview.com/practice)
- [The Staff Engineer's System Design Playbook: How to Pass an L6+ Interview — Design Gurus](https://designgurus.substack.com/p/the-staff-engineers-system-design)
- [How Staff Engineers Answer System Design Questions Differently — Design Gurus](https://designgurus.substack.com/p/l4-vs-l6-system-design-interviews)
- [System Design Interview Guide 2026 — Design Gurus](https://designgurus.io/blog/complete-guide-sys-design)
- [Staff Software Engineer Interview (questions and prep) — IGotAnOffer](https://igotanoffer.com/en/advice/staff-software-engineer-interview)
- [System Design Interview Prep Guide for Senior/Staff Engineers — StaffEngPrep](https://staffengprep.com/system-design-interview-prep-guide/)
- [System Design Interview Prep & Questions (2026 Guide) — Exponent](https://www.tryexponent.com/blog/system-design-interview-guide)
- [Machine Learning System Design Interview (2026 Guide) — Exponent](https://www.tryexponent.com/blog/machine-learning-system-design-interview-guide)
- [How to Prepare for an AI/ML System Design Interview (2026 Roadmap) — Design Gurus](https://www.designgurus.io/blog/prepare-for-ai-ml-system-design-interview-2026)
- [Senior vs Staff in a System Design Interview — Taro](https://www.jointaro.com/question/5TaZMBhgxvdsKMoQ7qWi/what-is-the-difference-between-a-senior-and-staff-engineer-in-a-system-design-interview/)
- [MIT 6.5840 Distributed Systems](https://pdos.csail.mit.edu/6.824/)
- [10 Best Free Resources to Learn System Design (2026) — Design Gurus](https://www.designgurus.io/blog/free-system-design-resources)
- [machine-learning-interviews — MLSD](https://github.com/alirezadir/machine-learning-interviews/blob/main/src/MLSD/ml-system-design.md)
- [Systems Design 2.0 — Jordan Has No Life](https://www.youtube.com/playlist?list=PLjTveVh7FakLdTmm42TMxbN8PvVn5g4KJ)
