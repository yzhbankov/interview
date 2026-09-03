# Master Preparation Plan — Senior SWE / Team Lead / Staff Engineer (10 Weeks)

This is the **top-level schedule**. Every other file in this repo is a track that this plan
routes you into week by week.

| Track | Where the material lives | Weekly load |
|-------|--------------------------|-------------|
| Coding / algorithms | [`senior-software-engineer.md`](senior-software-engineer.md), [`algorithm-patterns.md`](algorithm-patterns.md) | 2 × 60 min |
| System design | [`staff-system-design.md`](staff-system-design.md), [`system-design/`](system-design/) | 1 × 90 min + 1 × 60 min |
| Leadership & behavioral | [`leadership-behavioral.md`](leadership-behavioral.md), [`star-stories/`](star-stories/) | 1 × 60 min |
| Mock / integration | this file, Part 4 | 1 × 90 min (Sat) |

### How to use this repo

| When | Open |
|------|------|
| **Right now, first time** | Part 0 and Part 1 below — the loop shapes and the altitude dial |
| **Every session** | [`weeks/week-NN.md`](weeks/) for the current week — that page is self-contained |
| **Every Friday, 15 min** | The [tracker](#part-6--progress-tracker) in this file |
| **Writing a story** | [`leadership-behavioral.md`](leadership-behavioral.md) + [`star-stories/TEMPLATE.md`](star-stories/TEMPLATE.md) |
| **Scoring a design** | [Rubric](staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |
| **Night before a real interview** | [Anti-patterns](leadership-behavioral.md#part-7--anti-patterns-that-cost-levels) and [Part 7](#part-7--before-the-loop-starts) below |

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

**The schedule lives in [`weeks/`](weeks/) — one page per week.** Open the current week and work
top to bottom; everything for that day is in that one file.

| Week | Theme | Design problem | Behavioral output |
|:----:|-------|----------------|-------------------|
| [1](weeks/week-01.md) | Baseline and calibration | URL shortener | Story inventory + gap list |
| [2](weeks/week-02.md) | Replication, consensus, influence | Job scheduler | Influence without authority |
| [3](weeks/week-03.md) | Partitioning, conflict at scale | Rate limiter (multi-tenant) | Altitude rewrites, "I was wrong" |
| [4](weeks/week-04.md) | Caching, **people & delivery round** | News feed | The 10 lead questions |
| [5](weeks/week-05.md) | Streaming, idempotency, pressure | Ad click aggregator | Saying no; scope traded |
| [6](weeks/week-06.md) | Real-time, technical strategy | Chat | Multi-quarter direction |
| [7](weeks/week-07.md) | Search/geo/OLAP, **project deep dive** | Uber proximity | Deep-dive package |
| [8](weeks/week-08.md) | Multi-region, failure, cost | Retry storm; multi-region | Incident at staff altitude |
| [9](weeks/week-09.md) | ML/AI design, team building | Recsys; RAG | Growing people, hiring bar |
| [10](weeks/week-10.md) | Migration, platform, polish | 50 TB migration; platform | Three openers, questions |

Design weeks map 1:1 onto [`staff-system-design.md`](staff-system-design.md) Part 3, so the
deeper reading list for week N is there if you want more than the week page carries.

**The two weeks not to skip:** [Week 4](weeks/week-04.md) and [Week 7](weeks/week-07.md) — the
people/delivery round and the project deep dive. They are the rounds that decide your level, and
neither is covered by coding or design practice.

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
