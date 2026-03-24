# A Journey Through Data Structures: From Arrays to Graph Algorithms

It has been a while since my first LeetCode entry, *Magical String*. Back then, my focus was primarily on basic string manipulation. Over the past few weeks, I have systematically progressed through more complex data structures and algorithmic paradigms, covering **Arrays, Two Pointers, Sliding Windows, Linked Lists, Stacks, Queues, Trees**, and **Graph Theory (DFS/BFS)**.

This post documents my learning progression, focusing on the core concepts and optimization strategies I’ve acquired rather than just sharing code snippets.

---

## 1. Linear Data Structures and Optimization

The first major milestone was learning how to optimize solutions from brute-force $O(n^2)$ approaches to more efficient $O(n)$ time complexities using specific array techniques.

* **Two Pointers:** This technique involves using two indices to iterate through a data structure (usually an array or string). Depending on the problem, the pointers might move in the same direction to filter data or start at opposite ends and move towards the center. It is highly effective for sorted arrays, allowing for linear time complexity by eliminating unnecessary comparisons.
* **Sliding Window:** This is an optimal approach for problems involving "contiguous subarrays." The logic relies on maintaining a window (defined by a left and right pointer) that expands by moving the right pointer and contracts by moving the left pointer when a specific condition is broken. This dynamically adjusts the search space in a single pass.

---

## 2. Managing Memory and Execution Order

Moving beyond basic arrays required a solid understanding of memory references and execution flow control.

* **Linked Lists:** Working with linked lists fundamentally improved my understanding of pointer manipulation. The critical lesson here is sequence management: you must always store the reference to the `next` node before modifying the current node's pointers; otherwise, the rest of the list is lost in memory.
* **Stacks (LIFO):** Stacks are essential for problems that require reversing the order of processing or delaying execution until a specific condition is met (e.g., valid parentheses or monotonic stacks for finding the "next greater element").
* **Queues (FIFO):** Queues maintain a strict first-in, first-out order. Understanding how to manage a queue was a necessary stepping stone for implementing Breadth-First Search (BFS) later on.

---

## 3. Non-Linear Data Structures: Trees and Graphs

This phase involved shifting from linear iteration to spatial traversal, which significantly increased the complexity of the problems.

### Binary Trees and Recursion
Tree problems solidified my understanding of recursion. By breaking down traversals into `Root`, `Left`, and `Right` operations, I learned how to process data from the bottom up. Information is gathered at the leaf nodes and returned up the call stack to the root.

### Depth-First Search (DFS) in 2D Grids
DFS explores as far as possible along each branch before backtracking. When applied to 2D grid problems (like finding connected components or islands), I learned two critical concepts:
1. **State Modification:** To prevent infinite recursive loops and eventual Stack Overflow errors, it is mandatory to mark visited nodes. In grid problems, this often means modifying the input array in place (e.g., changing a land cell `1` to a water cell `0` after visiting it).
2. **Boundary-First Traversal:** For problems like *Number of Enclaves* (LC 1020), scanning the entire grid is inefficient. A better approach is to run DFS exclusively from the boundaries to eliminate all cells connected to the edge, leaving only the isolated components in the center.

### Breadth-First Search (BFS) and Shortest Paths
While DFS goes deep, BFS explores level by level. It is the standard algorithm for finding the shortest path in an unweighted graph.
* **Multi-Source BFS:** In problems like *Rotting Oranges* (LC 994), multiple starting points exist. The solution involves pushing all initial sources into a `queue<pair<int, int>>` and expanding the search space simultaneously layer by layer.
* **Distance Tracking:** My biggest realization came during the *01 Matrix* problem (LC 542). Instead of tracking global layers or counts, you can directly compute the distance by inheriting the value from the previous node: `mat[nr][nc] = mat[r][c] + 1`. To avoid revisiting nodes, I learned to initialize unvisited targets with a marker value (like `-1`) during the first pass, creating a clean and efficient condition for the BFS traversal.

---

## Conclusion

This learning phase has fundamentally changed how I approach algorithmic problems. Rather than relying on memorized templates, I now focus on identifying the underlying data structure and the appropriate traversal or optimization technique required to solve the problem efficiently. 

Looking forward to the next set of challenges.
