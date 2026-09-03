# Week 10 — Migration, platform, polish, and closing

**Goal:** Migration questions are the purest staff signal there is. Then polish and simulate a full loop.

**Due by Sunday**

- [ ] Zero-downtime migration design
- [ ] Platform design
- [ ] Three openers, questions for interviewers, re-scored Week 1 problem

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Repair pass

- [ ] Re-solve, from memory, the 5 problems you got wrong or slow in Weeks 1–9
- [ ] For each: what specifically went wrong the first time?

*Done when:* The repeat attempts are clean and fast.

---

## Tue — Coding, 60 min · Bar check

- [ ] 2 mediums in 45 minutes, strict timer, spoken out loud
- [ ] That is the senior coding bar. Confirm you clear it

*Done when:* You cleared it, or you know exactly which pattern cost you the time.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] [Martin Fowler — Strangler Fig](https://martinfowler.com/bliki/StranglerFigApplication.html)
- [ ] CDC-based migration, dual writes, shadow reads, backfill + verify, cutover, rollback

### Timed problem, 45 min — no notes, on paper

Two Tier C problems. (1) *"Migrate 50 TB from MySQL to a distributed store with zero downtime."* (2) *"Design a platform 30 teams will build on"* — interface contracts, guardrails, deprecation policy, and where on-call load lands.

### Staff move, 15 min · Reversibility

For the migration: what is the rollback at *each* phase, and what is the last point at which rollback is still cheap? Say it unprompted — it is the single most staff-sounding thing in a migration answer.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Re-score your **Week 1 URL shortener** answer against the rubric and compare to your baseline. That delta is your evidence of readiness.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Final polish

- [ ] Write all three [openers](../leadership-behavioral.md#part-5--openers): senior, lead, staff. 60–90 seconds each, out loud
- [ ] Three [questions per interviewer type](../leadership-behavioral.md#part-6--questions-to-ask-them)
- [ ] Leveling and compensation framing — the two-sentence scope answer
- [ ] Re-read the [anti-pattern table](../leadership-behavioral.md#part-7--anti-patterns-that-cost-levels) the night before any real loop

*Done when:* You can open any interview in three different registers on demand.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Mock #3 — full loop simulation.** Coding → design → behavioral, back to back, in one morning. Stamina is a real variable and it is testable.

---

## Week 10 gate

Check the [readiness gates](../interview-prep-plan.md#readiness-gates) for your target role. Every row true, or you know which row to work on.

---

[← Week 9](week-09.md) · [Week index](README.md)
