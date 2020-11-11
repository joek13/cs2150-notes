# Graphs
# Notes 11.11.2020 - notes

# Graph
- Graph is a series of nodes/vertices and edges (which connect two nodes)

## Real-world examples
- Google Maps/MapQuest road data
  - Every intersection is a node, and roads are edges between nodes
- Social network friend graph
- Internet pages
- Dependency graph / task graph
- Airline routes
- Prerequisite graphs for university courses

## Formal definition
- G = (V, E)
- V are vertices, E are edges
- Edges of form (v,w) where v,w elements of V
  - Ordered pair: directed graph or digraph
  - Unordered pair: undirected graph
- A weight or cost can be associated with each edge
  - Assigned by weight function W: E -> Real
- w is adjacent to v iff (v,w) in E
- path: sequence of vertices w1,w2, …, wn such that (wi, wi+1) in E for 1 <= i < n
  - (all nodes are adjacent, and we traverse in the correct direction)
- Length of a path: number of edges in a path
- Simple path: all vertices are distinct (don't visit the same node twice)

## How big are these graphs?
- Airline routes? 59,036 routes (edges) between 3,209 airports (vertices)


## How to weight a graph?
- Roadmap
  - distance between roads
  - Traffic on road
  - Time to travel
  - Tolls (monetary cost)
- Airline routes
  - Cost?
  - Distance?
  - Length of flight?

## More terminology
- Cycle:
  - Directed graph: path of length >= 1 such that w1 = wn
  - Undirected graph: same, except all edges are distinct
- Connected: there is a path from every vertex to every other vertex
- Loop: (v, v) in E
- Complete graph: there is an edge between every pair of vertices

## Digraph Terminology
- Directed acyclic graph: no cycles; often called a DAG
- Strongly connected: there is a path from every vertex to every other vertex
- Weakly connected: the underlying undirected graph is connected

## Graph representation
### Adjacency matrix
Matrix A such that:

`A[u][v]` is 0 if (u,v) not in E
  is the weight if (u,v) in E

Works with directed graphs
If |V| = v, then the matrix is v x v, so we need v^2 cells

### Adjacency list
- For each node:
  - Create linked list to every adjacent node
Stored in list of linked lists

Time complexity: Theta(v) (worst case, our vertex is connected to every other vertex)
Practical considerations: lots of dynamic allocation, lots of pointer dereferencing

## How does Google Maps store this?
- Their graph is way too big to fit in an adjacency matrix
- Think about this: for the row representing a single row, the adjacency matrix will store information about the millions of roads it doesn't connect to
- Adjacency list is way easier

# Algorithms on graphs
## Topological sort
- Given a DAG, construct an ordering of the vertices such that, if there is a path from v_i to v_j, v_j appears after v_i in the ordering
  - Results in a linear list of vertices
- indegree of v: number of edges (*,v) -- number of incoming edges

How to do topological sort?
- Look at nodes with indegree 0
- Remove all outgoing edges
- Print out the node / emit it / store in some datastructure
