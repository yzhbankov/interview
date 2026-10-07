# Week 4 — Cost and scope

**Week of:** Mon 2 Nov 2026
**Goal:** Put a dollar figure on a design. Get your first outside signal. Finish the stakeholder stories.

**Due by Sunday**

- [ ] News feed redo + one-page cost model + the AI extension
- [ ] Story 11; timeline story refined
- [ ] External mock booked and done

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · 2-D dynamic programming

- [ ] [Unique Paths](https://leetcode.com/problems/unique-paths/)
- [ ] [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
- [ ] [Edit Distance](https://leetcode.com/problems/edit-distance/)

*Done when:* you can draw the table and derive one cell aloud.

## Tue — Coding, 60 min · Backtracking

- [ ] [Subsets](https://leetcode.com/problems/subsets/) and [Permutations](https://leetcode.com/problems/permutations/)
- [ ] [Combination Sum](https://leetcode.com/problems/combination-sum/)
- [ ] [Word Search](https://leetcode.com/problems/word-search/)
- [ ] Stretch: [N-Queens](https://leetcode.com/problems/n-queens/)

*Done when:* choose / explore / un-choose is muscle memory.

---

## Wed — System design, 90 min · News feed, with a price tag

**Study, 20 min**

- [ ] [Builders' Library — Caching challenges and strategies](https://aws.amazon.com/builders-library/caching-challenges-and-strategies/)
- [ ] Order-of-magnitude unit costs you can quote: object storage per TB-month, egress per TB, a cache node, a mid-size VM. Write them on an index card

**Timed, 35 min — no notes**

**News feed / home timeline**, redo from the summer. Fanout-on-write vs on-read and the celebrity hybrid. Spend the saved 10 minutes on the cost model below.

**Staff move, 15 min — cost model**

One page: storage, cache footprint, egress, compute. Name the dominant line item and one change that halves it, then say what quality that change costs.

**AI extension, 10 min — the 2026 follow-up**

*"Now add an LLM-generated 'what you missed' summary to the feed."* Where does the model call sit, sync or async? What happens when the model is down or slow? Cost per summary × DAU — is it affordable? What do you cache? This question is now common at the end of classic designs; practice bolting AI onto something that already works.

**Score, 10 min** — rubric, three misses, then the [team-lead four questions](../staff-system-design.md#using-this-plan-at-three-altitudes) in writing.

*Done when:* score, misses, a cost page with a dominant line item, and an answer to the AI extension.

---

## Thu — Behavioral, 60 min · Scope and stakeholders

- [ ] **Story 11 — saying no / managing a demanding stakeholder**
- [ ] Refine the [timeline story](../star-stories/session-2-project-timeline.md): add the number and the *scope you traded*
- [ ] For both: *what happened to the thing you cut?* Interviewers ask; most candidates have no answer

*Done when:* both end with a measurable outcome, not "we delivered on time".

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] One weakest thing; one fix

---

## Sat — Mock, 90 min · External design mock

[interviewing.io](https://interviewing.io/), [Hello Interview](https://www.hellointerview.com/premium) or [Pramp](https://www.pramp.com/). Any Tier B problem you haven't done (Ticketmaster, payment ledger, Google Docs). Ask explicitly to be scored at staff level. If you can't book one, have your friend run **Ticketmaster** unseen.

---

## Week 4 gate

You have a real outside score, and it confirms or corrects your Week 1 baseline.

---

[← Week 3](week-03.md) · [Week index](README.md) · [Week 5 →](week-05.md)
