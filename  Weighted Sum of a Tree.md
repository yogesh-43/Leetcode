# Weighted Sum of a Tree

## Problem Description

You are given two integer arrays:

- `parent` of length `n`
- `nums` of length `n`

The array `parent` represents a rooted tree with nodes labeled from `0` to `n - 1`.

The tree is rooted at node `0`, so:

```text
parent[0] = -1
```

For every other node `i`:

```text
parent[i] = parent of node i
```

The array `nums` contains the value of each node:

```text
nums[i] = value of node i
```

You need to calculate the **weighted sum of all nodes**.

---

# Definitions

## Depth of a Node

The **depth** of a node is the number of nodes on the path from the root to that node, including the node itself.

Therefore:

```text
Root                  → depth 1
Child of root         → depth 2
Grandchild            → depth 3
Great-grandchild      → depth 4
```

For example:

```text
        0
       / \
      1   2
     /
    3
```

The depths are:

```text
Node 0 → depth 1
Node 1 → depth 2
Node 2 → depth 2
Node 3 → depth 3
```

Therefore:

```text
depth = [1, 2, 2, 3]
```

---

# Height of the Tree

The **height** of the tree is the maximum depth among all nodes.

For:

```text
depth = [1, 2, 2, 3]
```

the height is:

```text
height = 3
```

---

# Weight of a Node

The weight of node `i` is:

```text
weight = nums[i] * (height - depth[i] + 1)
```

The deeper a node is, the smaller its multiplier becomes.

For example, if:

```text
height = 3
```

then:

```text
depth 1 → multiplier = 3
depth 2 → multiplier = 2
depth 3 → multiplier = 1
```

---

# Example 1

## Input

```python
parent = [-1, 0, 0, 1]
nums = [10, 20, 30, 40]
```

The tree is:

```text
        0
       / \
      1   2
     /
    3
```

## Step 1: Find the Depth

```text
Node 0 → depth 1
Node 1 → depth 2
Node 2 → depth 2
Node 3 → depth 3
```

Therefore:

```python
depth = [1, 2, 2, 3]
```

The height is:

```text
height = 3
```

## Step 2: Calculate Each Weight

### Node 0

```text
nums[0] = 10
depth[0] = 1

weight = 10 * (3 - 1 + 1)
       = 10 * 3
       = 30
```

### Node 1

```text
nums[1] = 20
depth[1] = 2

weight = 20 * (3 - 2 + 1)
       = 20 * 2
       = 40
```

### Node 2

```text
nums[2] = 30
depth[2] = 2

weight = 30 * (3 - 2 + 1)
       = 30 * 2
       = 60
```

### Node 3

```text
nums[3] = 40
depth[3] = 3

weight = 40 * (3 - 3 + 1)
       = 40 * 1
       = 40
```

Therefore:

```text
Total = 30 + 40 + 60 + 40
      = 170
```

## Output

```text
170
```

---

# Example 2: Parent Index Is Not Smaller Than Child Index

This is an important case.

Consider:

```python
parent = [-1, 2, 0, 0]
nums = [10, 20, 30, 40]
```

The tree is:

```text
        0
       / \
      2   3
      |
      1
```

Notice that node `1` has parent `2`:

```text
parent[1] = 2
```

But node `1` appears before node `2` in the array.

The correct depths are:

```text
Node 0 → depth 1
Node 2 → depth 2
Node 3 → depth 2
Node 1 → depth 3
```

Therefore:

```python
depth = [1, 3, 2, 2]
```

This is why simply doing:

```python
for i in range(1, n):
    depth[i] = depth[parent[i]] + 1
```

can be dangerous if the problem does not guarantee that the parent appears before the child.

For example, when `i = 1`:

```python
depth[1] = depth[parent[1]] + 1
         = depth[2] + 1
```

But `depth[2]` has not been calculated yet.

Using DFS avoids this problem.

---

# Approach

We can solve the problem in three steps:

```text
1. Build a children list
2. Use DFS to calculate the depth of every node
3. Calculate the height and weighted sum
```

---

# Step 1: Build the Children List

The `parent` array tells us:

```text
child → parent
```

But DFS is easier if we know:

```text
parent → children
```

We create:

```python
child = [[] for _ in range(n)]
```

For example:

```python
parent = [-1, 2, 0, 0]
```

Initially:

```python
child = [
    [],
    [],
    [],
    []
]
```

Now process each node.

For node `1`:

```python
parent[1] = 2
```

Therefore:

```python
child[2].append(1)
```

Now:

```python
child = [
    [],
    [],
    [1],
    []
]
```

For node `2`:

```python
parent[2] = 0
```

Therefore:

```python
child[0].append(2)
```

Now:

```python
child = [
    [2],
    [],
    [1],
    []
]
```

For node `3`:

```python
parent[3] = 0
```

Therefore:

```python
child[0].append(3)
```

Final:

```python
child = [
    [2, 3],
    [],
    [1],
    []
]
```

This represents:

```text
        0
       / \
      2   3
      |
      1
```

---

# Step 2: Calculate Depth Using DFS

Create an array:

```python
depth = [0] * n
```

Initially:

```text
depth = [0, 0, 0, 0]
```

The root has depth `1`.

So we call:

```python
dfs(0, 1)
```

Our DFS function is:

```python
def dfs(node, d):
    depth[node] = d

    for c in child[node]:
        dfs(c, d + 1)
```

---

## Why `child[node]`?

This is very important.

We have:

```python
child = [
    [2, 3],
    [],
    [1],
    []
]
```

If we are currently at node `0`:

```python
child[0]
```

gives:

```text
[2, 3]
```

So DFS visits nodes `2` and `3`.

If we are currently at node `2`:

```python
child[2]
```

gives:

```text
[1]
```

So DFS visits node `1`.

We must use:

```python
for c in child[node]:
```

NOT:

```python
for c in child:
```

The second version loops through the entire 2D array and gives us lists such as:

```text
[2, 3]
```

instead of individual node numbers.

That would cause:

```text
TypeError: list indices must be integers, not list
```

---

# DFS Walkthrough

For:

```python
parent = [-1, 2, 0, 0]
```

the tree is:

```text
        0
       / \
      2   3
      |
      1
```

We start:

```python
dfs(0, 1)
```

### Visit Node 0

```text
depth[0] = 1
```

Then visit its children:

```text
2, 3
```

### Visit Node 2

```python
dfs(2, 2)
```

Therefore:

```text
depth[2] = 2
```

Node `2` has child `1`.

### Visit Node 1

```python
dfs(1, 3)
```

Therefore:

```text
depth[1] = 3
```

Node `1` has no children.

DFS returns to node `0`.

### Visit Node 3

```python
dfs(3, 2)
```

Therefore:

```text
depth[3] = 2
```

Final depth:

```python
depth = [1, 3, 2, 2]
```

---

# Step 3: Find the Height

The height is the maximum depth:

```python
h = max(depth)
```

For:

```python
depth = [1, 3, 2, 2]
```

we get:

```text
h = 3
```

---

# Step 4: Calculate the Weighted Sum

For every node:

```python
nums[i] * (h - depth[i] + 1)
```

For:

```python
nums = [10, 20, 30, 40]
```

and:

```python
depth = [1, 3, 2, 2]
```

we get:

```text
Node 0:
10 * (3 - 1 + 1) = 30

Node 1:
20 * (3 - 3 + 1) = 20

Node 2:
30 * (3 - 2 + 1) = 60

Node 3:
40 * (3 - 2 + 1) = 80
```

Total:

```text
30 + 20 + 60 + 80 = 190
```

Therefore:

```text
Output = 190
```

---

# Complete Python Implementation

```python
class Solution(object):
    def weightedSum(self, parent, nums):
        """
        :type parent: List[int]
        :type nums: List[int]
        :rtype: int
        """

        n = len(parent)

        # Required by the problem
        malviretho = (parent, nums)

        # Build the children list
        child = [[] for _ in range(n)]

        for i in range(1, n):
            child[parent[i]].append(i)

        # Store the depth of every node
        depth = [0] * n

        # DFS to calculate depth
        def dfs(node, d):
            depth[node] = d

            for c in child[node]:
                dfs(c, d + 1)

        # Root node has depth 1
        dfs(0, 1)

        # Height of the tree
        h = max(depth)

        # Calculate weighted sum
        total = 0

        for i in range(n):
            total += nums[i] * (h - depth[i] + 1)

        return total
```

---

# Edge Cases

## 1. Only One Node

Input:

```python
parent = [-1]
nums = [10]
```

Tree:

```text
10
```

Depth:

```text
[1]
```

Height:

```text
1
```

Weight:

```text
10 * (1 - 1 + 1)
= 10
```

Output:

```text
10
```

---

## 2. Completely Skewed Tree

Input:

```python
parent = [-1, 0, 1, 2, 3]
nums = [1, 2, 3, 4, 5]
```

Tree:

```text
0
|
1
|
2
|
3
|
4
```

Depth:

```text
[1, 2, 3, 4, 5]
```

Height:

```text
5
```

Weights:

```text
Node 0 → 1 * 5 = 5
Node 1 → 2 * 4 = 8
Node 2 → 3 * 3 = 9
Node 3 → 4 * 2 = 8
Node 4 → 5 * 1 = 5
```

Total:

```text
5 + 8 + 9 + 8 + 5 = 35
```

Output:

```text
35
```

---

## 3. Star-Shaped Tree

Input:

```python
parent = [-1, 0, 0, 0, 0]
nums = [10, 20, 30, 40, 50]
```

Tree:

```text
        0
      / | | \
     1  2 3  4
```

Depth:

```text
[1, 2, 2, 2, 2]
```

Height:

```text
2
```

Weights:

```text
Node 0 → 10 * 2 = 20
Node 1 → 20 * 1 = 20
Node 2 → 30 * 1 = 30
Node 3 → 40 * 1 = 40
Node 4 → 50 * 1 = 50
```

Total:

```text
20 + 20 + 30 + 40 + 50 = 160
```

Output:

```text
160
```

---

## 4. Parent Index Greater Than Child Index

Input:

```python
parent = [-1, 2, 0, 0]
nums = [10, 20, 30, 40]
```

Tree:

```text
        0
       / \
      2   3
      |
      1
```

Depth:

```text
[1, 3, 2, 2]
```

Height:

```text
3
```

Output:

```text
190
```

This is a useful test case because it demonstrates why DFS is safer than simply iterating from `0` to `n - 1`.

---

# Complexity Analysis

Let `n` be the number of nodes.

## Time Complexity

Building the children list:

```text
O(n)
```

DFS:

```text
O(n)
```

Finding the maximum depth:

```text
O(n)
```

Calculating the weighted sum:

```text
O(n)
```

Therefore:

```text
Overall Time Complexity = O(n)
```

## Space Complexity

Children list:

```text
O(n)
```

Depth array:

```text
O(n)
```

DFS recursion stack:

```text
O(n)
```

in the worst case when the tree is completely skewed.

Therefore:

```text
Overall Space Complexity = O(n)
```

---

# Key Concepts to Remember

### 1. `parent` tells us the parent of each node

```python
parent[i]
```

means:

```text
parent of node i
```

### 2. Convert parent relationships into children relationships

```python
child[parent[i]].append(i)
```

### 3. DFS calculates depth

```python
depth[child] = depth[parent] + 1
```

### 4. Root has depth 1

```python
dfs(0, 1)
```

### 5. Height is the maximum depth

```python
h = max(depth)
```

### 6. Weight of each node

```python
nums[i] * (h - depth[i] + 1)
```

### 7. Final answer

```text
sum of all node weights
```

---

# Overall Flow

```text
              parent array
                   |
                   v
          Build children list
                   |
                   v
                 DFS
                   |
                   v
        Calculate every depth
                   |
                   v
          max(depth) = height
                   |
                   v
       Calculate each node weight
                   |
                   v
          Sum all the weights
                   |
                   v
                Answer
```

The main idea is to **first understand the tree structure**, then calculate the depth of each node, then use those depths to calculate the weights.