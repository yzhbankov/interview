# Master Plan v2 — Senior SWE / Team Lead / Staff Engineer (8 weeks, Oct–Dec 2026)

This is the top-level plan. Every other file in this repo is a track that this plan routes you into
week by week. Version 2 replaces the September 10-week plan (now in
[`archive/plan-v1-sep-2026/`](archive/plan-v1-sep-2026/)) after two months of real progress
changed the starting point.

| Track | Material | Weekly load |
|-------|----------|-------------|
| Coding | [`coding-track.md`](coding-track.md), [`algorithm-patterns.md`](algorithm-patterns.md) | 2 × 60 min |
| System design | [`staff-system-design.md`](staff-system-design.md), [`system-design/`](system-design/) | 1 × 90 min |
| Leadership & behavioral | [`leadership-behavioral.md`](leadership-behavioral.md), [`star-stories/`](star-stories/) | 1 × 60 min |
| Mock | this file, Part 4 | 1 × 90 min (Sat) |

| When | Open |
|------|------|
| **Every session** | [`weeks/week-NN.md`](weeks/) — self-contained |
| **Every Friday, 15 min** | The [tracker](#part-6--progress-tracker) below |
| **Writing a story** | [`leadership-behavioral.md`](leadership-behavioral.md) + [`star-stories/TEMPLATE.md`](star-stories/TEMPLATE.md) |
| **Scoring a design** | [Rubric](staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |
| **Night before a loop** | [Anti-patterns](leadership-behavioral.md#part-7--anti-patterns-that-cost-levels) and [Part 7](#part-7--before-the-loop-starts) |

---

## Where you are (7 Oct 2026)

| Track | Done | Still open | Verdict |
|-------|------|------------|---------|
| **Coding** | Arrays & hashing, two pointers, sliding window, linked lists, stacks, trees | Graphs, heaps, intervals, binary search, DP, backtracking, tries, practical/API problems, the AI-assisted format | On track. Half the patterns, the harder half, remain |
| **Design** | Six problems with a friend, swapping roles: URL shortener, job scheduler, rate limiter, news feed, ad click aggregator, WhatsApp. Five solo recordings from the spring | Redo as *candidate* the ones you only interviewed. Staff moves (cost, blast radius, ops, phased plan) not yet drilled. Nothing on geo, failure at scale, AI design, migration | Ahead on breadth, unmeasured on altitude |
| **Behavioral** | Six stories from Jan–Mar | 8 of 14 slots empty. No deep-dive package. People/delivery round untouched | **The gap.** This is the round that sets the level |

**What v2 changes, and why**

1. **Design hours move from "learn new problems" to "redo known problems at the staff bar", plus four new areas: geo, failure, AI, migration.** You have the breadth; you have no rubric scores.
2. **Behavioral gets priority over coding whenever time is short.** Coding is a filter; behavioral and the deep dive decide the level.
3. **Two 2026 round types are added:** the AI-assisted coding round and the generative-AI system design round. See below.
4. **Eight weeks, not ten.** Weeks 1–3 of the old plan are done. The schedule is dated so you can tell whether you're behind.

### What changed in interviews this year

Based on interviewer-run prep sites and reported loops, not on official company statements — verify with each recruiter.

- **AI-assisted coding rounds are real.** Meta rolled them out in 2025, Canva requires tool use, Shopify lets you bring your own IDE, and Google is piloting Gemini in a code-comprehension round. You are scored on how you *direct* the tool, *verify* its output and *catch the bugs it introduces*. They sit alongside a classic tool-free round, so fundamentals still matter. → [`coding-track.md` § new round types](coding-track.md#the-two-new-round-types-2026), Week 6.
- **Generative-AI system design is a third design round type.** The LLM inference API is "the new URL shortener"; enterprise RAG and "add an assistant to this existing system" are standard follow-ups. Expectations: cost per query/token, latency budget, eval, fallback when the model fails. Classic problems are still asked most. → Weeks 4, 5, 6.
- **Deep dives got harder, narration matters more.** With AI handling breadth, interviewers push depth on the component you chose and listen for independent judgment. Silent thinking scores zero.
- **A new behavioral question:** *how do you and your team use AI, and where don't you trust it?* → Week 7.

Sources: [Hello Interview — AI-enabled coding interviews](https://www.hellointerview.com/learn/ai-coding/overview/introduction) ·
[PracHub — the AI coding interview, 2026](https://prachub.com/resources/ai-coding-interview-guide) ·
[Companies that allow AI during interviews](https://www.finalroundai.com/blog/companies-that-allow-ai-during-interviews) ·
[techinterview.org — AI in system design interviews, 2026](https://www.techinterview.org/post/3233474919/ai-in-system-design-interviews-2026/) ·
[Instahyre — system design interview guide](https://resources.instahyre.com/blog/system-design-interview-guide/)

---

## Part 0 — Which loop are you in?

| Round | Senior SWE | Team Lead / TLM | Staff Engineer |
|-------|-----------|-----------------|----------------|
| Coding | 2 rounds, full bar; one may be AI-assisted | 1 round, still real | 1–2 rounds, often practical/API-shaped |
| System design | 1 round | 1 round, senior bar | 1–2 rounds, **staff bar**; one may be AI/ML design |
| Project deep dive | rare | common | **almost always** — 45 min on one project you led |
| Behavioral / values | 1 round | 2 rounds, one on people management | 1–2 rounds on influence and scope |
| People / delivery | — | **dedicated round** | — |
| Hiring-manager chat | yes | yes, and it's an evaluation | yes, and it sets your level |

**The one-sentence bar per role**

- **Senior:** *"Give this person a hard, well-defined problem and they will land it well."*
- **Team Lead:** *"Give this person a team and a fuzzy goal and the team will deliver, and be better for it."*
- **Staff:** *"Give this person a fuzzy, cross-team problem and they will define it, get others to agree, and land it — mostly through other people."*

Label every story and every design answer with which sentence it demonstrates. If none, cut it.

---

## Part 1 — The altitude dial

The same question, three answers. Drill until switching is deliberate.

| Prompt | Senior | Team Lead | Staff |
|--------|--------|-----------|-------|
| "Design a rate limiter." | Token bucket in Redis, correct, handles races, trade-offs when asked | Same, plus: who owns it, how it rolls out to 30 client teams, the migration and support burden | Same, plus: *should this be a shared platform at all?*, blast radius, fail-open vs fail-closed as a **policy with a named owner**, tenant isolation, cost |
| "Tell me about a conflict." | I disagreed, brought data, we aligned, shipped | I mediated between two engineers, protected delivery, changed the process so it doesn't recur | I resolved a disagreement between two *teams* with opposing incentives by reframing around a metric both owned |
| "Biggest technical decision?" | Chose X over Y for this service, with reasons | Chose X over Y, and got six engineers productive on it in a quarter | Chose X over Y for an org, wrote the doc that made it the default, stated the condition for reversing it |
| "How do you handle ambiguity?" | Ask until it's defined | Break it into a plan with milestones and owners | *Propose* the definition, write it down, get sign-off, name what you're not doing |

**The tell:** senior answers describe *what I built*, lead answers *how the team delivered*, staff
answers *how the decision was made and who else changed because of it*.

**Rule of thumb: answer at the level on the job description, plus a visible half-step above.**
Over-shooting (strategy with no depth) reads as "stopped building". Under-shooting (a flawless
senior answer to a staff question) is the most common staff rejection.

---

## Part 2 — The week

| Day | Slot | Track | Output |
|-----|------|-------|--------|
| **Mon** | 60 min | Coding | 3 problems, one pattern note |
| **Tue** | 60 min | Coding | 3 problems, or one practical problem with tests |
| **Wed** | 90 min | Design | 20 study · 45 timed · 15 staff move · 10 score |
| **Thu** | 60 min | Behavioral | 1–2 written stories, or one drill |
| **Fri** | 15 min | Review | Tracker row, one fix |
| **Sat** | 90 min | Mock | Timed, with a human where the schedule says so |

**Non-negotiable**

- Never read a reference solution before your own timed attempt.
- Every design and behavioral answer is **spoken aloud**. Silent prep overestimates readiness.
- Record at least one mock a month and watch it.
- Your friend is your best free resource. Use them for the design, deep-dive and HM mocks; give them the rubric first.

---

## Part 3 — The 8-week schedule

**The schedule lives in [`weeks/`](weeks/) — one page per week.**

| Week | Week of | Theme | Design problem | Behavioral output |
|:----:|---------|-------|----------------|-------------------|
| [1](weeks/week-01.md) | 12 Oct | Re-baseline | Cold redo of a friend-driven problem | Gap list, story 7 |
| [2](weeks/week-02.md) | 19 Oct | Trade-offs, influence | Uber / proximity | Stories 8, 9 |
| [3](weeks/week-03.md) | 26 Oct | Failure, **people round** | Retry storm; multi-region WhatsApp | 10 lead questions, story 10 |
| [4](weeks/week-04.md) | 2 Nov | Cost, scope | News feed + cost model + AI extension | Story 11, timeline story |
| [5](weeks/week-05.md) | 9 Nov | AI design I | RAG over enterprise documents | Story 12, incident at staff altitude |
| [6](weeks/week-06.md) | 16 Nov | **Deep dive**, AI design II | LLM inference API | Deep-dive package |
| [7](weeks/week-07.md) | 23 Nov | Migration, platform, team | 50 TB migration; platform for 30 teams | Stories 13, 14, weakness |
| [8](weeks/week-08.md) | 30 Nov | Polish | Re-score Week 1; six openings | Openers, questions, level answer |

**The two weeks not to skip:** [Week 3](weeks/week-03.md) and [Week 6](weeks/week-06.md).

---

## Part 4 — Mocks

| Week | Mock | With | Why here |
|------|------|------|----------|
| 1 | Design — a friend-driven problem, you as candidate | friend | Baseline at the staff bar |
| 2 | Coding — 2 mediums | self | Pace check |
| 3 | Behavioral, recorded | self | Catch verbal habits while there's time |
| 4 | **Design, external** | interviewing.io / Hello Interview / Pramp | First outside signal |
| 5 | Coding — 1 hard | self | Stamina on one deep problem |
| 6 | **Project deep dive** | friend, interrupting | The under-practiced round |
| 7 | Behavioral + hiring manager | friend | Level-setting conversation |
| 8 | **Full loop** | friend for ≥ 1 round | Context switching and stamina |

At least four of these with another human. Self-mocks never interrupt, never ask the follow-up you dread, and never look bored.

---

## Part 5 — Compressed variants

**4 weeks:** Weeks 1, 3, 6, 8. You keep the baseline, the people round, the deep dive and the full loop.

**2 weeks:** Deep-dive package · the 8 core stories (slots 1, 2, 3, 5, 6, 7, 9, 11) · redo two of the summer six cold with the rubric · RAG or LLM API once · the remaining must-know 15.

**1 week:** Deep-dive package · 8 stories · 2 designs (one classic, one AI) · 5 coding problems. Verbal rehearsal over new material.

---

## Part 6 — Progress tracker

Fill every Friday. Confidence 1–5; design score out of 24 from the rubric.

| Week | Coding (1–5) | Design /24 | Behavioral (1–5) | Stories written (of 14) | Mock done | Weakest thing this week |
|------|--------------|------------|------------------|-------------------------|-----------|-------------------------|
| 1 | | | | 6 → | | |
| 2 | | | | | | |
| 3 | | | | | | |
| 4 | | | | | | |
| 5 | | | | | | |
| 6 | | | | | | |
| 7 | | | | | | |
| 8 | | | | | | |

### Readiness gates

Ready for a target when **all** of its rows are true.

| | Senior | Team Lead | Staff |
|---|---|---|---|
| Coding | 2 mediums in 45 min, clean, aloud | 1 medium in 25 min | 1 medium in 25 min + one practical/API problem |
| AI-assisted round | can narrate, verify and catch a planted bug in 45 min | same | same |
| Design | 14+/24 on an unseen problem | 14+/24 plus a delivery plan | **16+/24 on three consecutive unseen problems**, one Tier C, one AI |
| Stories | 8, each ≤ 2 min, each with a number | 12 incl. all 4 people-management slots | 12, at least 4 at cross-team scope |
| Deep dive | can describe a project clearly | 45 min, survives interruption | 45 min, survives hostile "why not X?" at every decision |
| Altitude | — | lead column of Part 1 cold | switches altitude on demand, mid-answer |

A gate failing two weeks running is a *format* problem, not a study problem: add a human, a timer or a recording.

---

## Part 7 — Before the loop starts

- [ ] Ask the recruiter the **level the role is calibrated to** and what the design round expects at that level. This sets your altitude for the whole loop.
- [ ] Ask which rounds are in the loop, whether there is a project deep dive, and whether a design round is AI/ML-flavored.
- [ ] Ask **which rounds allow AI tools and which forbid them.** Using one where banned is misconduct.
- [ ] Have the deep-dive project chosen and drawn before round one.
- [ ] Three questions per interviewer type ([`leadership-behavioral.md` § Part 6](leadership-behavioral.md#part-6--questions-to-ask-them)).
- [ ] Sleep. Stamina shows in your Week 8 mock and it shows to them.
