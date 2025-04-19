# Two Pointers Technique Explained

Hey there! Let's dive into the **Two Pointers** technique. It's a really clever trick used in algorithms to solve problems involving ordered data structures like arrays and sometimes linked lists. Instead of iterating with one pointer, you use two! This often lets you process the data in a single pass, drastically improving efficiency.

Imagine you have a line of people sorted by height, and you need to find a pair with a specific combined height. Instead of checking every possible pair (which could take ages!), you could have one person start checking from the shortest end and another from the tallest end, moving inwards based on whether the current pair's height is too much or too little. That's the essence of Two Pointers!

## Problem Breakdown: Two Sum II - Input Array Is Sorted

Let's use a common LeetCode problem to illustrate: **[LeetCode 167. Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)**.

**The Problem:** Given a 1-indexed array of integers `numbers` that is already **sorted in non-decreasing order**, find two numbers such that they add up to a specific `target` number. Let these two numbers be `numbers[index1]` and `numbers[index2]` where `1 <= index1 < index2 <= numbers.length`. Return the indices of the two numbers, `index1` and `index2`, **added by one** as an integer array `[index1, index2]` of length 2. You may assume that each input would have **exactly one solution** and you **may not use the same element twice**. Your solution must use only constant extra space.

**Example:**

*   **Input:** `numbers = [2, 7, 11, 15]`, `target = 9`
*   **Output:** `[1, 2]`
*   **Explanation:** The sum of 2 and 7 is 9. Their indices are 1 and 2 (remember, it's 1-indexed).

## Key Insight

*   **Core Concept:** The magic ingredient here is that the **array is sorted**. This allows us to make intelligent decisions about how to move our pointers. If the current sum is too small, we know we need a larger number, so we move the pointer starting from the left (smaller numbers) forward. If the sum is too large, we need a smaller number, so we move the pointer starting from the right (larger numbers) backward.
*   **Why it's tricky (without Two Pointers):** A naive approach might involve checking every single pair of numbers, which becomes very slow for large arrays. The sorted property is the key hint to look for a more optimized approach like Two Pointers.
*   **Solution Idea (Words):** Start with one pointer (`left`) at the beginning of the array and another pointer (`right`) at the end. Calculate the sum of the elements at these two pointers.
    *   If the sum equals the `target`, we found our pair! Return their 1-based indices.
    *   If the sum is less than the `target`, we need a larger sum. Since the array is sorted, the only way to potentially increase the sum is to move the `left` pointer one step to the right.
    *   If the sum is greater than the `target`, we need a smaller sum. Move the `right` pointer one step to the left.
    *   Repeat until the pointers meet or cross (though the problem guarantees a solution, so they'll find it before crossing).

## Brute Force Approach (Nested Loops)

You *could* solve this by checking every possible pair using nested loops.

```python
# This is NOT the recommended way for this specific problem
def two_sum_brute_force(numbers: list[int], target: int) -> list[int]:
    n = len(numbers)
    for i in range(n):
        for j in range(i + 1, n): # Start j from i+1 to avoid using the same element
            if numbers[i] + numbers[j] == target:
                # Remember to return 1-based indices
                return [i + 1, j + 1]
    return [] # Should not happen based on problem description
```

*   **Time Complexity:** O(n²). For each element, we potentially iterate through the rest of the array.
*   **Space Complexity:** O(1). We are only using a few variables.
*   **Why it's not optimal:** The O(n²) time complexity can be very slow for large input arrays. We are not leveraging the fact that the array is sorted.

## 🔥 Optimized Approach (Two Pointers)

Here's how to implement the Two Pointers strategy:

```python
def two_sum_two_pointers(numbers: list[int], target: int) -> list[int]:
    """
    Finds two numbers in a sorted array that add up to the target using the Two Pointers technique.

    Args:
        numbers: A 1-indexed list of integers sorted in non-decreasing order.
        target: The target sum.

    Returns:
        A list containing the 1-based indices of the two numbers.
    """
    left = 0  # Pointer starting at the beginning (0-indexed)
    right = len(numbers) - 1 # Pointer starting at the end (0-indexed)

    while left < right:
        current_sum = numbers[left] + numbers[right]

        if current_sum == target:
            # Found the pair, return 1-based indices
            return [left + 1, right + 1]
        elif current_sum < target:
            # Sum is too small, need a larger number
            # Move the left pointer to the right
            left += 1
        else: # current_sum > target
            # Sum is too large, need a smaller number
            # Move the right pointer to the left
            right -= 1

    # The problem guarantees a solution exists, so this part should theoretically not be reached.
    # Included for completeness / handling potential edge cases if guarantee wasn't there.
    return []

# Example Usage:
numbers1 = [2, 7, 11, 15]
target1 = 9
print(f"Input: numbers = {numbers1}, target = {target1}")
print(f"Output: {two_sum_two_pointers(numbers1, target1)}") # Output: [1, 2]

numbers2 = [2, 3, 4]
target2 = 6
print(f"Input: numbers = {numbers2}, target = {target2}")
print(f"Output: {two_sum_two_pointers(numbers2, target2)}") # Output: [1, 3]

numbers3 = [-1, 0]
target3 = -1
print(f"Input: numbers = {numbers3}, target = {target3}")
print(f"Output: {two_sum_two_pointers(numbers3, target3)}") # Output: [1, 2]
```

*   **Walkthrough (Example: `numbers = [2, 7, 11, 15]`, `target = 9`):**
    1.  `left = 0` (points to 2), `right = 3` (points to 15). `sum = 2 + 15 = 17`.
    2.  `17 > 9` (sum is too large). Decrement `right`. Now `right = 2` (points to 11).
    3.  `left = 0` (points to 2), `right = 2` (points to 11). `sum = 2 + 11 = 13`.
    4.  `13 > 9` (sum is too large). Decrement `right`. Now `right = 1` (points to 7).
    5.  `left = 0` (points to 2), `right = 1` (points to 7). `sum = 2 + 7 = 9`.
    6.  `9 == 9` (sum equals target!). Return `[left + 1, right + 1]`, which is `[0 + 1, 1 + 1] = [1, 2]`.
*   **Time Complexity:** O(n). In the worst case, each pointer traverses the array approximately once. The `left` pointer moves `n` steps right, and the `right` pointer moves `n` steps left.
*   **Space Complexity:** O(1). We only use a fixed number of variables (`left`, `right`, `current_sum`), regardless of the input size, fulfilling the problem's constraint.

## How This Technique is Reused in Harder Problems

The Two Pointers pattern is incredibly versatile!

*   **Connections & Patterns:**
    *   **Finding Triplets/Quadruplets:** Problems like "3Sum" or "4Sum" often use Two Pointers within a loop. You sort the array first, then iterate with one pointer (`i`) and use Two Pointers (`left`, `right`) on the remainder of the array to find pairs that sum up to `target - nums[i]`.
    *   **Sliding Window:** This is a variation where the pointers define a "window" that slides over the data. Often used for finding subarrays/substrings with certain properties (e.g., max sum subarray of size k, longest substring without repeating characters). The pointers might move in the same direction.
    *   **Fast & Slow Pointers:** Commonly used in linked lists to detect cycles (Floyd's Tortoise and Hare algorithm), find the middle element, or determine list length. One pointer moves faster than the other.
    *   **Removing Duplicates/Elements:** Used in problems like "Remove Duplicates from Sorted Array" or "Remove Element" where one pointer (`slow`) tracks the position for the next unique element, and another pointer (`fast`) iterates through the array.
    *   **String Palindrome Check:** Pointers start at both ends of the string and move inwards, checking if characters match.

*   **Related Problems:**
    *   [LeetCode 15. 3Sum](https://leetcode.com/problems/3sum/)
    *   [LeetCode 11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/) (Classic two pointers from ends)
    *   [LeetCode 26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
    *   [LeetCode 125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
    *   [LeetCode 88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) (Pointers move backward from the end)
    *   [LeetCode 141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) (Fast/Slow pointers)
    *   [LeetCode 3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) (Sliding Window variation)

*   **Key Building Blocks:** The core idea is reducing the search space by intelligently moving pointers based on some condition (usually exploiting sorted order or specific problem constraints). This avoids redundant checks performed by nested loops.

*   **Real-World Applications:**
    *   **Data Processing:** Efficiently finding pairs or ranges in large, sorted datasets.
    *   **Real-time Systems:** Processing streams of data where you need to maintain a window of recent events.
    *   **Search Algorithms:** Optimizing search operations in sorted collections.
    *   **Collision Detection:** In simulations or games, detecting if objects moving towards each other will collide.

Mastering Two Pointers is fundamental for tackling many array and list problems efficiently!

## Quick Understanding Check

### Level 1 (Remember/Recall)
- Q: What is the core advantage of using the Two Pointers technique?
  - A: It allows you to process data in a single pass, often improving efficiency from O(n²) to O(n). 
- Q: What type of data structures is the Two Pointers technique typically applied to?
  - A: Ordered data structures like arrays and sometimes linked lists.
- Q: In the Two Sum II problem, why does moving the left pointer increase the sum?

### Level 2 (Understand/Explain)
- Q: How would you explain the time complexity advantage of the Two Pointers approach compared to a nested loop approach?
  - A: The Two Pointers approach makes a single pass through the array with two pointers moving toward each other, resulting in O(n) time complexity. In contrast, nested loops check every possible pair, leading to O(n²) operations. The Two Pointers technique exploits the sorted property to eliminate unnecessary comparisons.
- Q: Why is the sorted nature of the input array crucial for the Two Pointers technique in the Two Sum problem?
  - A: The sorted order allows us to make intelligent decisions about which pointer to move based on comparing the current sum with the target. If the sum is too small, we can increase it by moving the left pointer; if it's too large, we can decrease it by moving the right pointer. Without sorted order, we wouldn't know which direction to move to approach the target sum efficiently.
- Q: How might you adapt the Two Pointers technique for a "3Sum" problem?
  - A: For a 3Sum problem, you would use a combination of iteration and Two Pointers. For each element in the array (using a loop), fix that element as the first number, then use Two Pointers on the remaining portion of the array to find pairs that sum with the fixed element to the target value.
