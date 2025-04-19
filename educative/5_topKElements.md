# Top K Elements Pattern Explained

Let's explore the **Top K Elements** pattern! This pattern is used when you need to find the K largest/smallest/most frequent elements in a dataset. It's particularly efficient when K is much smaller than the size of the input (K << N).

The key to solving these problems efficiently is using a **Heap** (Priority Queue). For finding K largest elements, use a Min-Heap of size K. For K smallest elements, use a Max-Heap of size K. This way, you maintain only K elements at any time, making it memory efficient.

## Problem Breakdown: Kth Largest Element in an Array

Let's use a classic problem to illustrate this pattern: **[LeetCode 215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)**.

**The Problem:** Given an integer array `nums` and an integer `k`, return the kth largest element in the array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example:**
*   **Input:** `nums = [3,2,1,5,6,4]`, `k = 2`
*   **Output:** `5`
*   **Explanation:** The array sorted in descending order is `[6,5,4,3,2,1]`, so the 2nd largest element is 5.

## Key Insight

*   **Core Concept:** For finding the Kth largest element, maintain a min-heap of size K. As you process each number:
    - If the heap size is less than K, add the number.
    - If the number is larger than the smallest number in the heap (heap top), remove the top and add the new number.
    - After processing all numbers, the heap top is the Kth largest number.
*   **Why it's efficient:** You never need to store more than K elements, and each heap operation is O(log K) rather than O(log N).
*   **Solution Idea (Words):**
    1.  Create a min-heap.
    2.  Process each number in the array:
        - If heap size < K: Add the number to heap
        - If heap size = K and current number > heap top: Remove top, add current number
    3.  After processing all numbers, the heap top is your answer.

## Brute Force Approach (Sort)

The simplest approach would be to sort the array and return the Kth element from the end.

```python
def find_kth_largest_sort(nums: list[int], k: int) -> int:
    # Sort in descending order and return kth element
    nums.sort(reverse=True)
    return nums[k-1]

# Example Usage:
nums1 = [3, 2, 1, 5, 6, 4]
k1 = 2
print(f"Input: nums = {nums1}, k = {k1}")
print(f"Output: {find_kth_largest_sort(nums1, k1)}") # Output: 5
```

*   **Time Complexity:** O(N log N) for sorting.
*   **Space Complexity:** O(1) if using in-place sort (though some sort implementations might use O(N) space).
*   **Why it's not optimal:** We sort the entire array when we only need the Kth largest element.

## 🔥 Optimized Approach (Min-Heap)

Here's the heap-based solution:

```python
import heapq

def find_kth_largest(nums: list[int], k: int) -> int:
    """
    Finds the kth largest element in an array using a min-heap of size k.
    
    Args:
        nums: List of integers
        k: The k value for finding kth largest element
        
    Returns:
        The kth largest element
    """
    # Create a min heap
    min_heap = []
    
    # Process each number
    for num in nums:
        if len(min_heap) < k:
            # If heap has less than k elements, add the number
            heapq.heappush(min_heap, num)
        elif num > min_heap[0]:
            # If current number is larger than the smallest number in heap,
            # remove smallest and add current number
            heapq.heapreplace(min_heap, num)
    
    # The top of the heap is the kth largest element
    return min_heap[0]

# Example Usage:
nums1 = [3, 2, 1, 5, 6, 4]
k1 = 2
print(f"Input: nums = {nums1}, k = {k1}")
print(f"Output: {find_kth_largest(nums1, k1)}") # Output: 5

nums2 = [3, 2, 3, 1, 2, 4, 5, 5, 6]
k2 = 4
print(f"Input: nums = {nums2}, k = {k2}")
print(f"Output: {find_kth_largest(nums2, k2)}") # Output: 4
```

*   **Walkthrough (Example: `nums = [3,2,1,5,6,4]`, `k = 2`):**
    1.  Process 3: Heap = `[3]`
    2.  Process 2: Heap = `[2,3]`
    3.  Process 1: Heap size = k (2), 1 < heap top (2), skip
    4.  Process 5: Heap size = k (2), 5 > heap top (2), remove 2, add 5: Heap = `[3,5]`
    5.  Process 6: Heap size = k (2), 6 > heap top (3), remove 3, add 6: Heap = `[5,6]`
    6.  Process 4: Heap size = k (2), 4 < heap top (5), skip
    7.  Final heap = `[5,6]`. Return top element: 5

*   **Time Complexity:** O(N log K). We process N elements, and each heap operation takes O(log K).
*   **Space Complexity:** O(K) to store the heap.

## Variations of the Pattern

The Top K pattern can be adapted for various similar problems:

1. **K Largest/Smallest Elements:**
```python
def k_largest_elements(nums: list[int], k: int) -> list[int]:
    min_heap = []
    for num in nums:
        if len(min_heap) < k:
            heapq.heappush(min_heap, num)
        elif num > min_heap[0]:
            heapq.heapreplace(min_heap, num)
    return sorted(min_heap, reverse=True)  # Sort for nice output format

def k_smallest_elements(nums: list[int], k: int) -> list[int]:
    # Use negative numbers with min-heap to simulate max-heap
    max_heap = []
    for num in nums:
        if len(max_heap) < k:
            heapq.heappush(max_heap, -num)
        elif -num > max_heap[0]:
            heapq.heapreplace(max_heap, -num)
    return sorted([-x for x in max_heap])  # Convert back to positive and sort
```

2. **Top K Frequent Elements:**
```python
from collections import Counter

def top_k_frequent(nums: list[int], k: int) -> list[int]:
    # Count frequencies
    count = Counter(nums)
    
    # Use min heap based on frequencies
    heap = []
    for num, freq in count.items():
        if len(heap) < k:
            heapq.heappush(heap, (freq, num))
        elif freq > heap[0][0]:
            heapq.heapreplace(heap, (freq, num))
    
    # Extract numbers (ignore frequencies)
    return [num for freq, num in sorted(heap, reverse=True)]
```

## How This Pattern is Reused in Harder Problems

The Top K Elements pattern is fundamental for many problems involving finding, maintaining, or processing the K most important items according to some criteria.

*   **Connections & Patterns:**
    *   **K-Way Merge:** When merging K sorted arrays, you can use a min-heap to track the current smallest element from each array.
    *   **Sliding Window:** For problems like "sliding window maximum", you might need to maintain the K largest elements in a window.
    *   **Stream Processing:** When processing infinite streams, you can maintain the K largest/smallest elements seen so far.
    *   **Frequency Based Problems:** Finding most frequent elements, least frequent elements, or elements with frequencies meeting certain criteria.

*   **Related Problems:**
    *   [LeetCode 347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
    *   [LeetCode 973. K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)
    *   [LeetCode 692. Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)
    *   [LeetCode 451. Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)
    *   [LeetCode 703. Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/)

*   **Key Building Blocks:**
    *   Using a heap of size K to maintain the K most important elements
    *   Choosing between min-heap and max-heap based on the problem
    *   Combining with frequency counting for frequency-based problems
    *   Using custom comparison functions for complex objects

*   **Real-World Applications:**
    *   **Search Results:** Returning top K most relevant results
    *   **Recommendation Systems:** Finding K most similar items
    *   **System Monitoring:** Tracking K most resource-intensive processes
    *   **Analytics:** Finding K most frequent events/visitors/purchases
    *   **Network Traffic:** Identifying K largest data flows

The Top K Elements pattern is particularly valuable in scenarios where you need to find or maintain a subset of the most important elements from a larger dataset, especially when K is significantly smaller than the total number of elements.

## Quick Understanding Check

### Level 1 (Remember/Recall)
- Q: What heap type should you use when finding the K largest elements in an array?
  - A: A Min-Heap of size K. [*Source: Introduction paragraph*]
- Q: What heap type should you use when finding the K smallest elements in an array?
  - A: A Max-Heap of size K. [*Source: Introduction paragraph*]
- Q: What is the time complexity of the heap-based approach for finding the Kth largest element?
  - A: O(N log K), where N is the number of elements in the array. [*Source: Time Complexity analysis*]

### Level 2 (Understand/Explain)
- Q: Why do we use a Min-Heap (not a Max-Heap) when finding the K largest elements?
  - A: A Min-Heap keeps track of the K largest elements seen so far, with the smallest of these K elements at the top. This allows us to quickly identify and replace the "smallest of the largest" when we encounter a new element larger than the heap top. This approach maintains only K elements in memory and efficiently discards elements that cannot be among the K largest.
- Q: Compare the efficiency of using a heap versus sorting the entire array when K is much smaller than N.
  - A: When K << N, a heap-based approach processes each element in O(log K) time, resulting in O(N log K) total time. In contrast, sorting requires O(N log N) time regardless of K. Since log K is much smaller than log N when K << N, the heap approach is significantly more efficient. Additionally, the heap only requires O(K) space, while sorting might need O(N) space.
- Q: How would you modify the Top K pattern to find the K most frequent elements in an array?
  - A: You would first count the frequency of each element using a hash map. Then, use a min-heap to keep track of the K elements with the highest frequencies. For each element-frequency pair, if the heap size is less than K, add it to the heap; otherwise, if the current frequency is higher than the smallest frequency in the heap, replace the heap top. This approach maintains the K most frequent elements seen so far. 