# Week 7 — Migration, platform, and growing people

**Week of:** Mon 23 Nov 2026
**Goal:** Migration questions are the purest staff signal. Finish the people stories and the weakness answer.

**Due by Sunday**

- [ ] Zero-downtime migration design with rollback at every phase; platform design
- [ ] Stories 13 and 14; weakness rehearsed
- [ ] All 14 slots filled — count them

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Mixed timed set

- [ ] 3 random mediums, 60 min total, no hints, no editorial until the end
- [ ] Log the pattern and time for each

*Done when:* you know your honest medium-problem pace.

## Tue — Coding, 60 min · Concurrency & object design

- [ ] Thread-safe bounded blocking queue — invariants first, then code
- [ ] Parking lot or elevator: classes, interfaces, extension points. Verbalize every boundary

*Done when:* you justify each class boundary by what it lets you change later.

---

## Wed — System design, 90 min · Migration and platform

**Study, 20 min**

- [ ] [Martin Fowler — Strangler Fig](https://martinfowler.com/bliki/StranglerFigApplication.html)
- [ ] CDC vs dual writes, shadow reads, backfill + verify, cutover, rollback — one line each

**Timed, 45 min — two Tier C problems**

1. *"Migrate 50 TB from MySQL to a distributed store with zero downtime."* (25 min)
2. *"Design a platform 30 teams will build on."* (20 min) — interface contracts, guardrails vs gates, deprecation policy, where on-call load lands.

**Staff move, 15 min — reversibility**

For the migration: the rollback at *each* phase, and the last point at which rollback is still cheap. Say it unprompted; it is the most staff-sounding sentence in a migration answer.

**Score, 10 min** — rubric on the migration, three misses.

*Done when:* score, misses, and a phase table with a rollback column.

---

## Thu — Behavioral, 60 min · Growing people, bad data, weakness

- [ ] **Story 13 — mentoring someone to a promotion / growing the team**. Staff framing: how you *scale yourself* — docs, defaults, review standards, things that outlived you
- [ ] **Story 14 — a decision made with incomplete or bad data**
- [ ] [Drill 5](../leadership-behavioral.md#drill-5--weakness-rehearsal): the real weakness, with the mechanism and the evidence it works
- [ ] A 60-second answer to *"how do you and your team use AI tools, and where don't you trust them?"* — now a standard question; have a specific example of each

*Done when:* all 14 slots in [Part 1](../leadership-behavioral.md#part-1--story-portfolio-the-14-slots) point at a written story.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] One weakest thing; one fix

---

## Sat — Mock, 90 min · Behavioral + hiring manager

45 min with your friend as hiring manager, including *"what level do you think you're at?"*, *"walk me through your career"* and *"what are your questions for me?"* Then 45 min of Round C (staff influence) questions.

---

## Week 7 gate

Fourteen stories, and a two-sentence level answer that names scope, not years.

---

[← Week 6](week-06.md) · [Week index](README.md) · [Week 8 →](week-08.md)
