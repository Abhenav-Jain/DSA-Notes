# 🔵 LeetCode 1971 — Find if Path Exists in Graph

**Pattern:** Graph + DFS + Adjacency List
**Difficulty:** Easy
**Core Concept:** DFS Traversal to check whether a path exists between two vertices.

---

## 1. Problem Statement

Given an **undirected graph** with `n` vertices numbered from `0` to `n-1` and a list of edges.

Given:

* `source`
* `destination`

Return `true` if there exists **at least one path** from `source` to `destination`, otherwise return `false`.

### Example

```text
n = 3
edges = [[0,1], [1,2]]
source = 0
destination = 2
```

Graph:

```text
0 ---- 1 ---- 2
```

Output:

```text
true
```

---

# 2. Main Idea

We can perform **DFS starting from `source`**.

During DFS:

1. If current node is `destination` → return `true`.
2. Mark current node as visited.
3. Traverse all its neighbours.
4. If any neighbour can reach destination → return `true`.
5. Otherwise → return `false`.

### Core Pattern

```text
Source
  ↓
DFS
  ↓
Visit neighbours
  ↓
Reach destination?
  ↓
YES → true
NO  → continue
```

---

# 3. Why Adjacency List?

Input is given as an edge list:

```text
edges = [[0,1], [1,2], [2,3]]
```

We convert it into an adjacency list:

```text
0 → 1
1 → 0, 2
2 → 1, 3
3 → 2
```

Because the graph is **undirected**, every edge is stored in both directions:

```cpp
graph[u].push_back(v);
graph[v].push_back(u);
```

---

# 4. Why `visited` is Necessary?

Graphs can contain **cycles**.

Example:

```text
0 ---- 1
|      |
|      |
3 ---- 2
```

Without `visited`, DFS could keep doing:

```text
0 → 1 → 2 → 3 → 0 → 1 → 2 → ...
```

So we maintain:

```cpp
vector<bool> visited(n, false);
```

Before exploring neighbours:

```cpp
visited[curr] = true;
```

---

# 5. DFS Function

```cpp
bool solve(int curr,
           int destination,
           vector<vector<int>>& graph,
           vector<bool>& visited)
```

### Parameters

| Parameter     | Meaning                      |
| ------------- | ---------------------------- |
| `curr`        | Current node                 |
| `destination` | Target node                  |
| `graph`       | Adjacency list               |
| `visited`     | Keeps track of visited nodes |

---

# 6. Base Case

```cpp
if(curr == destination){
    return true;
}
```

If DFS reaches the destination, a path exists.

---

# 7. Mark Current Node

```cpp
visited[curr] = true;
```

This prevents revisiting the same node.

---

# 8. Traverse Neighbours

```cpp
for(int neighbor : graph[curr]){
    
    if(!visited[neighbor]){
        
        if(solve(neighbor, destination, graph, visited)){
            return true;
        }
    }
}
```

For every neighbour:

* If it is unvisited
* Go there using DFS
* If that DFS finds destination → immediately return `true`

---

# 9. Why `return false`?

```cpp
return false;
```

If all possible neighbours have been explored and destination was never reached, then no path exists from this branch.

---

# 10. Complete Code

```cpp
class Solution {
public:

    bool solve(int curr,
               int destination,
               vector<vector<int>>& graph,
               vector<bool>& visited){

        if(curr == destination){
            return true;
        }

        visited[curr] = true;

        for(int neighbor : graph[curr]){

            if(!visited[neighbor]){

                if(solve(neighbor,
                         destination,
                         graph,
                         visited)){
                    return true;
                }
            }
        }

        return false;
    }

    bool validPath(int n,
                   vector<vector<int>>& edges,
                   int source,
                   int destination) {

        // Build adjacency list
        vector<vector<int>> graph(n);

        for(auto& edge : edges){
            graph[edge[0]].push_back(edge[1]);
            graph[edge[1]].push_back(edge[0]);
        }

        // Visited array
        vector<bool> visited(n, false);

        // DFS
        return solve(source,
                     destination,
                     graph,
                     visited);
    }
};
```

---

# 11. Dry Run

Consider:

```text
n = 6

edges =
[[0,1],
 [0,2],
 [1,3],
 [2,4],
 [3,5]]

source = 0
destination = 5
```

Graph:

```text
       1 ---- 3 ---- 5
      /
     0
      \
       2 ---- 4
```

DFS:

```text
solve(0)
    ↓
visited[0] = true

Neighbours = 1, 2

    ↓
solve(1)
    ↓
visited[1] = true

    ↓
solve(3)
    ↓
visited[3] = true

    ↓
solve(5)
    ↓
curr == destination

    ↓
true
```

So final answer:

```text
true
```

---

# 12. Most Important Concept

The most important line is:

```cpp
solve(neighbor, destination, graph, visited)
```

We don't call DFS again on `curr`.

❌ Wrong:

```cpp
solve(curr, destination, graph, visited);
```

✅ Correct:

```cpp
solve(neighbor, destination, graph, visited);
```

Because recursion must move to the **next node**.

---

# 13. Complexity

### Time

```text
O(V + E)
```

Every vertex and edge is explored at most once.

### Space

```text
O(V + E)
```

Because:

* Adjacency list → `O(V + E)`
* Visited array → `O(V)`
* Recursion stack → `O(V)` worst case

---

# 14. Pattern to Remember

### Graph DFS Template

```cpp
visited[curr] = true;

for(auto neighbor : graph[curr]){

    if(!visited[neighbor]){

        if(solve(neighbor)){
            return true;
        }
    }
}

return false;
```

### Mental Model

```text
Current Node
     ↓
Mark Visited
     ↓
Explore every neighbour
     ↓
Recursive DFS
     ↓
Destination found?
   ↙       ↘
 YES       NO
  ↓         ↓
true    explore next
```

---

# 15. Interview Takeaway ⭐

Whenever the question asks:

> **"Is there a path from A to B?"**

Think:

```text
Graph
  ↓
Adjacency List
  ↓
DFS / BFS
  ↓
Visited
  ↓
Reach destination?
```

This is a fundamental **Graph Traversal** pattern.

---

## 🔥 One-Line Revision

> **Build adjacency list → DFS from source → mark visited → recursively explore unvisited neighbours → destination found = true.**

**LeetCode:** 1971 — Find if Path Exists in Graph
**Pattern:** `Graph → Adjacency List → DFS → Visited → Reachability`
