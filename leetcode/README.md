# LeetCode Quick Review

## Bit Manipulation

### [338. Counting Bits](https://leetcode.com/problems/counting-bits/)

**Problem:** Count the 1-bits of every integer from 0 through n.

**Naive solution:** For each integer, repeatedly shift and count its 1-bits — O(n log n) time, O(n) output space.

**Hint / Intuition:** Every number is a smaller number plus one bit.

**Solution 1:** Remove the highest 1-bit: `dp[i] = dp[i ^ (1 << (i.bit_length()-1))] + 1` — O(n) time, O(n) space.

**Solution 2:** Remove the lowest bit: `dp[i] = dp[i >> 1] + (i & 1)` — O(n) time, O(n) space.

**Pattern / Remember:** Bit Manipulation / DP. Reuse a previously computed smaller number; initialize `dp[0] = 0`.

---

### [268. Missing Number](https://leetcode.com/problems/missing-number/)

**Problem:** Find the missing integer from 0 through n.

**Naive solution:** Check each candidate by scanning the array — O(n²) time, O(1) extra space.

**Hint / Intuition:** The expected numbers are known, so cancel what is present.

**Solution 1:** Sum difference: `n*(n+1)//2 - sum(nums)` — O(n) time, O(1) space.

**Solution 2:** XOR all indices, values, and n; matching numbers cancel — O(n) time, O(1) space.

**Pattern / Remember:** Math / XOR. Expected sum → subtract; pairs → `a ^ a = 0`.

---

## Tries / Backtracking

### [212. Word Search II](https://leetcode.com/problems/word-search-ii/)

**Problem:** Find dictionary words along adjacent board cells, without reusing a cell in a path.

**Naive solution:** Search every word separately by enumerating board paths — up to O(W·M·N·L·3^L) time, O(L) DFS space.

**Hint / Intuition:** Stop as soon as a prefix cannot match; multiple words share prefixes.

**Solution 1:** DFS separately for each word, pruning on mismatched letters — O(W·M·N·3^L) worst-case time.

**Solution 2:** Build a Trie; DFS the board while following Trie edges; remove found words/dead branches — O(S + M·N·3^L) worst-case time, O(S+L) space.

**Pattern / Remember:** Trie / Backtracking. Prefix pruning reduces actual work, not the exponential worst case. Memoization needs visited cells too, so cache hits may be rare. W=words; M×N=board; L=max word length (≤MN); S=total dictionary characters.

---

## Trees / DFS

### [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

**Problem:** Find the lowest node that is an ancestor of both p and q.

**Naive solution:** Find both root-to-target paths, then compare their common prefix — O(n) time, O(h) extra space.

**Hint / Intuition:** If targets appear in different child subtrees, the current node is their LCA.

**Solution 1:** Build parent pointers, then walk ancestors to find the first shared one — O(n) time, O(n) space.

**Solution 2:** Postorder DFS: return p/q when found; if both children return nodes, return current; otherwise return non-null child — O(n) time, O(h) stack.

**Pattern / Remember:** Recursive DFS / Trees. A recursive call returns a found target, LCA, or None.

---

### [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

**Problem:** Rebuild a binary tree from preorder and inorder arrays of distinct values.

**Naive solution:** Take the preorder root; search inorder and recursively slice both arrays — O(n²) worst-case time and auxiliary space.

**Hint / Intuition:** Preorder chooses the root; inorder reveals the size of its left and right subtrees.

**Solution 1:** Use an inorder-index hashmap, a preorder cursor, and recursive inorder bounds — O(n) time, O(n) space.

**Solution 2:** Iterative stack with an inorder cursor: attach left children until an inorder match, then backtrack to attach right — O(n) time, O(n) space.

**Pattern / Remember:** Divide & Conquer / DFS. Preorder = Root–Left–Right; Inorder = Left–Root–Right.

---

## Linked Lists

### [143. Reorder List](https://leetcode.com/problems/reorder-list/)

**Problem:** Change 1→2→3→4→5 into 1→5→2→4→3, rewiring the original nodes.

**Naive solution:** Collect node references in an array, then alternate its front/back indices — O(n) time, O(n) space.

**Hint / Intuition:** Accessing both ends is hard in a singly linked list; reorganize how you access the second half.

**Solution 1:** Store node references in a deque; alternate `popleft()` and `pop()` — O(n) time, O(n) space.

**Solution 2:** Find middle (slow/fast), reverse second half, weave halves together — O(n) time, O(1) extra space.

**Pattern / Remember:** Two Pointers / Linked List Reversal. Reverse = save next → flip pointer → advance; terminate final next with None.

---

### [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

**Problem:** Merge k sorted linked lists into one sorted list.

**Naive solution:** Collect all N nodes and sort by value — O(N log N) time, O(N) extra space.

**Hint / Intuition:** Only the smallest remaining node from each list can be the next output.

**Solution 1:** Min heap of up to k heads: pop min, append, push its successor — O(N log k) time, O(k) space.

**Solution 2:** Merge lists pairwise in rounds (iterative divide-and-conquer) — O(N log k) time, O(1) auxiliary space.

**Pattern / Remember:** Heap / Divide & Conquer. For Python heap ties use `(value, list_index, node)`; N=total nodes, k=lists.

---

## Greedy

### [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

**Problem:** Remove the fewest intervals so the remaining ones do not overlap.

**Naive solution:** Sort, then try all subsets and check overlaps — O(n·2^n) time (plus sorting), O(n) space.

**Hint / Intuition:** When two overlap, keeping the one that ends first leaves more space for the future.

**Solution 1:** Sort by start; on overlap discard the one with the later end — O(n log n) time, O(1) scan space.

**Solution 2:** Sort by end; greedily keep each interval starting at/after the last kept end — O(n log n) time, O(1) scan space.

**Pattern / Remember:** Greedy / Intervals. Earliest finish dominates later finishes; sorting may use extra space.

---

### [55. Jump Game](https://leetcode.com/problems/jump-game/)

**Problem:** Decide whether you can reach the last index.

**Naive solution:** DFS every possible jump — O(2^n) worst-case time, O(n) stack.

**Hint / Intuition:** Exact jump choices do not matter if a single reachable boundary summarizes them.

**Solution 1:** Memoize whether each index can reach the end — O(n²) time, O(n) space.

**Solution 2:** Greedy scan: if `i > farthest`, fail; otherwise `farthest = max(farthest, i + nums[i])` — O(n) time, O(1) space.

**Pattern / Remember:** Greedy / Reachability. Multiple DP states collapse to one farthest reachable index.

---

## Arrays / DP

### [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

**Problem:** Find the contiguous subarray with maximum product.

**Naive solution:** Start at each index and extend the running product — O(n²) time, O(1) extra space.

**Hint / Intuition:** Negative values can turn the smallest product into the largest.

**Solution 1:** DP arrays store the max/min product ending at each index — O(n) time, O(n) space.

**Solution 2:** Keep rolling max/min: each = max/min of `x`, `prev_max*x`, `prev_min*x` — O(n) time, O(1) space.

**Pattern / Remember:** DP / Kadane-like. Compute both updates from PREVIOUS max/min; zeros restart naturally.

---

## Strings / DP

### [139. Word Break](https://leetcode.com/problems/word-break/)

**Problem:** Decide whether a string can be segmented into reusable dictionary words.

**Naive solution:** Try every matching word and recurse on the remaining suffix — exponential, up to O(W·L·2^n) time; O(n) stack.

**Hint / Intuition:** Different splits can reach the same suffix; cache the starting index, not the word choices.

**Solution 1:** Top-down DFS + memoization by index — O(n·W·L) time, O(n) cache/stack space.

**Solution 2:** Bottom-up DP: `dp[i]` is true if some matching word starts at i and leads to a true suffix — O(n·W·L) time, O(n) space.

**Pattern / Remember:** DP / String Segmentation. W=dictionary size, L=max word length; slicing can add cost; words can be reused.

---

## Strings / Palindromes

### [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

**Problem:** Count all palindromic contiguous substrings.

**Naive solution:** Enumerate all substrings and check each by two pointers — O(n³) time, O(1) extra space.

**Hint / Intuition:** Every palindrome has a center; or matching ends enclosing a palindromic interior.

**Solution 1:** DP: `pal[l][r] = s[l]==s[r] and (r-l<2 or pal[l+1][r-1])` — O(n²) time, O(n²) space.

**Solution 2:** Expand around each character and each gap — O(n²) time, O(1) extra space (best simple interview choice).

**Pattern / Remember:** Two Pointers / DP. There are `2n−1` centers. Advanced Manacher algorithm achieves O(n) time.

---

# Problem-Solving Concepts

### [C1. DP or Greedy?](https://en.wikipedia.org/wiki/Greedy_algorithm)

**Problem:** Decide whether to keep multiple possible states or make a safe local choice.

**Naive solution:** Explore every decision path — often exponential, O(b^d) for branching b and depth d.

**Hint / Intuition:** Can one state dominate the others without ever sacrificing an optimal outcome?

**Solution 1:** DP: store meaningful states and transitions — O(states × transitions) time, O(states) space.

**Solution 2:** Greedy: retain a provably sufficient best value/boundary — e.g., Jump Game O(n) time, O(1) space.

**Pattern / Remember:** Prove greedy dominance; state compression alone does not guarantee a greedy solution. Example: Jump Game farthest reach.

---

### [C2. Recognizing Exponential Recursion](https://en.wikipedia.org/wiki/Recurrence_relation)

**Problem:** Estimate the runtime of a branching recursive search.

**Naive solution:** Draw its recursion tree: b calls per node, depth d gives O(b^d) calls as an upper bound (O(1) work/call).

**Hint / Intuition:** Ask: how many branches, how much input shrinks per call, and which states repeat?

**Solution 1:** Write a recurrence: `T(n)=2T(n-1)+O(1)` is O(2^n); `T(n)=2T(n/2)+O(1)` is O(n).

**Solution 2:** With memoization, count distinct states × transitions per state instead of all recursion paths.

**Pattern / Remember:** Recursion Trees / Complexity. Multiple branches alone ≠ exponential; small reductions + repeated subproblems are warning signs.

---

# Array Concepts

## Quick Cheat Sheet

| Concept | Contiguous? | Order matters? | Typical time complexity |
|---|---|---|---|
| Subarray | Yes | Yes | O(n²) to enumerate index ranges |
| Subsequence | No | Yes | O(n·2^n) to generate/copy all |
| Subset | No | No | O(n·2^n) to generate/copy all |
| Permutation | N/A | Yes | O(n·n!) to generate all |
| Combination | N/A | No | O(k·C(n,k)) to generate size-k sets |
| Prefix / Suffix | N/A | Yes | O(n) to precompute aggregates |
| Sliding Window | Yes | Yes | O(n) for a valid one-pass window |

*Complexities describe the stated enumeration or typical pattern, not all problems involving that concept. Output materialization can require additional space.*

---

### [A1. Subarray](https://www.geeksforgeeks.org/dsa/array-subarray-subsequence-and-subset/)

**Problem:** Enumerate every nonempty contiguous part of an array.

**Naive solution:** Materialize every slice with nested loops — O(n³) total copied elements/time.

**Hint / Intuition:** Fix a start; every end from that start onward is valid.

**Solution 1:** Enumerate `(start, end)` index pairs — O(n²) time, O(1) extra space if streamed.

**Solution 2:** Choose any two of the n+1 boundaries — O(n²) index pairs, O(1) extra space if streamed.

**Pattern / Remember:** Array Ranges. `n(n+1)/2` nonempty subarrays; actual copied contents total O(n³). Related: [53](https://leetcode.com/problems/maximum-subarray/), [152](https://leetcode.com/problems/maximum-product-subarray/).

---

### [A2. Subsequence](https://www.geeksforgeeks.org/dsa/array-subarray-subsequence-and-subset/)

**Problem:** Generate selections that preserve original element order but allow skipping.

**Naive solution:** Enumerate all 2^n inclusion masks and collect selected elements — O(n·2^n) time.

**Hint / Intuition:** At each index choose take or skip.

**Solution 1:** DFS include/exclude with backtracking — O(n·2^n) output time, O(n) stack (excluding results).

**Solution 2:** Bitmask from 0 to `2^n−1` — O(n·2^n) output time.

**Pattern / Remember:** Backtracking / Order Preserved. There are 2^n position-based subsequences including empty. Related: [300](https://leetcode.com/problems/longest-increasing-subsequence/), [1143](https://leetcode.com/problems/longest-common-subsequence/).

---

### [A3. Subset](https://www.geeksforgeeks.org/dsa/array-subarray-subsequence-and-subset/)

**Problem:** Generate every set of selected elements; ordering does not matter.

**Naive solution:** Copy an include/exclude decision list at each leaf — O(n·2^n) time.

**Hint / Intuition:** Each element has a yes/no membership decision.

**Solution 1:** Backtrack over include/exclude branches — O(n·2^n) output time, O(n) stack (excluding results).

**Solution 2:** Use a bitmask for membership — O(n·2^n) output time.

**Pattern / Remember:** Backtracking / Set Selection. 2^n subsets of n distinct elements. Related: [78](https://leetcode.com/problems/subsets/), [416](https://leetcode.com/problems/partition-equal-subset-sum/).

---

### [A4. Permutation](https://en.wikipedia.org/wiki/Permutation)

**Problem:** Generate all arrangements of n distinct elements.

**Naive solution:** Try all n possibilities at every position, then reject repeats — up to O(n^(n+1)) time.

**Hint / Intuition:** At each position choose one UNUSED element.

**Solution 1:** Backtrack with a used set/array — O(n·n!) time, O(n) auxiliary space (excluding output).

**Solution 2:** Swap each candidate into the current position and restore afterward — O(n·n!) time, O(n) stack.

**Pattern / Remember:** Backtracking / Order Matters. There are n! permutations. Related: [46](https://leetcode.com/problems/permutations/).

---

### [A5. Combination](https://en.wikipedia.org/wiki/Combination)

**Problem:** Select k of n distinct elements; selection order does not matter.

**Naive solution:** Generate all 2^n subsets and filter those of size k — O(n·2^n) time.

**Hint / Intuition:** Choose the next element only from positions after the previous choice.

**Solution 1:** Backtrack through increasing indices and prune impossible sizes — output-sensitive O(k·C(n,k)) typical time.

**Solution 2:** Generate successive k-element index combinations in lexicographic order — O(k·C(n,k)) time, O(k) working space.

**Pattern / Remember:** Combinatorics / Backtracking. `C(n,k) = n!/(k!(n-k)!)`. Related: [39](https://leetcode.com/problems/combination-sum/), [40](https://leetcode.com/problems/combination-sum-ii/).

---

### [A6. Prefix / Suffix](https://www.geeksforgeeks.org/dsa/precomputation-technique-on-arrays/)

**Problem:** Compute aggregates from the start (prefix) or end (suffix) of an array.

**Naive solution:** Recompute each requested prefix/suffix by scanning — O(n²) total time for all positions.

**Hint / Intuition:** Reuse the previously accumulated result instead of rescanning.

**Solution 1:** Build running prefix and suffix arrays in two passes — O(n) time, O(n) extra space.

**Solution 2:** For [238. Product Except Self](https://leetcode.com/problems/product-of-array-except-self/), store prefixes in output, then multiply by a running suffix — O(n) time, O(1) auxiliary space.

**Pattern / Remember:** Precomputation / Running Aggregate. Prefix starts at index 0; suffix ends at n−1.

---

### [A7. Sliding Window](https://blog.neetcode.io/p/prepare-coding-interviews)

**Problem:** Process contiguous ranges while moving their boundaries through the array.

**Naive solution:** Try every candidate range — at least O(n²) candidate windows.

**Hint / Intuition:** Can you update the window in O(1) as an endpoint moves?

**Solution 1:** Fixed-size window: add incoming and remove outgoing element — O(n) time, O(1) rolling state.

**Solution 2:** Variable-size window: expand right, shrink left while invalid (when the condition permits) — O(n) pointer moves, O(1)–O(n) tracking space.

**Pattern / Remember:** Two Pointers / Contiguous Range. O(n) works when each pointer moves forward only; not every window condition allows it. Related: [3](https://leetcode.com/problems/longest-substring-without-repeating-characters/), [424](https://leetcode.com/problems/longest-repeating-character-replacement/).

---

