# Week 6 — Real-time systems, and technical strategy

**Goal:** Stateful tiers and operability. The multi-quarter direction story.

**Due by Sunday**

- [ ] Chat design + operability spec
- [ ] Story 12: technical strategy

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Binary search

- [ ] [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
- [ ] [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) — binary search on the answer
- [ ] [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)

*Done when:* You spot "binary search on the answer" from the phrase "minimum X such that".

---

## Tue — Coding, 60 min · 1-D dynamic programming

- [ ] [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) → [House Robber](https://leetcode.com/problems/house-robber/)
- [ ] [Coin Change](https://leetcode.com/problems/coin-change/)
- [ ] [Word Break](https://leetcode.com/problems/word-break/)
- [ ] [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

*Done when:* You can state the recurrence out loud before writing any code.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] WebSocket / SSE / long-poll trade-offs, connection registry, sticky routing, reconnect storms
- [ ] Pub/sub fanout topologies; fanout-on-write vs on-read hybrids
- [ ] Component cards: **WebSocket layer**, **pub/sub**

### Timed problem, 45 min — no notes, on paper

**Chat system** at WhatsApp/Slack scale — 45 min.

### Staff move, 15 min · Operability

Specify: 3 SLIs with targets · the alert that fires first in an incident · the dashboard a responder opens · the deploy strategy for a stateful connection tier (you cannot just restart it) · the kill switch.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Read the Hello Interview chat breakdown + [Builders' Library — Implementing health checks](https://aws.amazon.com/builders-library/implementing-health-checks/).

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Technical strategy

- [ ] Story 12 — **a multi-quarter technical direction you set**: the doc you wrote, who adopted it, how you measured adoption
- [ ] If you have no org-scale version, write the honest team-scale one. **Do not inflate scope** — inflated scope collapses on the second follow-up question
- [ ] Run the [follow-up gauntlet](../leadership-behavioral.md#drill-4--the-follow-up-gauntlet) on it

*Done when:* The story survives "who disagreed with this direction, and what did they say?"

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**System design mock** — chat, cold. Mid-point altitude check against your Week 1 baseline.

---

## Week 6 gate

Design score has moved from your Week 1 baseline. If it hasn't, change the practice format — add a human.

---

[← Week 5](week-05.md) · [Week index](README.md) · [Week 7 →](week-07.md)
