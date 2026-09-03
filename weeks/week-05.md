# Week 5 — Streaming, idempotency, and delivery under pressure

**Goal:** Correctness under failure when money is involved. Scope-cutting stories.

**Due by Sunday**

- [ ] Ad click aggregator with billing correctness
- [ ] End-to-end exactly-once write-up
- [ ] Story 11: saying no; refined timeline story

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Heaps

- [ ] [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)
- [ ] [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) — two heaps
- [ ] [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
- [ ] [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

*Done when:* You recognize "top k / streaming median" as a heap problem instantly.

---

## Tue — Coding, 60 min · Intervals

- [ ] [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [ ] [Insert Interval](https://leetcode.com/problems/insert-interval/)
- [ ] [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [ ] [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

*Done when:* Sort-by-start vs sort-by-end is a decision you make deliberately.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] DDIA Ch. 11 (stream processing)
- [ ] [Kafka design docs](https://kafka.apache.org/documentation/#design) — partitions, ISR, exactly-once
- [ ] Watermarks, windowing, late events, backpressure
- [ ] Component cards: **Kafka**, **Flink**

### Timed problem, 45 min — no notes, on paper

**Ad click aggregator** (revisit) with **billing correctness as a hard requirement**. 45 min.

### Staff move, 15 min · Correctness under failure

Write the exactly-once story end to end: producer idempotency → consumer offset semantics → sink idempotency → the reconciliation batch job that is the actual source of truth. State the money consequence of getting each one wrong.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Diff vs [`system-design/ad-click-aggregator/structured.md`](../system-design/ad-click-aggregator/structured.md). Read one [Jepsen analysis](https://jepsen.io/analyses) to calibrate what "exactly once" claims are worth.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Scope and stakeholders

- [ ] Refine [the timeline story](../star-stories/session-2-project-timeline.md) — good reflection already; it needs a number and the *scope you traded*
- [ ] Story 11 — **saying no / managing a demanding stakeholder**
- [ ] For both: what happened to the thing you cut? (Interviewers ask this and most candidates have no answer)

*Done when:* Both stories end with a measurable outcome, not "we delivered on time".

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Coding mock** — 1 hard problem, 35 min, out loud.

---

## Week 5 gate

You can explain exactly-once end to end without hand-waving the reconciliation step.

---

[← Week 4](week-04.md) · [Week index](README.md) · [Week 6 →](week-06.md)
