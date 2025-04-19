# Two Heaps Pattern Explained

The **Two Heaps** pattern is a clever approach to solve problems where you need to find the median or middle element of a data stream, or when you need to divide elements into two parts with specific ordering properties.

## Problem Breakdown

Let's use the classic problem to illustrate this pattern: **[LeetCode 295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)**.

**The Problem:** Design a data structure that supports:
- Adding integers to the structure
- Finding the median of all added integers
- The median is the middle value in a sorted sequence. If the sequence has an even length, the median is the average of the two middle values.

**Example:**
```python
MedianFinder medianFinder = new MedianFinder()
medianFinder.addNum(1)    # [1]
medianFinder.findMedian() # return 1.0
medianFinder.addNum(2)    # [1, 2]
medianFinder.findMedian() # return 1.5 (average of 1 and 2)
medianFinder.addNum(3)    # [1, 2, 3]
medianFinder.findMedian() # return 2.0
```

## Key Insight

- **Core Concept:** Maintain two heaps:
  1. A max-heap for the lower half of numbers
  2. A min-heap for the upper half of numbers
- The heaps should be balanced (or nearly balanced), with the max-heap containing at most one more element than the min-heap
- The median can then be found by:
  - If heaps have equal size: average of max-heap top and min-heap top
  - If max-heap has one more element: max-heap top
- **Why it's tricky:** 
  - Maintaining the balance between heaps while ensuring proper ordering
  - Handling stream of data efficiently
- **Solution Idea (Words):**
  1. When adding a number, compare it with the tops of both heaps
  2. Add to appropriate heap based on comparison
  3. Rebalance heaps if necessary
  4. For finding median, use heap tops based on total count

## Brute Force Approach

A simple but inefficient approach would be to store all numbers in a sorted array:

```python
class MedianFinderBrute:
    def __init__(self):
        self.numbers = []
    
    def addNum(self, num: int) -> None:
        # Insert number in sorted position
        index = bisect.bisect_left(self.numbers, num)
        self.numbers.insert(index, num)
    
    def findMedian(self) -> float:
        n = len(self.numbers)
        if n % 2 == 0:
            return (self.numbers[n//2 - 1] + self.numbers[n//2]) / 2
        return float(self.numbers[n//2])
```

- **Time Complexity:** 
  - addNum: O(N) due to insertion
  - findMedian: O(1)
- **Space Complexity:** O(N)
- **Why it's not optimal:** Insertion takes linear time as we need to shift elements

## 🔥 Optimized Approach

Here's the efficient two-heaps solution:

```python
import heapq

class MedianFinder:
    def __init__(self):
        # Max heap for lower half (multiply by -1 to simulate max heap)
        self.lower_half = []
        # Min heap for upper half
        self.upper_half = []
    
    def addNum(self, num: int) -> None:
        # If lower half is empty or num is less than max of lower half
        if not self.lower_half or num < -self.lower_half[0]:
            heapq.heappush(self.lower_half, -num)
        else:
            heapq.heappush(self.upper_half, num)
        
        # Rebalance if necessary
        if len(self.lower_half) > len(self.upper_half) + 1:
            heapq.heappush(self.upper_half, -heapq.heappop(self.lower_half))
        elif len(self.upper_half) > len(self.lower_half):
            heapq.heappush(self.lower_half, -heapq.heappop(self.upper_half))
    
    def findMedian(self) -> float:
        if len(self.lower_half) > len(self.upper_half):
            return float(-self.lower_half[0])
        return (-self.lower_half[0] + self.upper_half[0]) / 2

# Example usage:
finder = MedianFinder()
finder.addNum(1)
print(finder.findMedian())  # 1.0
finder.addNum(2)
print(finder.findMedian())  # 1.5
finder.addNum(3)
print(finder.findMedian())  # 2.0
```

- **Time Complexity:**
  - addNum: O(log N) for heap operations
  - findMedian: O(1)
- **Space Complexity:** O(N) to store N numbers across two heaps

## How This Pattern is Reused in Harder Problems

The Two Heaps pattern is particularly useful in problems involving:

1. **Sliding Window Median:**
   - [LeetCode 480. Sliding Window Median](https://leetcode.com/problems/sliding-window-median/)
   - Maintain two heaps while sliding the window

2. **Schedule Problems:**
   - [LeetCode 253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
   - Use min-heap for end times and track overlaps

3. **Stream Processing:**
   - Finding running median
   - Processing time-series data

**Key Building Blocks:**
- Balancing two heaps
- Using negative numbers to simulate max-heap
- Maintaining ordering invariants between heaps

**Real-World Applications:**
1. **Financial Systems:**
   - Computing running medians of stock prices
   - Analyzing trading volumes

2. **Monitoring Systems:**
   - Finding median response times
   - Detecting anomalies in metrics

3. **Load Balancing:**
   - Distributing tasks between two groups of servers
   - Maintaining balanced processing queues

4. **Data Analytics:**
   - Computing rolling medians
   - Finding mid-points in streaming data

The Two Heaps pattern demonstrates how seemingly complex problems can be solved efficiently by maintaining two complementary data structures. It's particularly valuable in scenarios involving streaming data where you need to maintain some form of middle ground or balanced partition. 

## Quick Understanding Check

### Level 1 (Remember/Recall)
- Q: What are the two types of heaps used in the Two Heaps pattern?
  - A: A max-heap for the lower half of numbers and a min-heap for the upper half of numbers. [*Source: Key Insight section*]
- Q: How should the two heaps be balanced for finding the median?
  - A: The heaps should be balanced (or nearly balanced), with the max-heap containing at most one more element than the min-heap. [*Source: Key Insight section*]
- Q: What is the time complexity of adding a number in the Two Heaps approach?
  - A: O(log N) for heap operations. [*Source: Time Complexity analysis*]

### Level 2 (Understand/Explain)
- Q: Why does the Two Heaps pattern use two different types of heaps (max and min) rather than two of the same type?
  - A: The two different heap types serve distinct purposes: the max-heap keeps the lower half of numbers with the largest (median candidate) at the top, while the min-heap keeps the upper half with the smallest (another median candidate) at the top. This arrangement gives O(1) access to the middle value(s) needed for median calculation, which wouldn't be possible with two heaps of the same type.
- Q: Explain the logic behind the rebalancing step in the Two Heaps pattern.
  - A: Rebalancing ensures that the heaps maintain their relative sizes (equal or max-heap having one more element) after each insertion. This is crucial because the median calculation depends on this specific balance. If one heap grows much larger than the other, the middle elements would shift, making the median calculation incorrect. The rebalancing transfers elements between heaps to maintain this invariant.
- Q: How would you modify the Two Heaps approach to handle a streaming data scenario where old values might expire?
  - A: You would need to track elements in both heaps using additional data structures (like hash maps) to know which heap contains expired elements. When an element expires, remove it from its heap (requiring a custom implementation or lazy removal). After removal, rebalance the heaps if needed. This modification allows maintaining the median of a sliding window of valid data points. 