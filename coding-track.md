# Coding Track

Problem sets and session rules for the Mon/Tue coding blocks in [`weeks/`](weeks/). Pattern
templates with code are in [`algorithm-patterns.md`](algorithm-patterns.md).

**The bar.** Senior: 2 mediums in 45 min, clean, spoken aloud. Lead/staff: 1 medium in ~25 min
plus one practical, API-shaped problem. Coding is a filter, not the level-setter; do not let it
eat the behavioral hours.

---

## Where you are (Oct 2026)

| Pattern | Status | Covered |
|---------|--------|---------|
| Arrays & hashing | ✅ done | Two Sum, Contains Duplicate, Group Anagrams, Product of Array Except Self, Max Subarray, Best Time to Buy/Sell |
| Two pointers & sliding window | ✅ done | 3Sum, Container With Most Water, Longest Substring Without Repeating |
| Linked lists | ✅ done | Reverse, Merge Two Sorted, Cycle, Remove Nth From End |
| Stacks & monotonic stack | ✅ done | Valid Parentheses, Min Stack, Daily Temperatures, RPN |
| Trees | ✅ done | Level Order, Validate BST, LCA, Diameter, Serialize/Deserialize, Max Path Sum, Build from Pre/In |
| Graphs, union-find, topo sort | Week 1 | |
| Heaps, intervals | Week 2 | |
| Binary search, 1-D DP | Week 3 | |
| 2-D DP, backtracking | Week 4 | |
| Tries, LRU, practical API coding | Week 5 | |
| **AI-assisted coding, code comprehension** | Week 6 | new round types, see below |
| Mixed timed set, concurrency & OOD | Week 7 | |
| Repair pass, bar check | Week 8 | |

---

## Must-know 15

If you solve nothing else before a loop, make sure every row is ✅.

| # | Problem | Pattern | Status |
|---|---------|---------|--------|
| 1 | [Two Sum](https://leetcode.com/problems/two-sum/) | HashMap | ✅ |
| 2 | [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) | Linked list | ✅ |
| 3 | [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Sliding window | ✅ |
| 4 | [3Sum](https://leetcode.com/problems/3sum/) | Two pointers | ✅ |
| 5 | [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) | Tree DFS | ✅ |
| 6 | [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | Tree BFS/DFS | ✅ |
| 7 | [Number of Islands](https://leetcode.com/problems/number-of-islands/) | BFS/DFS | Week 1 |
| 8 | [Course Schedule](https://leetcode.com/problems/course-schedule/) | Topological sort | Week 1 |
| 9 | [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) | Two heaps | Week 2 |
| 10 | [Merge Intervals](https://leetcode.com/problems/merge-intervals/) | Sorting | Week 2 |
| 11 | [Coin Change](https://leetcode.com/problems/coin-change/) | 1-D DP | Week 3 |
| 12 | [Word Break](https://leetcode.com/problems/word-break/) | 1-D DP | Week 3 |
| 13 | [Edit Distance](https://leetcode.com/problems/edit-distance/) | 2-D DP | Week 4 |
| 14 | [LRU Cache](https://leetcode.com/problems/lru-cache/) | HashMap + DLL | Week 5 |
| 15 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Two pointers / stack | Week 8 |

---

## The 60-minute session

| Minutes | Do |
|---------|----|
| 0–5 | Read twice. Write inputs, outputs, constraints, two edge cases. |
| 5–10 | Two worked examples by hand, one of them an edge case. |
| 10–20 | Say the brute force, then the optimization, **out loud**. Name the pattern. |
| 20–40 | Code in a plain editor — no autocomplete, no running. |
| 40–50 | Trace one example through the code by hand. Fix what you find. |
| 50–55 | Time and space complexity, with the reason. |
| 55–60 | One-line pattern note: *"this problem teaches me…"* |

Stuck past 20 min: read a hint, not a solution. Past 35: read the solution, close it, re-implement
from memory, and re-solve two days later.

**Speaking template.** *"Let me restate… I'm thinking [approach] because [reason]. That's O(n)
because… Let me code it."* Silence scores zero.

---

## Practical / API-shaped problems (lead and staff loops)

Increasingly what lead and staff rounds ask instead of pure LeetCode. Name the interface first,
write tests, no tricks.

- In-process **token-bucket rate limiter** with a clean public interface
- **Retry with exponential backoff and jitter**, cancellable
- **Bounded LRU with TTL** and eviction callbacks
- **Thread-safe bounded blocking queue** — state the invariants before coding
- **Parking lot / elevator** object design — justify each class boundary by what it lets you change later

*Done when:* someone else could use your API from the signature alone.

---

## The two new round types (2026)

### AI-assisted coding round

Meta, Canva and Shopify run it; Google is piloting it. You get Cursor, Copilot, Claude Code or a
company tool and a realistic task — build a small feature, extend a codebase — and you are scored
on **how you direct the tool, how you verify its output, and whether you catch the bugs it
introduces**, not on typing speed. It is usually paired with a classic, tool-free round, so the
fundamentals above still matter.

How to practice (Week 6 Mon, 60 min):

1. Pick a task you could do by hand in ~2 hours. Example: *a CLI that ingests CSV request logs and
   reports the top-k error codes per minute, with tests.*
2. 45 min with the tool. Narrate every prompt and every decision as if the interviewer is listening:
   what you asked for, why, what you checked before accepting.
3. 15 min: list every bug, missing edge case or questionable design choice the tool produced and
   what you did about it. If the list is empty, you were not reading the output.

Rules that get candidates rejected: accepting generated code unread · letting the tool choose the
architecture · going silent while it generates · not writing or running a single test yourself.

### Code comprehension round

You are handed an unfamiliar module and asked to explain what it does, find the bug, and extend
it. Practice (Week 6 Tue): take ~500 lines from an open-source project you don't know, 25 min to
explain its data flow out loud, then find one real issue (a race, an unhandled error path, an
off-by-one). Read first, grep second, run last.

**Before any loop:** ask the recruiter *which rounds allow AI tools and which forbid them.* Using
one where it's banned is treated as misconduct.

---

## Pattern recognition

| If you see… | Think… |
|-------------|--------|
| Pair / subarray with sum X | HashMap, two pointers |
| Contiguous subarray or substring | Sliding window |
| "Minimum X such that…" / sorted input | Binary search, often on the answer |
| Tree by level / by path | BFS / DFS |
| Connectivity, components | BFS/DFS, union-find |
| Dependencies, ordering | Topological sort |
| All combinations / permutations | Backtracking |
| Optimal substructure, overlapping subproblems | DP — state, recurrence, base case, order |
| K largest / smallest, streaming median | Heap |
| Prefix matching | Trie |
| Overlapping ranges | Sort by start, sweep |

---

## Recovery rules

| Situation | Action |
|-----------|--------|
| Same pattern failed twice | Stop. 30 min on the template in `algorithm-patterns.md`, then retry |
| Solved but > 45 min | Re-solve tomorrow from memory; target < 25 min |
| Couldn't solve | Read solution → wait two days → solve from scratch |
| Same mistake recurring | Mistake log: pattern, mistake, fix. Re-read before each mock |

---

## Resources

- [NeetCode roadmap](https://neetcode.io/roadmap) — problem order and video explanations
- [LeetCode](https://leetcode.com/) — all problems above
- [Hello Interview — AI-enabled coding interviews](https://www.hellointerview.com/learn/ai-coding/overview/introduction) — the new round, from interviewers who run it
- [Big-O cheat sheet](https://www.bigocheatsheet.com/)
