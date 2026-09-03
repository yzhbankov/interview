# Week 3 — Partitioning, and conflict at scale

**Goal:** Multi-tenant thinking, blast radius, and rewriting existing stories one altitude up.

**Due by Sunday**

- [ ] Rate limiter as shared infrastructure
- [ ] Failure analysis with a named policy owner
- [ ] Two stories rewritten at lead/staff altitude, plus story 9

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Trees

- [ ] [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [ ] [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [ ] [Lowest Common Ancestor of a BST](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
- [ ] [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

*Done when:* You reach for BFS vs DFS without hesitating.

---

## Tue — Coding, 60 min · Tree DFS and serialization

- [ ] [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)
- [ ] [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [ ] [Construct Binary Tree from Preorder and Inorder](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

*Done when:* You can explain the return-value-vs-global-max distinction in path-sum problems.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] DDIA Ch. 6 (partitioning)
- [ ] [Dynamo paper](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) §4 — consistent hashing, quorums, hinted handoff
- [ ] Component cards: **Cassandra**, **consistent hashing**

### Timed problem, 45 min — no notes, on paper

**Rate limiter — as shared multi-tenant infrastructure used by 30 teams**, not a single-service utility. 45 min.

### Staff move, 15 min · Blast radius

Write the failure analysis: what breaks when the counter store partitions, does it fail open or closed, **who decides that policy**, what 30 client teams' retry behavior does to it, and what isolation stops one tenant starving the others.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Diff vs [`system-design/rate-limiter/structured.md`](../system-design/rate-limiter/structured.md) + [Builders' Library on fairness in multi-tenant systems](https://aws.amazon.com/builders-library/).

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Altitude rewrites + being wrong

- [ ] Rewrite [conflict with teammate](../star-stories/session-1-jan-22.md#2-conflict-with-teammate) at lead altitude — the mechanism you changed, not just the agreement you reached
- [ ] Rewrite [technology stack decision](../star-stories/session-1-jan-22.md#3-technology-stack-decision) at staff altitude — you already have disagree-and-commit; add the reversal condition
- [ ] Story 9 — **I was wrong and changed my mind**. Under-used, very high signal
- [ ] Run [Drill 2, the scoping ladder](../leadership-behavioral.md#drill-2--the-scoping-ladder) on all three

*Done when:* Each story exists in a senior and a lead/staff version, and you know which to use when.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Behavioral mock, recorded.** 5 questions, 2 min each. Watch it back — this is unpleasant and it is the highest-value 90 minutes so far.

---

## Week 3 gate

You caught at least two verbal habits on the recording (filler, "we" instead of "I", rambling past 2 minutes).

---

[← Week 2](week-02.md) · [Week index](README.md) · [Week 4 →](week-04.md)
