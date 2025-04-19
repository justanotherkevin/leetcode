# Cyclic Sort Pattern Explained

Let's unpack the **Cyclic Sort** pattern! This is a fascinating and efficient in-place sorting technique that works particularly well when you have an array containing numbers in a specific, limited range (usually 1 to N or 0 to N-1) and there are no duplicates (or you need to find duplicates/missing numbers based on this assumption).

Imagine you have a set of numbered boxes (1 to N) and a jumbled array of items, each labeled with a number from 1 to N. The goal is to put item `i` into box `i`. Cyclic Sort does this by picking an item, checking if it's in the correct box, and if not, swapping it with the item currently in its correct box. You repeat this swapping process for the *new* item you just received until the item you are holding belongs in the current position.

## Problem Breakdown: Cyclic Sort (Conceptual Problem)

While not a specific LeetCode problem *named* "Cyclic Sort", the pattern itself is the core of many problems. Let's define a conceptual problem:

**The Problem:** You are given an unsorted array `nums` containing `n` distinct numbers taken from the range `1` to `n`. Without using any extra space (modify the array in-place) and in O(n) time, sort the array.

**Example:**

*   **Input:** `nums = [3, 1, 5, 4, 2]`
*   **Output:** `[1, 2, 3, 4, 5]`
*   **Explanation:** The array contains numbers from 1 to 5. After sorting, `nums[0]` should be 1, `nums[1]` should be 2, and so on.

## Key Insight

*   **Core Concept:** The key idea is that if the array were sorted, the number `x` should be at index `x-1` (assuming the range is 1 to N). Cyclic Sort iterates through the array. For each element `nums[i]`, it checks if it's in its correct position (`nums[i] == i + 1`). If not, it finds the correct position for `nums[i]` (which is `nums[i] - 1`) and swaps `nums[i]` with the element currently at that correct position (`nums[nums[i] - 1]`). This swap might put the *correct* number at index `i`, or it might bring *another* misplaced number to index `i`. Crucially, you **don't advance `i`** until the number currently at `nums[i]` is the correct one (`i + 1`).
*   **Why it's tricky:** The main trick is realizing that you should keep swapping the element at the *current index* `i` until `nums[i]` holds the value `i+1`, before moving `i` to `i+1`. If you just swap once and increment `i`, you might leave a misplaced element behind.
*   **Solution Idea (Words):**
    1.  Initialize a pointer `i = 0`.
    2.  While `i` is less than the length of the array `n`:
        a.  Find the correct index `j` where the current element `nums[i]` should ideally go. If the numbers are in the range 1 to `n`, the correct index is `j = nums[i] - 1`.
        b.  Check if the element `nums[i]` is already in its correct position. This happens if `nums[i] == nums[j]` (or equivalently, `nums[i] == i + 1`).
        c.  If `nums[i]` is *not* in its correct place (`nums[i] != nums[j]`):
            i. Swap `nums[i]` with `nums[j]`.
            ii. **Do not increment `i`**. Stay at the current index `i` and repeat the check with the *new* value that just landed in `nums[i]`.
        d.  If `nums[i]` *is* in its correct place (`nums[i] == nums[j]`):
            i. Increment `i` to move to the next position.
    3.  Once the loop finishes, the array will be sorted.

## Brute Force Approach (Standard Sorting)

The simplest way to solve the conceptual problem is to use a standard sorting algorithm.

```python
def sort_using_standard_sort(nums: list[int]) -> None:
    # This uses extra space depending on the sort implementation (e.g., Timsort O(n) or O(log n))
    # or might not be O(n) time (e.g., comparison sorts are O(n log n))
    nums.sort()

# Example
arr = [3, 1, 5, 4, 2]
sort_using_standard_sort(arr)
print(arr) # Output: [1, 2, 3, 4, 5]
```

*   **Time Complexity:** Typically O(n log n) for comparison-based sorts.
*   **Space Complexity:** O(n) or O(log n) depending on the algorithm (Python's Timsort uses space).
*   **Why it's not optimal (for this pattern):** It doesn't meet the strict O(n) time and O(1) space constraints often associated with Cyclic Sort problems. It also doesn't leverage the specific property of the input numbers (range 1 to n).

## 🔥 Optimized Approach (Cyclic Sort)

This implements the in-place swapping logic described.

```python
def cyclic_sort(nums: list[int]) -> None:
    """
    Sorts an array containing n distinct numbers from the range 1 to n
    in-place using Cyclic Sort.

    Args:
        nums: A list of distinct integers from 1 to n.
    """
    i = 0
    n = len(nums)
    while i < n:
        # The correct index for the value nums[i] should be nums[i] - 1
        correct_index = nums[i] - 1

        # If the number is not at its correct index, swap it
        if nums[i] != nums[correct_index]:
            # Swap nums[i] with the element at its correct index
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            # If the number is already in its correct place, move to the next index
            i += 1

# Example Usage:
arr1 = [3, 1, 5, 4, 2]
print(f"Input: {arr1}")
cyclic_sort(arr1)
print(f"Output: {arr1}") # Output: [1, 2, 3, 4, 5]

arr2 = [2, 6, 4, 3, 1, 5]
print(f"Input: {arr2}")
cyclic_sort(arr2)
print(f"Output: {arr2}") # Output: [1, 2, 3, 4, 5, 6]

arr3 = [1, 5, 6, 4, 3, 2]
print(f"Input: {arr3}")
cyclic_sort(arr3)
print(f"Output: {arr3}") # Output: [1, 2, 3, 4, 5, 6]
```

*   **Walkthrough (Example: `nums = [3, 1, 5, 4, 2]`):**
    1.  `i = 0`, `nums[0] = 3`. Correct index for 3 is `3 - 1 = 2`. `nums[0] != nums[2]` (3 != 5). Swap `nums[0]` and `nums[2]`. Array becomes `[5, 1, 3, 4, 2]`. `i` stays 0.
    2.  `i = 0`, `nums[0] = 5`. Correct index for 5 is `5 - 1 = 4`. `nums[0] != nums[4]` (5 != 2). Swap `nums[0]` and `nums[4]`. Array becomes `[2, 1, 3, 4, 5]`. `i` stays 0.
    3.  `i = 0`, `nums[0] = 2`. Correct index for 2 is `2 - 1 = 1`. `nums[0] != nums[1]` (2 != 1). Swap `nums[0]` and `nums[1]`. Array becomes `[1, 2, 3, 4, 5]`. `i` stays 0.
    4.  `i = 0`, `nums[0] = 1`. Correct index for 1 is `1 - 1 = 0`. `nums[0] == nums[0]` (1 == 1). Element is in place. Increment `i` to 1.
    5.  `i = 1`, `nums[1] = 2`. Correct index for 2 is `2 - 1 = 1`. `nums[1] == nums[1]` (2 == 2). Element is in place. Increment `i` to 2.
    6.  `i = 2`, `nums[2] = 3`. Correct index for 3 is `3 - 1 = 2`. `nums[2] == nums[2]` (3 == 3). Element is in place. Increment `i` to 3.
    7.  `i = 3`, `nums[3] = 4`. Correct index for 4 is `4 - 1 = 3`. `nums[3] == nums[3]` (4 == 4). Element is in place. Increment `i` to 4.
    8.  `i = 4`, `nums[4] = 5`. Correct index for 5 is `5 - 1 = 4`. `nums[4] == nums[4]` (5 == 5). Element is in place. Increment `i` to 5.
    9.  `i = 5`. Loop condition `i < n` (5 < 5) is false. Exit.
    10. Final array: `[1, 2, 3, 4, 5]`.
*   **Time Complexity:** O(n). Although there's a `while` loop and swaps happen inside, each number is swapped at most once to reach its final correct position. In the worst case, every element needs one swap, plus the initial check. So, the total number of swaps is bounded by `n`, and the total number of comparisons is also roughly proportional to `n`. Thus, the complexity is linear.
*   **Space Complexity:** O(1). Sorting is done in-place without any auxiliary data structures proportional to the input size.

## How This Pattern is Reused in Harder Problems

Cyclic Sort is rarely used just for sorting itself (standard sorts are often easier to use if allowed). Its real power comes in **finding errors or discrepancies** in an array that *should* contain numbers from 1 to n (or 0 to n-1).

After attempting a Cyclic Sort:
*   The **first number** that is *not* in its correct place (`nums[i] != i + 1`) often reveals the **missing number**. The missing number *should* have been `i + 1`.
*   If you find a number `nums[i]` at index `i`, but the correct number `i + 1` is *also* somewhere else, then `nums[i]` might be the **duplicate number**.

*   **Connections & Patterns:**
    *   **Finding the Missing Number:** Given an array of `n` numbers from `0` to `n` with one missing. Use Cyclic Sort (adjusting indices for 0-based range). After sorting, iterate; the first index `i` where `nums[i] != i` indicates `i` is the missing number.
    *   **Finding the Duplicate Number:** Given an array of `n+1` numbers from `1` to `n` with one duplicate. Use Cyclic Sort. During the swap phase, if you try to swap `nums[i]` with `nums[correct_index]` but find `nums[i] == nums[correct_index]` *before* `nums[i]` is in its correct place (`i+1`), then `nums[i]` is the duplicate.
    *   **Finding All Missing Numbers:** Similar to finding one, but collect all indices `i` where `nums[i] != i + 1` after the sort phase.
    *   **Finding All Duplicate Numbers:** Can be adapted, often checking `nums[i] != i + 1` after the sort attempt; if `nums[i]` is not `i + 1`, it's a misplaced number, potentially a duplicate.
    *   **Find the Corrupt Pair (Missing and Duplicate):** Combine the logic for finding the missing and duplicate numbers.

*   **Related Problems:**
    *   [LeetCode 268. Missing Number](https://leetcode.com/problems/missing-number/)
    *   [LeetCode 287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/) (Note: Cyclic Sort can solve this but might modify the array; other solutions like Floyd's cycle detection are common if modification is disallowed).
    *   [LeetCode 448. Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)
    *   [LeetCode 442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/)
    *   [LeetCode 645. Set Mismatch](https://leetcode.com/problems/set-mismatch/) (Find the duplicate and the missing number)
    *   [LeetCode 41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/) (Harder variation, requires handling negatives/zeros and the range 1 to n within potentially larger array)

*   **Key Building Blocks:** The core is the in-place swapping strategy: iterate, find the correct position for `nums[i]`, and swap `nums[i]` with `nums[correct_index]` if `nums[i]` is not already there. Keep processing index `i` until the correct value is placed.

*   **Real-World Applications:** While direct applications are less common than standard sorting, the underlying principle relates to hash maps and placing items in their correct "buckets" or positions in-place, which can be relevant in memory-constrained environments or specialized indexing tasks.

Cyclic Sort is a clever pattern primarily used for its O(n) time and O(1) space characteristics in problems involving finding missing or duplicate numbers within a constrained range. 

## Quick Understanding Check

### Level 1 (Remember/Recall)
- Q: What type of arrays is the Cyclic Sort pattern particularly effective for?
  - A: Arrays containing numbers in a limited range (usually 1 to N or 0 to N-1) with no duplicates. [*Source: Introduction paragraph*]
- Q: How do you determine the correct position for an element in Cyclic Sort?
  - A: For a value 'x' in range 1 to N, its correct position is at index 'x-1'. [*Source: Key Insight section*]
- Q: What is the time complexity of Cyclic Sort?
  - A: O(n), as each number is swapped at most once to reach its final position. [*Source: Time Complexity analysis*]

### Level 2 (Understand/Explain)
- Q: Why does Cyclic Sort not increment the pointer 'i' after swapping elements?
  - A: After swapping, the new element that arrived at position 'i' might still be in the wrong place. By keeping the pointer at 'i', we ensure that the current position contains the correct value before moving on. This guarantees that every element will end up in its proper position with minimal swaps.
- Q: How is Cyclic Sort different from standard sorting algorithms, and what advantage does it provide?
  - A: Unlike comparison-based sorts (O(n log n)), Cyclic Sort achieves O(n) time complexity by exploiting the property that the numbers are in a known range. It directly maps each number to its final position without needing comparisons between elements. This makes it significantly faster for the specific cases where it applies.
- Q: Explain how you would use Cyclic Sort to find both a missing number and a duplicate number in an array.
  - A: After attempting a Cyclic Sort, any number not in its correct position reveals information: if nums[i] != i+1, then i+1 is the missing number, and the value at nums[i] that couldn't be placed correctly is the duplicate. By tracking these discrepancies during the sorting process, you can identify both the missing and duplicate numbers in a single pass. 