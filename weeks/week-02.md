# Week 2 — Trade-offs and influence

**Week of:** Mon 19 Oct 2026
**Goal:** Make the three-line trade-off automatic. Fill the two influence slots that staff loops probe hardest.

**Due by Sunday**

- [ ] Uber / proximity design, scored
- [ ] Story 8 (alignment across teams) and story 9 (I was wrong)

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Heaps

- [ ] [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)
- [ ] [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
- [ ] [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) — two heaps
- [ ] Stretch: [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

*Done when:* "top k" and "streaming median" read as heap problems instantly.

## Tue — Coding, 60 min · Intervals

- [ ] [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [ ] [Insert Interval](https://leetcode.com/problems/insert-interval/)
- [ ] [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [ ] [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

*Done when:* sort-by-start vs sort-by-end is a deliberate decision.

---

## Wed — System design, 90 min · Uber / proximity

**Study, 20 min**

- [ ] Geospatial indexing: geohash vs S2 vs quadtree — when each wins
- [ ] Driver state machine and matching under contention (two riders, one driver)

**Timed, 45 min — no notes, on paper**

**Uber: location ingestion + nearby-driver matching.** Numbers first: drivers online, location updates/sec, match latency target.

**Staff move, 15 min — scope negotiation**

Re-open the problem and do **the first five minutes only**, three ways: narrow to matching · narrow to location ingestion · narrow to trip lifecycle. Each time say what you're cutting and why. Out loud, timed, until it fits in 90 seconds.

**Score, 10 min** — rubric, three misses, then the reference breakdown.

*Done when:* you have a score and can open the problem three different ways in 90 seconds each.

---

## Thu — Behavioral, 60 min · Influence stories

- [ ] **Story 8 — alignment on a contentious decision across teams**: two parties wanted different things; you reframed the decision around something both owned
- [ ] **Story 9 — I was wrong and changed my mind**: under-used, very high signal. What changed your mind, what it cost, what you do differently now
- [ ] Run [Drill 3, the "so what?" ladder](../leadership-behavioral.md#drill-3--the-so-what-ladder) on both results

*Done when:* both stories fit in 2 minutes spoken and end with a number.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] One weakest thing; one fix

---

## Sat — Mock, 90 min · Coding

2 mediums back to back, 45 min, spoken aloud, plain editor. Pick from patterns you have *not* done this week. 15 min: what cost you time?

---

## Week 2 gate

You can say *chose / gave up / would reverse if* about any component without thinking about it.

---

[← Week 1](week-01.md) · [Week index](README.md) · [Week 3 →](week-03.md)
