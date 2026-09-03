# Week 4 — Caching, and the people/delivery round

**Goal:** Cost modelling in design; the team-lead people round in behavioral. Do this week even if you are targeting staff — staff loops probe mentorship and delivery too.

**Due by Sunday**

- [ ] News feed design + one-page cost model
- [ ] Answers to the 10 people/delivery questions
- [ ] Mock #1 booked and done with a human

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Graphs

- [ ] [Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [ ] [Clone Graph](https://leetcode.com/problems/clone-graph/)
- [ ] [Course Schedule](https://leetcode.com/problems/course-schedule/) — topological sort

*Done when:* You can write Kahn's algorithm from memory.

---

## Tue — Coding, 60 min · Union-Find & implicit graphs

- [ ] [Number of Connected Components](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/) — union-find with path compression
- [ ] [Word Ladder](https://leetcode.com/problems/word-ladder/) — BFS on an implicit graph
- [ ] [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)

*Done when:* You can state when union-find beats DFS.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] [Builders' Library — Caching challenges and strategies](https://aws.amazon.com/builders-library/caching-challenges-and-strategies/)
- [ ] Cache invalidation patterns, thundering herd, negative caching, TTL jitter
- [ ] Component cards: **Redis**, **CDN**

### Timed problem, 45 min — no notes, on paper

**News feed / home timeline** — 45 min. Fanout-on-write vs fanout-on-read, and the hybrid for celebrity accounts.

### Staff move, 15 min · Cost modelling

One page: storage, cache footprint, egress, compute. Name the dominant line item and one change that halves it — then state what quality that change costs you.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Read a reference breakdown, list 3 misses. Then answer the [team-lead lens](../staff-system-design.md#using-this-plan-at-three-altitudes) four questions in writing.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · The people & delivery round

- [ ] Work [`leadership-behavioral.md` § Round B](../leadership-behavioral.md#round-b--people--delivery-team-lead--tlm--the-round-most-under-prepared) — answer all 10 out loud
- [ ] Story 10 — **underperformance or difficult feedback**
- [ ] Extend your [hiring & onboarding story](../star-stories/session-1-jan-22.md#4-hiring-and-onboarding) with the *hiring bar* decision: who you said no to, and why
- [ ] Check every answer contains a **mechanism** — a cadence, a doc, a rubric, a dated plan — not just good intentions

*Done when:* None of your answers to Round B sound like an IC who happens to help people.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Mock #1 — external.** Pramp / interviewing.io / a peer. Any Tier B problem. Ask explicitly to be scored at staff level.

---

## Week 4 gate

You have a real outside score, and it either confirms or corrects your Week 1 self-assessment.

---

[← Week 3](week-03.md) · [Week index](README.md) · [Week 5 →](week-05.md)
