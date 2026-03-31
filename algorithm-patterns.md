# Algorithm Patterns Cheat Sheet

Quick reference for the most common interview patterns with templates and when to use them.

---

## 1. Two Pointers

**When to use:** Sorted arrays, finding pairs, partitioning, palindromes.

```
left, right = 0, len(arr) - 1
while left < right:
    if condition_met(arr[left], arr[right]):
        return result
    elif need_larger:
        left += 1
    else:
        right -= 1
```

**Key problems:** Two Sum (sorted), 3Sum, Container With Most Water, Trapping Rain Water

---

## 2. Sliding Window

**When to use:** Contiguous subarray/substring with a constraint (max, min, exact count).

```
left = 0
window = {}  # or counter
answer = 0

for right in range(len(s)):
    # expand: add s[right] to window
    window[s[right]] = window.get(s[right], 0) + 1

    # shrink: while window is invalid
    while window_is_invalid():
        # remove s[left] from window
        window[s[left]] -= 1
        left += 1

    # update answer
    answer = max(answer, right - left + 1)
```

**Key problems:** Longest Substring Without Repeating Characters, Minimum Window Substring, Max Consecutive Ones III

---

## 3. Binary Search

**When to use:** Sorted array, "find min/max that satisfies condition", search space reduction.

```
left, right = 0, len(arr) - 1
while left <= right:
    mid = left + (right - left) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        left = mid + 1
    else:
        right = mid - 1
return -1  # or left for insertion point
```

**Variant — search on answer:**
```
left, right = min_answer, max_answer
while left < right:
    mid = left + (right - left) // 2
    if feasible(mid):
        right = mid      # try smaller
    else:
        left = mid + 1   # need larger
return left
```

**Key problems:** Binary Search, Search in Rotated Sorted Array, Koko Eating Bananas, Median of Two Sorted Arrays

---

## 4. BFS (Breadth-First Search)

**When to use:** Shortest path (unweighted), level-order traversal, spreading from multiple sources.

```
from collections import deque

queue = deque([start])
visited = {start}

while queue:
    node = queue.popleft()
    for neighbor in get_neighbors(node):
        if neighbor not in visited:
            visited.add(neighbor)
            queue.append(neighbor)
```

**Key problems:** Level Order Traversal, Number of Islands, Rotting Oranges, Word Ladder

---

## 5. DFS (Depth-First Search)

**When to use:** Explore all paths, connected components, tree traversal, cycle detection.

```
def dfs(node, visited):
    if node in visited:
        return
    visited.add(node)
    for neighbor in get_neighbors(node):
        dfs(neighbor, visited)
```

**Key problems:** Number of Islands, Clone Graph, Pacific Atlantic Water Flow, Course Schedule

---

## 6. Backtracking

**When to use:** All combinations, permutations, subsets, constraint satisfaction.

```
def backtrack(candidates, path, result):
    if is_solution(path):
        result.append(path[:])
        return

    for i, candidate in enumerate(candidates):
        if not is_valid(candidate):
            continue
        path.append(candidate)           # choose
        backtrack(candidates[i:], path, result)  # explore
        path.pop()                        # un-choose
```

**Key problems:** Subsets, Combination Sum, Permutations, Word Search, N-Queens

---

## 7. Dynamic Programming

**Framework:**
1. **Define state:** What does `dp[i]` (or `dp[i][j]`) represent?
2. **Recurrence:** How does `dp[i]` relate to previous states?
3. **Base case:** What are the starting values?
4. **Order:** Fill table left-to-right, bottom-to-top, etc.
5. **Answer:** Where is the answer in the table?

**Common patterns:**

| Pattern | Example | Recurrence |
|---------|---------|------------|
| Linear | Climbing Stairs | `dp[i] = dp[i-1] + dp[i-2]` |
| Knapsack | Coin Change | `dp[i] = min(dp[i], dp[i-coin] + 1)` |
| LIS | Longest Increasing Subsequence | `dp[i] = max(dp[j] + 1) for j < i where a[j] < a[i]` |
| Grid | Unique Paths | `dp[i][j] = dp[i-1][j] + dp[i][j-1]` |
| String | Edit Distance | `dp[i][j] = dp[i-1][j-1] if match, else 1 + min(insert, delete, replace)` |

**Key problems:** Climbing Stairs, Coin Change, Longest Increasing Subsequence, House Robber, Edit Distance, Longest Common Subsequence

---

## 8. Monotonic Stack

**When to use:** "Next greater/smaller element", histogram problems, temperature problems.

```
stack = []  # stores indices
result = [-1] * len(arr)

for i in range(len(arr)):
    while stack and arr[i] > arr[stack[-1]]:
        idx = stack.pop()
        result[idx] = arr[i]  # arr[i] is next greater for arr[idx]
    stack.append(i)
```

**Key problems:** Daily Temperatures, Next Greater Element, Largest Rectangle in Histogram

---

## 9. Heap / Priority Queue

**When to use:** "K largest/smallest", merge K sorted lists, scheduling, running median.

```
import heapq

# Min heap (default in Python)
heap = []
heapq.heappush(heap, value)
smallest = heapq.heappop(heap)

# Max heap: negate values
heapq.heappush(heap, -value)
largest = -heapq.heappop(heap)

# K largest: maintain min-heap of size k
```

**Key problems:** Top K Frequent Elements, Merge K Sorted Lists, Find Median from Data Stream, Task Scheduler

---

## 10. Union-Find (Disjoint Set)

**When to use:** Connected components, cycle detection in undirected graphs, grouping.

```
parent = list(range(n))
rank = [0] * n

def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])  # path compression
    return parent[x]

def union(x, y):
    px, py = find(x), find(y)
    if px == py:
        return False  # already connected
    if rank[px] < rank[py]:
        px, py = py, px
    parent[py] = px
    if rank[px] == rank[py]:
        rank[px] += 1
    return True
```

**Key problems:** Number of Islands (alt), Redundant Connection, Accounts Merge, Graph Valid Tree

---

## 11. Topological Sort (Kahn's Algorithm)

**When to use:** Dependencies, ordering, cycle detection in directed graphs.

```
from collections import deque

in_degree = {node: 0 for node in graph}
for node in graph:
    for neighbor in graph[node]:
        in_degree[neighbor] += 1

queue = deque([n for n in in_degree if in_degree[n] == 0])
order = []

while queue:
    node = queue.popleft()
    order.append(node)
    for neighbor in graph[node]:
        in_degree[neighbor] -= 1
        if in_degree[neighbor] == 0:
            queue.append(neighbor)

has_cycle = len(order) != len(graph)
```

**Key problems:** Course Schedule, Course Schedule II, Alien Dictionary

---

## 12. Trie (Prefix Tree)

**When to use:** Autocomplete, spell check, prefix matching, word search in matrix.

```
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end
```

**Key problems:** Implement Trie, Word Search II, Design Add and Search Words

---

## 13. Linked List Manipulation

**When to use:** Reverse, merge, detect cycles, find middle, reorder.

**Reverse a linked list (iterative):**
```
prev = None
curr = head
while curr:
    next_node = curr.next
    curr.next = prev
    prev = curr
    curr = next_node
return prev  # new head
```

**Detect cycle (Floyd's):**
```
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
    if slow == fast:
        return True  # cycle found
return False
```

**Find middle (slow/fast):**
```
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
return slow  # middle node
```

**Key problems:** Reverse Linked List, Merge Two Sorted Lists, Linked List Cycle, Remove Nth Node From End, Reorder List

---

## 14. Intervals

**When to use:** Overlapping intervals, scheduling, merge/insert intervals.

```
# Merge overlapping intervals
intervals.sort(key=lambda x: x[0])
merged = [intervals[0]]
for start, end in intervals[1:]:
    if start <= merged[-1][1]:
        merged[-1][1] = max(merged[-1][1], end)
    else:
        merged.append([start, end])
```

**Key problems:** Merge Intervals, Insert Interval, Non-overlapping Intervals, Meeting Rooms II

---

## Complexity Quick Reference

| Data Structure | Access | Search | Insert | Delete |
|---------------|--------|--------|--------|--------|
| Array | O(1) | O(n) | O(n) | O(n) |
| HashMap | — | O(1) avg | O(1) avg | O(1) avg |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) |
| Heap | — | O(n) | O(log n) | O(log n) |
| Stack/Queue | — | O(n) | O(1) | O(1) |

| Algorithm | Time | Space |
|-----------|------|-------|
| Binary Search | O(log n) | O(1) |
| BFS/DFS | O(V + E) | O(V) |
| Sorting (comparison) | O(n log n) | O(n) |
| Topological Sort | O(V + E) | O(V) |
| Union-Find (optimized) | O(α(n)) ≈ O(1) | O(n) |
