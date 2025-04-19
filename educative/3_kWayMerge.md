# K-Way Merge Pattern Explained

Let's explore the **K-Way Merge** pattern. This pattern is used when you need to merge multiple sorted lists (or arrays, streams, etc.) into a single sorted list. The "K" represents the number of sorted lists you start with.

Imagine you have K sorted lanes of cars merging onto a single highway, and you want the cars on the highway to remain sorted (e.g., by destination exit number). The K-Way Merge pattern provides an efficient way to manage this merge.

## Problem Breakdown: Merge K Sorted Lists

A classic problem illustrating this pattern is **[LeetCode 23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)**.

**The Problem:** You are given an array of `k` linked lists `lists`, where each linked list is sorted in ascending order. Merge all the linked lists into one sorted linked list and return *it*.

**Example:**

*   **Input:** `lists = [[1,4,5],[1,3,4],[2,6]]` (Representing 3 sorted linked lists)
*   **Output:** `[1,1,2,3,4,4,5,6]` (A single sorted linked list)
*   **Explanation:** The lists are combined and sorted.

## Key Insight

*   **Core Concept:** The most efficient way to merge K sorted lists involves repeatedly finding the smallest element among the current heads (the first available element) of all K lists. A **Min-Heap** (Priority Queue) is the perfect data structure for this task. It allows you to efficiently store the head elements and extract the minimum one in logarithmic time.
*   **Why it's tricky:** Merging two sorted lists is straightforward (like the merge step in Merge Sort). However, naively extending this to K lists (e.g., merging list 1 and 2, then merging the result with list 3, and so on) is inefficient (O(N*K), where N is the total number of elements). Comparing the heads of all K lists linearly in each step is also suboptimal (O(N*K)). The Min-Heap approach optimizes finding the minimum element across the K lists.
*   **Solution Idea (Words - using Min-Heap):**
    1.  Create a Min-Heap.
    2.  Initialize the heap by adding the head node (the first element) from each of the K input lists. Store pairs like `(node.value, list_index, node)` in the heap to keep track of the value, which list it came from, and the node itself. (Storing `list_index` isn't strictly necessary for LeetCode 23 if you store the node object, as `node.next` gives you the next element from the same list, but it's conceptually helpful).
    3.  Create a dummy head node for the result list to simplify insertion. Maintain a `current` pointer for the result list, initially pointing to the dummy head.
    4.  While the Min-Heap is not empty:
        a.  Extract the node with the smallest value from the heap. Let this be `(value, index, smallest_node)`.
        b.  Append `smallest_node` to the result list ( `current.next = smallest_node`).
        c.  Advance the `current` pointer (`current = current.next`).
        d.  If `smallest_node` has a next element in its original list (`smallest_node.next` is not null), add that next element (`smallest_node.next`) to the Min-Heap.
    5.  Return the `next` node of the dummy head, which is the start of the merged sorted list.

## Brute Force Approach (Concatenate and Sort)

A very simple but usually inefficient approach:

1.  Iterate through all K lists and collect all nodes/values into a single large list.
2.  Sort this large list.
3.  Reconstruct the sorted linked list from the sorted values.

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

# Simplified Python-like pseudocode (doesn't handle ListNode directly)
def merge_k_lists_brute_force(lists: list[list[int]]) -> list[int]:
    all_elements = []
    for lst in lists:
        all_elements.extend(lst) # In reality, you'd traverse each linked list

    all_elements.sort()

    # In reality, you'd rebuild a linked list here
    return all_elements
```

*   **Time Complexity:** O(N log N), where N is the *total* number of nodes across all lists. The dominant step is sorting the combined list.
*   **Space Complexity:** O(N) to store all elements in the intermediate list. If rebuilding a linked list, that also takes O(N).
*   **Why it's not optimal:** It doesn't leverage the fact that the input lists are already sorted. For large N, this can be slower than heap-based approaches, especially if K is significantly smaller than N.

## 🔥 Optimized Approach (Min-Heap)

This approach efficiently finds the minimum element at each step.

```python
import heapq

# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

    # Add __lt__ for heapq comparison if Python version < 3
    # or if storing nodes directly without tuples
    def __lt__(self, other):
        return self.val < other.val

def merge_k_lists_heap(lists: list[ListNode]) -> ListNode:
    """
    Merges k sorted linked lists using a min-heap.

    Args:
        lists: A list of k sorted ListNode heads.

    Returns:
        The head of the single merged sorted linked list.
    """
    # Min-heap will store tuples: (value, node_object)
    # We need node_object to access node.next
    # ListNode needs __lt__ defined for direct comparison by heapq
    min_heap = []

    # 1. Initialize the heap with the head of each list
    for head_node in lists:
        if head_node:
            heapq.heappush(min_heap, (head_node.val, head_node))
            # Alternatively, if ListNode doesn't have __lt__:
            # heapq.heappush(min_heap, (head_node.val, id(head_node), head_node))
            # Using id() as a tie-breaker if values are equal

    # 2. Dummy head for the result list
    dummy_head = ListNode(-1)
    current = dummy_head

    # 3. While the heap is not empty
    while min_heap:
        # a. Extract the smallest node
        val, smallest_node = heapq.heappop(min_heap)

        # b. Append it to the result list
        current.next = smallest_node
        # c. Advance the current pointer
        current = current.next

        # d. If the extracted node has a successor, add it to the heap
        if smallest_node.next:
            next_node = smallest_node.next
            heapq.heappush(min_heap, (next_node.val, next_node))
            # Or with id tie-breaker:
            # heapq.heappush(min_heap, (next_node.val, id(next_node), next_node))

    # 4. Return the merged list (skip dummy head)
    return dummy_head.next

# Helper function to create a linked list from a list
def create_linked_list(arr):
    if not arr: return None
    head = ListNode(arr[0])
    curr = head
    for i in range(1, len(arr)):
        curr.next = ListNode(arr[i])
        curr = curr.next
    return head

# Helper function to print a linked list
def print_linked_list(head):
    res = []
    curr = head
    while curr:
        res.append(curr.val)
        curr = curr.next
    print(res)

# Example Usage:
list1 = create_linked_list([1, 4, 5])
list2 = create_linked_list([1, 3, 4])
list3 = create_linked_list([2, 6])
lists = [list1, list2, list3]

print("Input Lists:")
print_linked_list(list1)
print_linked_list(list2)
print_linked_list(list3)

merged_head = merge_k_lists_heap(lists)
print("\nMerged List:")
print_linked_list(merged_head) # Output: [1, 1, 2, 3, 4, 4, 5, 6]

```

*   **Walkthrough (Conceptual):**
    1.  **Heap Init:** Add `(1, node(1 from list1))`, `(1, node(1 from list2))`, `(2, node(2 from list3))` to heap.
    2.  **Extract min:** Pop `(1, node(1 from list1))`. Append node(1) to result. Add its successor `(4, node(4 from list1))` to heap.
    3.  **Extract min:** Pop `(1, node(1 from list2))`. Append node(1) to result. Add its successor `(3, node(3 from list2))` to heap.
    4.  **Extract min:** Pop `(2, node(2 from list3))`. Append node(2) to result. Add its successor `(6, node(6 from list3))` to heap.
    5.  **Extract min:** Pop `(3, node(3 from list2))`. Append node(3) to result. Add its successor `(4, node(4 from list2))` to heap.
    6.  **Extract min:** Pop `(4, node(4 from list1))` (or the other 4, heap order might vary for ties). Append node(4) to result. Add its successor `(5, node(5 from list1))` to heap.
    7.  ...continue until heap is empty.
*   **Time Complexity:** O(N log K), where N is the total number of nodes and K is the number of lists. Inserting the first K nodes takes O(K log K). Each subsequent extraction takes O(log K) and each insertion takes O(log K). Since there are N nodes in total, we perform approximately N extraction/insertion pairs. So, the main loop runs in O(N log K).
*   **Space Complexity:** O(K) for the Min-Heap, which stores at most one node from each of the K lists at any time. (O(N) if you count the space for the output list).

## How This Pattern is Reused in Harder Problems

The K-Way merge pattern, especially the Min-Heap approach, is fundamental for problems involving merging multiple sorted data sources.

*   **Connections & Patterns:**
    *   **External Sort:** When sorting datasets too large to fit in memory, external sort algorithms often break the data into sorted chunks (that fit in memory) and then use a K-way merge (with a Min-Heap) to merge these chunks from disk.
    *   **Finding Smallest Range:** Problems like "Smallest Range Covering Elements from K Lists" (LeetCode 632) use a similar heap-based approach to track the minimum and maximum elements currently considered across the K lists.
    *   **Event Scheduling/Processing:** Systems processing events from multiple sorted queues might use K-way merging to handle events in global chronological order.
    *   **Matrix Problems:** Finding the Kth smallest element in a sorted matrix (LeetCode 378) can be viewed as merging K sorted rows, solvable with a Min-Heap.

*   **Related Problems:**
    *   [LeetCode 21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/) (Base case, K=2)
    *   [LeetCode 378. Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)
    *   [LeetCode 632. Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/)
    *   [LeetCode 786. K-th Smallest Prime Fraction](https://leetcode.com/problems/k-th-smallest-prime-fraction/) (Can be modeled as merging sorted lists of fractions)

*   **Key Building Blocks:** Using a Min-Heap to efficiently manage and retrieve the smallest element from multiple sorted sources is the core technique.

*   **Real-World Applications:**
    *   **Database Merging:** Merging results from multiple sorted index scans.
    *   **Log Aggregation:** Combining timestamp-sorted logs from multiple servers.
    *   **Distributed Systems:** Aggregating sorted results or events from different nodes.
    *   **Large File Sorting (External Sort):** As mentioned, merging sorted chunks stored on disk.

K-Way Merge is a powerful technique for combining sorted data efficiently.

## Quick Understanding Check

### Level 1 (Remember/Recall)
- Q: What key data structure is used in the K-Way Merge pattern?
  - A: A Min-Heap (Priority Queue). [*Source: Key Insight section*]
- Q: What information do we typically store in the heap when implementing K-Way Merge?
  - A: The value, list index, and node object (or a way to access the next element). [*Source: Solution Idea section, step 2*]
- Q: What is the time complexity of the optimized K-Way Merge approach?
  - A: O(N log K), where N is the total number of nodes and K is the number of lists. [*Source: Time Complexity analysis*]

### Level 2 (Understand/Explain)
- Q: Why is using a Min-Heap more efficient than repeatedly finding the minimum head value across all K lists?
  - A: Using a Min-Heap allows us to find the minimum element in O(log K) time, rather than O(K) time for linear search across K list heads. Over N operations, this reduces the time complexity from O(N*K) to O(N log K), which is significantly faster when K is large.
- Q: How does the K-Way Merge pattern leverage the property that the input lists are already sorted?
  - A: Since each list is already sorted, we only need to consider the current head element of each list as a candidate for the next element in the merged result. This allows us to maintain a heap of just K elements (one from each list) rather than all N elements, significantly reducing space complexity from O(N) to O(K).
- Q: In what real-world scenario might you apply the K-Way Merge pattern, and why would it be effective?
  - A: In a distributed system where multiple servers each have their own sorted logs with timestamps, you could use K-Way Merge to efficiently aggregate these logs in chronological order. It's effective because you leverage the existing sorted order and only need to maintain K positions in memory (one for each server's log), regardless of how large the total dataset is.
 