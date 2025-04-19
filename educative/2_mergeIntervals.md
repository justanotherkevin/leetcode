# Merge Intervals Pattern Explained

Alright, let's talk about the **Merge Intervals** pattern! This is a common algorithmic technique used when you have a collection of intervals (like time slots, ranges, etc.) and you need to merge the ones that overlap.

Think about scheduling meetings. If you have one meeting from 9:00 to 10:30 and another from 10:00 to 11:00, they overlap. You could merge them into a single block from 9:00 to 11:00. The Merge Intervals pattern helps automate this process efficiently.

## Problem Breakdown: Merge Intervals

Let's use the quintessential LeetCode problem for this pattern: **[LeetCode 56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)**.

**The Problem:** Given an array of `intervals` where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals, and return *an array of the non-overlapping intervals that cover all the intervals in the input*.

**Example:**

*   **Input:** `intervals = [[1,3],[2,6],[8,10],[15,18]]`
*   **Output:** `[[1,6],[8,10],[15,18]]`
*   **Explanation:** Since intervals `[1,3]` and `[2,6]` overlap, merge them into `[1,6]`. The other intervals `[8,10]` and `[15,18]` do not overlap with `[1,6]` or each other.

## Key Insight

*   **Core Concept:** The crucial first step is **sorting**. If you sort the intervals based on their starting points, you only need to compare adjacent (or potentially overlapping) intervals. Once sorted, you can iterate through the intervals and decide whether to merge the current interval with the previous merged interval or start a new non-overlapping interval.
*   **Why it's tricky:** Without sorting, figuring out which intervals overlap with which becomes a much more complex comparison problem (potentially checking every pair).
*   **Solution Idea (Words):**
    1.  Sort the input array of intervals based on their starting times.
    2.  Initialize a list to store the merged intervals. Add the first interval from the sorted list to it.
    3.  Iterate through the rest of the sorted intervals (starting from the second one).
    4.  For each interval, compare its start time with the *end* time of the *last* interval added to the merged list.
    5.  If the current interval overlaps with the last merged interval (i.e., `current_interval.start <= last_merged_interval.end`), then merge them by updating the end time of the last merged interval to be the maximum of its current end time and the current interval's end time (`last_merged_interval.end = max(last_merged_interval.end, current_interval.end)`).
    6.  If the current interval does *not* overlap, it means we've finished a merged block (or the previous one was standalone). Add the current interval as a new entry to the merged list.
    7.  After iterating through all intervals, the merged list contains the final result.

## Brute Force Approach (Less Efficient)

A less efficient approach might involve repeatedly scanning the list, merging any pair found to overlap, and repeating until no more merges are possible. This often leads to multiple passes and complex management.

Another approach could be comparing every interval with every other interval, which would be O(n²), plus the complexity of merging.

*Sorting first is the key to an efficient single-pass solution.* We'll skip coding a brute-force version as the sorted approach is standard.

## 🔥 Optimized Approach (Sort and Merge)

Here's the implementation using the sort and merge strategy:

```python
def merge_intervals(intervals: list[list[int]]) -> list[list[int]]:
    """
    Merges overlapping intervals.

    Args:
        intervals: A list of intervals, where each interval is [start, end].

    Returns:
        A list of non-overlapping intervals covering all input intervals.
    """
    if not intervals:
        return []

    # 1. Sort intervals based on the start time
    # The lambda function tells sort() to use the first element (index 0) of each sublist
    intervals.sort(key=lambda x: x[0])

    merged_intervals = []
    # 2. Add the first interval to start the process
    merged_intervals.append(intervals[0])

    # 3. Iterate through the rest of the intervals
    for i in range(1, len(intervals)):
        current_start, current_end = intervals[i]
        last_merged_start, last_merged_end = merged_intervals[-1] # Get the last interval in the merged list

        # 4. Check for overlap
        if current_start <= last_merged_end:
            # 5. Overlap detected: Merge by updating the end time of the last interval
            # We take the maximum end time because the current interval might end later
            # than the previous last_merged_end (e.g., [1,3], [2,6] -> merge to [1,6])
            # or it might be completely contained (e.g., [1,6], [2,4] -> still [1,6])
            merged_intervals[-1][1] = max(last_merged_end, current_end)
        else:
            # 6. No overlap: Add the current interval as a new separate interval
            merged_intervals.append([current_start, current_end])

    # 7. Return the result
    return merged_intervals

# Example Usage:
intervals1 = [[1,3],[2,6],[8,10],[15,18]]
print(f"Input: {intervals1}")
print(f"Output: {merge_intervals(intervals1)}") # Output: [[1, 6], [8, 10], [15, 18]]

intervals2 = [[1,4],[4,5]]
print(f"Input: {intervals2}")
print(f"Output: {merge_intervals(intervals2)}") # Output: [[1, 5]]

intervals3 = [[1,5],[2,4]]
print(f"Input: {intervals3}")
print(f"Output: {merge_intervals(intervals3)}") # Output: [[1, 5]]
```

*   **Walkthrough (Example: `intervals = [[1,3],[2,6],[8,10],[15,18]]`):**
    1.  **Sort:** Input is already sorted by start time: `[[1,3],[2,6],[8,10],[15,18]]`.
    2.  **Initialize:** `merged = [[1,3]]`.
    3.  **Process `[2,6]`:** `current_start = 2`, `last_merged_end = 3`. Since `2 <= 3` (overlap), update last merged interval's end: `merged[-1][1] = max(3, 6) = 6`. Now `merged = [[1,6]]`.
    4.  **Process `[8,10]`:** `current_start = 8`, `last_merged_end = 6`. Since `8 > 6` (no overlap), add `[8,10]` to `merged`. Now `merged = [[1,6], [8,10]]`.
    5.  **Process `[15,18]`:** `current_start = 15`, `last_merged_end = 10`. Since `15 > 10` (no overlap), add `[15,18]` to `merged`. Now `merged = [[1,6], [8,10], [15,18]]`.
    6.  **End:** Return `merged`.
*   **Time Complexity:** O(n log n). The dominant step is sorting the intervals. The merging process itself takes O(n) because we iterate through the sorted intervals once.
*   **Space Complexity:** O(n) or O(log n), depending on the sort implementation. O(n) if we consider the space needed for the output array `merged_intervals`. If we can modify the input array in-place (sometimes allowed, sometimes not) and the sort uses O(log n) space (like Timsort in Python), the auxiliary space is O(log n). If we must create a separate output array, it's O(n).

## How This Pattern is Reused in Harder Problems

The Merge Intervals pattern is a building block for many other problems:

*   **Connections & Patterns:**
    *   **Meeting Rooms:** Problems like "Meeting Rooms II" (LeetCode 253) ask for the minimum number of conference rooms required for a set of meetings (intervals). This often involves sorting and using a min-heap to track meeting end times.
    *   **Inserting Intervals:** Problems like "Insert Interval" (LeetCode 57) involve adding a new interval into a list of non-overlapping intervals and merging if necessary. The core merging logic is similar.
    *   **Finding Overlaps/Conflicts:** Identifying conflicting appointments or resource bookings.
    *   **Data Compression/Summarization:** Merging adjacent or overlapping data ranges.
    *   **Geometric Problems:** Problems involving ranges on a line or overlaps in 1D space.

*   **Related Problems:**
    *   [LeetCode 57. Insert Interval](https://leetcode.com/problems/insert-interval/)
    *   [LeetCode 252. Meeting Rooms](https://leetcode.com/problems/meeting-rooms/) (Check if any meetings overlap)
    *   [LeetCode 253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) (Find max concurrent meetings)
    *   [LeetCode 435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) (Find minimum intervals to remove to make rest non-overlapping)
    *   [LeetCode 763. Partition Labels](https://leetcode.com/problems/partition-labels/) (Can be solved by determining the "reach" of each character, forming intervals, and merging them)

*   **Key Building Blocks:** Sorting by start time, iterating, and comparing the current interval with the end of the last merged interval are the fundamental steps.

*   **Real-World Applications:**
    *   **Calendar Management:** Merging busy time slots.
    *   **Resource Allocation:** Combining time blocks when a resource is used.
    *   **Genomics:** Merging overlapping gene sequences or regions of interest.
    *   **Data Visualization:** Grouping continuous data points into ranges.

Understanding how to efficiently sort and merge intervals is key for tackling these types of problems.

## Quick Understanding Check

### Level 1 (Remember/Recall)
- Q: What is the first crucial step in the Merge Intervals pattern?
  - A: Sorting the intervals based on their starting points. [*Source: Key Insight section*]
- Q: How do you determine if two intervals overlap?
  - A: Two intervals overlap if the start time of the current interval is less than or equal to the end time of the last merged interval. [*Source: Key Insight section, step 4*]
- Q: What is the time complexity of the optimized Merge Intervals approach?
  - A: O(n log n), with the dominant step being sorting the intervals. [*Source: Time Complexity analysis*]

### Level 2 (Understand/Explain)
- Q: Why does sorting make the Merge Intervals problem more efficient?
  - A: Sorting by start times ensures that potentially overlapping intervals are adjacent in the array. This allows us to make a single pass through the sorted array, comparing only adjacent intervals instead of checking every possible pair of intervals, which would require O(n²) comparisons.
- Q: In the merge_intervals function, why do we take the maximum of last_merged_end and current_end when merging?
  - A: We take the maximum because the current interval might be completely contained within the last merged interval (e.g., [1,6], [2,4]), or it might extend beyond it (e.g., [1,3], [2,6]). By taking the maximum, we ensure the merged interval covers the entire range of both intervals.
- Q: How would you adapt the Merge Intervals pattern to solve the "Insert Interval" problem?
  - A: To insert a new interval, you would process the existing intervals in order and handle three cases: 1) intervals that come before the new interval (add directly to result), 2) intervals that overlap with the new interval (merge them), and 3) intervals that come after the merged interval (add directly to result). 