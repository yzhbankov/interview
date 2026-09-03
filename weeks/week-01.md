# Week 1 — Baseline and calibration

**Goal:** Measure, don't study. You cannot aim the next nine weeks without a baseline.

**Due by Sunday**

- [ ] Rubric score on the URL shortener (your baseline)
- [ ] Written behavioral gap list (which of the 14 story slots you're missing)
- [ ] Coding fluency check: how many of 6 problems landed in time

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Arrays & hashing

- [ ] [Two Sum](https://leetcode.com/problems/two-sum/)
- [ ] [Group Anagrams](https://leetcode.com/problems/group-anagrams/)
- [ ] [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
- [ ] Pattern note: when HashMap beats sorting for lookup problems

*Done when:* You solved all three without looking anything up, and you know which ones were slow.

---

## Tue — Coding, 60 min · Two pointers & sliding window

- [ ] [3Sum](https://leetcode.com/problems/3sum/)
- [ ] [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [ ] [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
- [ ] Pattern note: the sliding window template (expand right, shrink left, track answer)

*Done when:* You can write the sliding-window skeleton from memory.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] [System Design in a Hurry](https://www.hellointerview.com/learn/system-design/in-a-hurry/introduction) — *Core Concepts* and *Delivery Framework*
- [ ] [5 Keys to Staff-Level System Design](https://www.hellointerview.com/blog/staff-level-system-design)
- [ ] DDIA Ch. 3 — storage engines, B-tree vs LSM
- [ ] Self-quiz the existing cards: [Postgres](../system-design/components/postgres.md), [DynamoDB](../system-design/components/dynamodb.md). Cover the page and recall the hard limit of each

### Timed problem, 45 min — no notes, on paper

**URL shortener** — 45 min, no notes, on paper. You have notes on this already; that is the point. You are measuring altitude, not knowledge.

### Staff move, 15 min · Quantification drives design

Rewrite the design so every architectural choice cites a number you computed. If a number does not justify a component, delete the component.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Diff against [`system-design/url-shortener/structured.md`](../system-design/url-shortener/structured.md). Score with the [Part 0 rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem). Expect a low score on rows 4–6 — that is your baseline, not a failure.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Story inventory — the most important behavioral hour in the plan

- [ ] Read [`leadership-behavioral.md` § Part 1](../leadership-behavioral.md#part-1--story-portfolio-the-14-slots)
- [ ] Map your six existing stories in [`star-stories/`](../star-stories/) onto the 14 slots
- [ ] Write the gap list: which slots are empty, and which weeks below will fill them
- [ ] Label each existing story senior / lead / staff using [the altitude table](../interview-prep-plan.md#part-1--the-altitude-dial)

*Done when:* You have a written list of exactly which stories you still owe yourself.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Coding mock**, 45 min: 2 mediums back-to-back, spoken out loud, no IDE.

---

## Week 1 gate

You have three baseline numbers written down (design score, coding count, story gaps). Nothing here needs to be good yet.

---

[Week index](README.md) · [Week 2 →](week-02.md)
