# Week 7 — Search/geo/OLAP, and the project deep dive

**Goal:** The staff-critical week. The deep dive is where staff offers are won and lost, and it is almost never practiced.

**Due by Sunday**

- [ ] Uber/proximity design
- [ ] 90-second scope negotiation, rehearsed three ways
- [ ] The full deep-dive package for one project

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · 2-D dynamic programming

- [ ] [Unique Paths](https://leetcode.com/problems/unique-paths/)
- [ ] [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
- [ ] [Edit Distance](https://leetcode.com/problems/edit-distance/)

*Done when:* You can draw the DP table and explain one cell's derivation.

---

## Tue — Coding, 60 min · Backtracking

- [ ] [Subsets](https://leetcode.com/problems/subsets/) and [Permutations](https://leetcode.com/problems/permutations/)
- [ ] [Combination Sum](https://leetcode.com/problems/combination-sum/)
- [ ] [Word Search](https://leetcode.com/problems/word-search/)
- [ ] [N-Queens](https://leetcode.com/problems/n-queens/)

*Done when:* The choose / explore / un-choose skeleton is muscle memory.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] Inverted indexes, index build pipelines, near-real-time indexing
- [ ] Geospatial indexing: geohash, S2, quadtree
- [ ] OLAP vs OLTP, columnar storage, pre-aggregation
- [ ] Component cards: **Elasticsearch**, **geo index**, **OLAP store (ClickHouse/Druid)**

### Timed problem, 45 min — no notes, on paper

**Uber / proximity matching service** — 45 min.

### Staff move, 15 min · Scope negotiation

Re-open the problem and practice **the opening five minutes only**, three different ways: narrow to matching · narrow to location ingestion · narrow to trip lifecycle. Each time state what you're cutting and why. Out loud, timed, until you can do it in 90 seconds.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

List 3 misses. Then pick the project you'll use for Thursday's deep dive.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · The project deep dive (90 min — take the extra time)

- [ ] Read [`leadership-behavioral.md` § Part 3](../leadership-behavioral.md#part-3--the-project-deep-dive-45-minutes-staff-critical)
- [ ] Choose the project against the five criteria (you decided, it was ambiguous, something went wrong, there's a number, you can draw it)
- [ ] Write the one-page brief covering all seven rows of the 45-minute structure
- [ ] Draw the architecture from memory. Twice. No notes
- [ ] Three decisions, each with: the alternative, the deciding factor, the reversal condition
- [ ] Verify every number you plan to cite — never state one you can't defend two questions deep

*Done when:* You can run the whole 45 minutes without notes, and every number is defensible.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Mock #2 — deep dive.** Have someone interrupt with "why not X?" every five minutes. That is the actual format of the round.

---

## Week 7 gate

The deep dive survived hostile interruption without you losing the thread or inventing a number.

---

[← Week 6](week-06.md) · [Week index](README.md) · [Week 8 →](week-08.md)
