# LeetCode Quick Review

## Bit Manipulation

### [338. Counting Bits](https://leetcode.com/problems/counting-bits/)

Count the `1` bits for every number from `0` to `n`.

**Pattern:** Bit Manipulation + DP

**Hint / Intuition:** Remove the last bit, reuse its count, then add the last bit.

`dp[i] = dp[i >> 1] + (i & 1)`

**Complexity:** O(n) time, O(n) space.

---

### [268. Missing Number](https://leetcode.com/problems/missing-number/)

Find the missing number from `0...n` given `n` distinct numbers.

**Pattern:** Math / Bit Manipulation (XOR)

**Hint / Intuition:**

- **Math:** Expected sum minus actual sum. `n * (n + 1) // 2 - sum(nums)`
- **XOR:** Identical numbers cancel (`a ^ a = 0`). XOR all numbers from `0...n` with `nums`; only the missing number remains.

**Remember:** Expected sum → subtraction. Matching pairs → XOR cancellation.

**Complexity:** Both O(n) time, O(1) extra space.

---

## Trees / DFS

### [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

Find the lowest node that is an ancestor of both `p` and `q`.

**Pattern:** Recursive DFS / Postorder

**Hint / Intuition:** If left and right both find something, current node is the LCA. Otherwise propagate the non-null side.

**Complexity:** O(n) time, O(h) space.

---

## Linked Lists

### [143. Reorder List](https://leetcode.com/problems/reorder-list/)

Reorder `1 → 2 → 3 → 4 → 5` into `1 → 5 → 2 → 4 → 3`.

**Pattern:** Two Pointers / Reverse Linked List / Merge

**Hint / Intuition:** Need to alternate nodes from the front and back.

- **Easy:** Store node references in a deque, alternate `popleft()` and `pop()`. O(n) space.
- **Optimal:** Find middle (slow/fast) → reverse second half → merge alternately. O(1) extra space.

**Remember (reversal):** Save next → Flip arrow → Advance pointers.

**Complexity:** O(n) time, O(1) extra space (optimal).

---

### [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

Merge `k` sorted linked lists into one sorted linked list.

**Pattern:** Min Heap / Divide & Conquer

**Hint / Intuition:** Two optimal approaches:

**1. Min Heap**
- Keep one candidate (head) from each list in a min heap.
- Pop the smallest, append it to the result, push its successor.
- Use `(node.val, list_index, node)` to handle equal values.

**Complexity:** O(N log k) time, O(k) space.

**2. Divide & Conquer**
- Merge sorted lists in pairs, like merge sort.
- Repeat until only one merged list remains.
- Each round processes N nodes; there are O(log k) rounds.

**Complexity:** O(N log k) time, O(1) extra space (iterative, in-place).

**Remember:** Heap = repeatedly pick the smallest. Divide & Conquer = merge pairs in rounds.

`N` = total nodes, `k` = number of lists.

---

## Greedy / Intervals

### [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

Remove the fewest intervals so the rest do not overlap.

**Pattern:** Greedy + Sorting

**Hint / Intuition:** When intervals overlap, keep the one that ends earlier to leave more room for future intervals.

**Complexity:** O(n log n) time, O(1) extra space (excluding sort).

---

## Greedy

### [55. Jump Game](https://leetcode.com/problems/jump-game/)

Determine whether you can reach the last index.

**Pattern:** Greedy / Reachability

**Hint / Intuition:** Track the farthest reachable index. If the current index is beyond it, you are stuck.

`farthest = max(farthest, i + nums[i])`

**Complexity:** O(n) time, O(1) space.

---

## Arrays / Dynamic Programming

### [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

Find the contiguous subarray with the largest product.

**Pattern:** DP / Kadane-like

**Hint / Intuition:** Track both the max and min product ending here. A negative number can turn the smallest negative into the largest positive.

`cur_max = max(n, prev_max * n, prev_min * n)`

`cur_min = min(n, prev_max * n, prev_min * n)`

**Complexity:** O(n) time, O(1) space.

---

# Problem-Solving Concepts

### [C1. DP or Greedy?](https://en.wikipedia.org/wiki/Greedy_algorithm)

Decide whether a problem needs multiple saved states or can be reduced to one best summary.

**Pattern:** DP vs Greedy

**Hint / Intuition:** First find a valid DP state. Then ask:

**“Do I really need all these states, or does one state dominate the others?”**

DP usually means:
- Multiple possibilities must be preserved.
- The future depends on which previous state you came from.
- `dp[i]` combines several meaningful previous states.

Greedy usually means:
- One best value / boundary dominates the others.
- You do not care about the exact path.
- The future only needs a summary like `max`, `min`, `farthest`, or `earliest end`.

**Remember:** Brute force → memoization / DP → ask if the DP state can collapse into one value, and whether discarding the other choices is always safe.

**Example:** Jump Game DP stores which indices can reach the end. Greedy realizes only the reachable boundary matters.

---

# Array Concepts

## Quick Cheat Sheet

| Concept | Contiguous? | Order matters? | Remember |
|---|---|---|---|
| Subarray | Yes | Yes | Slice of the array |
| Subsequence | No | Yes | Skip elements, keep order |
| Subset | No | No | Take / skip elements |
| Permutation | N/A | Yes | Rearrangement |
| Combination | N/A | No | Selection |

---

### [A1. Subarray](https://www.geeksforgeeks.org/dsa/array-subarray-subsequence-and-subset/)

Given an array, choose a contiguous portion of it.

**Pattern:** Array Range

**Hint / Intuition:** No skipping. If you choose indices `l...r`, you take everything between them.

`[1, 2, 3, 4] → [2, 3]` ✓  
`[1, 2, 3, 4] → [1, 3]` ✗

**Remember:** `n(n + 1) / 2` non-empty subarrays.

**Related:** [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/), [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

---

### [A2. Subsequence](https://www.geeksforgeeks.org/dsa/array-subarray-subsequence-and-subset/)

Choose elements while preserving their original relative order.

**Pattern:** Take / Skip

**Hint / Intuition:** You may skip elements, but you cannot reorder them.

`[1, 2, 3, 4] → [1, 3, 4]` ✓  
`[1, 2, 3, 4] → [3, 1]` ✗

**Remember:** Each element is a yes/no choice → up to `2^n`.

**Related:** [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/), [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)

---

### [A3. Subset](https://www.geeksforgeeks.org/dsa/array-subarray-subsequence-and-subset/)

Choose any collection of elements; order does not matter.

**Pattern:** Take / Skip

**Hint / Intuition:** Ask only “is this element included?”

`[1, 2, 3] → {1, 3}`

`{1, 3}` and `{3, 1}` are the same subset.

**Remember:** `n` independent yes/no choices → `2^n` subsets.

**Related:** [78. Subsets](https://leetcode.com/problems/subsets/), [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

---

### [A4. Permutation](https://en.wikipedia.org/wiki/Permutation)

Arrange elements in every possible order.

**Pattern:** Backtracking

**Hint / Intuition:** At each position, choose any unused element.

`[1, 2, 3] → [1,2,3], [1,3,2], [2,1,3], ...`

**Remember:** Order matters. `n` distinct elements → `n!` permutations.

**Related:** [46. Permutations](https://leetcode.com/problems/permutations/)

---

### [A5. Combination](https://en.wikipedia.org/wiki/Combination)

Choose elements where the order of the chosen elements does not matter.

**Pattern:** Backtracking / Choose

**Hint / Intuition:** `{1, 2}` and `{2, 1}` represent the same choice.

**Remember:** Combination = choose. Permutation = arrange.

**Related:** [39. Combination Sum](https://leetcode.com/problems/combination-sum/), [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

---

### [A6. Prefix / Suffix](https://leetcode.com/problems/product-of-array-except-self/)

A prefix starts at the beginning; a suffix ends at the end.

**Pattern:** Precomputation / Running Value

`[1, 2, 3, 4]`

Prefix: `[1, 2, 3]`  
Suffix: `[3, 4]`

**Hint / Intuition:** Store information accumulated from the left and/or right so you do not recompute ranges.

**Related:** [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

---

### [A7. Sliding Window](https://blog.neetcode.io/p/prepare-coding-interviews)

Maintain a changing contiguous range while scanning an array or string.

**Pattern:** Two Pointers / Subarray

**Hint / Intuition:** Expand the right side; when the window becomes invalid, move the left side forward.

**Remember:** A sliding window is usually a smarter way to examine many related subarrays without restarting from scratch.

**Related:** [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/), [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
