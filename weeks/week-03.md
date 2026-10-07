# Week 3 — Failure, and the people/delivery round

**Week of:** Mon 26 Oct 2026
**Goal:** Reason about cascading failure like an operator. Then the round that decides lead-level offers.

**Due by Sunday**

- [ ] Retry-storm redesign as a postmortem-style doc; multi-region version of WhatsApp
- [ ] Spoken answers to the 10 people/delivery questions, each with a mechanism
- [ ] Story 10

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Binary search

- [ ] [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
- [ ] [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) — binary search on the answer
- [ ] Stretch: [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)

*Done when:* "minimum X such that…" triggers binary-search-on-the-answer.

## Tue — Coding, 60 min · 1-D dynamic programming

- [ ] [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) → [House Robber](https://leetcode.com/problems/house-robber/)
- [ ] [Coin Change](https://leetcode.com/problems/coin-change/)
- [ ] [Word Break](https://leetcode.com/problems/word-break/)
- [ ] Stretch: [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

*Done when:* you state the recurrence aloud before writing code.

---

## Wed — System design, 90 min · Failure at scale

**Study, 20 min**

- [ ] [Builders' Library — Timeouts, retries, backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)
- [ ] [Builders' Library — Load shedding](https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/)
- [ ] Skim [SRE Book ch. 22, cascading failures](https://sre.google/sre-book/addressing-cascading-failures/)

**Timed, 45 min — two short problems**

1. *"We had a 4-hour outage from a retry storm. Redesign so it can't recur."* (25 min) — retry budgets, jitter, circuit breakers, load shedding, isolation.
2. *"Make WhatsApp multi-region."* (20 min) — use the summer design as input: failure domains, active-active vs active-passive, where message ordering breaks, RTO/RPO.

**Staff move, 15 min — postmortem as design doc**

Write the retry-storm answer as a postmortem: contributing factors, design changes, and the detection that would have caught it in 5 minutes instead of 4 hours.

**Score, 10 min** — rubric on the retry storm; compare your detection story to how you really found the incident in your [production-incident story](../star-stories/session-1-jan-22.md#1-production-incident). That gap is Week 5's Thursday material.

*Done when:* score, three misses, and a one-page postmortem.

---

## Thu — Behavioral, 60 min · The people & delivery round

Do this week even if you only target staff — staff loops probe mentorship and delivery too.

- [ ] Answer all 10 of [Round B](../leadership-behavioral.md#round-b--people--delivery-team-lead--tlm--the-round-most-under-prepared) **out loud**
- [ ] **Story 10 — underperformance or difficult feedback**
- [ ] Extend the [hiring & onboarding story](../star-stories/session-1-jan-22.md#4-hiring-and-onboarding) with the *hiring bar* decision: who you said no to, and why
- [ ] Check every answer names a **mechanism** — a cadence, a doc, a rubric, a dated plan — not good intentions

*Done when:* none of your Round B answers sound like an IC who happens to help people.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] One weakest thing; one fix

---

## Sat — Mock, 90 min · Behavioral, recorded

5 questions (mix Round A and Round B), 2 min each, on camera. Watch it back the same day. Unpleasant, and the highest-value 90 minutes so far.

---

## Week 3 gate

You caught at least two verbal habits on the recording (filler, "we" for "I", running past 2 minutes), and every Round B answer has a mechanism in it.

---

[← Week 2](week-02.md) · [Week index](README.md) · [Week 4 →](week-04.md)
