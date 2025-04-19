# 133. Clone Graph

## Problem Breakdown

Given a reference of a node in a connected undirected graph, we need to return a deep copy (clone) of the graph. Each node in the graph contains a value and a list of its neighbors.

### Example:
```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]
Explanation:
Node 1's value is 1, and it has two neighbors: Node 2 and 4.
Node 2's value is 2, and it has two neighbors: Node 1 and 3.
Node 3's value is 3, and it has two neighbors: Node 2 and 4.
Node 4's value is 4, and it has two neighbors: Node 1 and 3.
```

### Constraints:
- Number of nodes: [0, 100]
- Node values: [1, 100]
- No repeated edges
- No self-loops
- Connected undirected graph

## Bloom's Taxonomy Level 1 (Remember/Knowledge)

### Graph Basics
1. What is an undirected graph?
   - Answer: A graph where edges have no direction, meaning if node A is connected to node B, then B is also connected to A.

2. What is a node in this graph?
   - Answer: A data structure that contains:
     * A value (integer)
     * A list of references to neighboring nodes

### Deep Copy Concepts
3. What is a deep copy?
   - Answer: A complete copy where all nested objects are also copied, not just references.

4. Why is deep copy important in this problem?
   - Answer: To create a completely independent copy of the graph with no shared references to the original.

### Data Structure Knowledge
5. What data structures are useful for graph traversal?
   - Answer:
     * Queue/Stack for traversal
     * HashMap for tracking visited nodes and their copies
     * List for storing neighbors

### Edge Cases
6. What are the important edge cases?
   - Answer:
     * Empty graph (null node)
     * Single node graph
     * Cyclic graph
     * Fully connected graph
     * Linear graph

## Key Insight 

The core concept here is graph traversal with state tracking. The tricky part is:
1. We need to create new nodes while maintaining the same structure
2. We need to handle cycles in the graph
3. We need to ensure we don't create duplicate copies of nodes

What makes this problem interesting:
- Need to maintain a mapping between original and copied nodes
- Need to handle cycles without getting stuck in infinite loops
- Need to ensure all connections are properly recreated
- Can be solved using either DFS or BFS approach

## Optimized Solution

### Approach: DFS with HashMap
We'll use a hash map to keep track of nodes we've already cloned:

1. Use HashMap to store mapping of original nodes to their copies
2. For each node:
   - If already cloned, return the clone
   - Create new node with same value
   - Store in HashMap
   - Recursively clone all neighbors
   - Add cloned neighbors to new node

### Python Implementation:
```python
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        if not node:
            return None
            
        # HashMap to store node -> copy mappings
        cloned = {}
        
        def dfs(node):
            # If node already cloned, return its copy
            if node in cloned:
                return cloned[node]
            
            # Create new node with same value
            copy = Node(node.val)
            # Store in HashMap before DFS to handle cycles
            cloned[node] = copy
            
            # Clone all neighbors
            for neighbor in node.neighbors:
                copy.neighbors.append(dfs(neighbor))
                
            return copy
            
        return dfs(node)
```

### Alternative BFS Approach:
```python
from collections import deque

class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        if not node:
            return None
            
        # Create first copy and add to HashMap
        cloned = {node: Node(node.val)}
        queue = deque([node])
        
        while queue:
            curr = queue.popleft()
            
            # Process all neighbors
            for neighbor in curr.neighbors:
                # If neighbor not yet cloned, create copy and add to queue
                if neighbor not in cloned:
                    cloned[neighbor] = Node(neighbor.val)
                    queue.append(neighbor)
                    
                # Add neighbor connection to cloned graph
                cloned[curr].neighbors.append(cloned[neighbor])
                
        return cloned[node]
```

### Complexity Analysis:
- Time Complexity: O(V + E) where V is vertices and E is edges
  - We visit each node and edge once
- Space Complexity: O(V)
  - HashMap stores each vertex once
  - Recursion/queue depth can go up to V

## How This Problem is Reused

### 1. Common Patterns
- Graph traversal pattern (DFS/BFS)
- HashMap for state tracking
- Deep copy pattern
- Cycle handling in graphs

### 2. Related LeetCode Problems
- 138: Copy List with Random Pointer
  - Similar concept but with linked list and random pointers
- 1485: Clone Binary Tree With Random Pointer
  - Tree version of the same concept
- 323: Number of Connected Components in an Undirected Graph
  - Uses similar graph traversal techniques
- 207: Course Schedule
  - Uses similar graph representation and traversal

### 3. Real-world Applications
- Social network cloning/backup systems
- State management in distributed systems
- Game state duplication
- Version control systems (creating independent branches)
- Database replication

### 4. Key Takeaways
1. HashMap is crucial for tracking visited/cloned nodes
2. Handle cycles by creating node before processing neighbors
3. Both DFS and BFS are valid approaches
4. Deep copy requires careful handling of all references
5. Graph problems often require state tracking to avoid cycles
