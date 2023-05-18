#input 

## Search Problems
Consists of:
1. Initial state
2. Actions
3. Transition Model
4. Goal test
5. Path cost function

### Goal
Find the **Optimal Solution**

## Node
>Data structure

Keeps track of:
1. A state
2. A parent (A node that generates this node)
3. An action (action applied to parent to get this node)
4. A path cost (from initial state to node)

## Approach
Start with a frontier that contains the initial state
Repeat:
	If the frontier is empty, then no solution
	Remove a node from the frontier
	If node contains goal state, return the solution
	Expand node, add resulting nodes the frontier

### Revised Approach
Start with a frontier that contains the initial state
Start with an empty explored set
Repeat:
	If the frontier is empty, then no solution
	Remove a node from the frontier
	If node contains goal state, return the solution
	Expand node, add resulting nodes to the frontier if they aren't already in the frontier or the explored set.

>When we use stack as the frontier, it will be DFS.
>When we use queue as the frontier, it will be BFS.

### DFS
NEED Luck to find the Optimal Solution

### BFS
Must find the Optimal Solution but it will find more states




