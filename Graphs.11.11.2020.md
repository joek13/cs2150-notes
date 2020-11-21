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
- Look for a node with indegree 0
- Remove all outgoing edges from that node
- Print out the node / emit it / store in some datastructure

Many graphs have multiple valid topological sorts

Remember, DAG's A = **acyclic**, so we can't have cycles

Naive solution is `Theta(v^2)` (*v* is number of vertices)
  - For every vertex:
    - Find vertex with indegree zero
    - Decrement indegree of all of its neighbors

Observation: after the loop runs, all the nodes with indegree zero will have been neighbors with *v*
Optimization: maintain a queue of nodes with indegree zero
- First time around, we have to populate it by scanning every node
- After that, we can just check if `indegree == 0` when we decrement neighbors' indegrees
  - If so, enqueue it

*So what?* Worst-case runtime is still `Theta(v^2)`

Another optimization: the loop that decrements the indegree of neighbor nodes runs, in total, exactly *e* times

## Shortest Path Algorithms
### Applications
- Map routing
- '6 degrees of separation'
- Internet packet routing
- Puzzle answers (Rubik's cube)

### Three types of shortest-path
- Single-pair : you have (source, destination), and you want to get from (source, destination) as fast as possible
- Single-source : from one particular node, what's the shortest path to every other node in the graph?
- All-pairs : what's the shortest path from every node to every other node?
- Travelling salesperson : coming soon

### Single-source shortest path
Given a graph G = (V,E) and a single distinguished vertex s, find the shortest weighted path from s to every other vertex in G

#### Special case: unweighted shortest path
Special case of the weighted problem where all weights are 1
Solution: breadth-first search
(Useful for Internet routing, where we care more about the number of hops than the length of wire between them)

**Breadth-first search:**
- s->dist = 0
- Enqueue `s`
- While queue isn't empty:
  - Dequeue node
  - For each neighbor n of node:
    - If n->dist == INFINITY
      - n->backlink = node
      - n->dist = node->dist + 1;
      - enqueue(n)

Adapting single-source to single-pair:
  Run the single-source algorithm, just follow the backlinks from your destination node

#### Weighted shortest path
- Assuming right now: no negative weight edges
- Group of set algorithms: maintain two sets, "known" and "unknown"
- "Known" is the set of vertices we currently know the shortest path to
- "Unknown" is the set of vertices we currently do not know the shortest path to

#### Dijkstra's algorithm
- Initialize each vertex's distance as infinity
- Start at a given vertex s
  - Update s's distance to be zero
- Repeat:
  - Pick the next unknown vertex with the shortest distance to be the next v
    - If no more vertices are unknown, terminate loop
  - Mark v as known
  - For each edge from v to adjacent unknown vertices w
    - If the total distance to w is less than the current distance to w
      - Update w's distance and the path to w

##### Runtime analysis of Dijkstra's algorithm
Assume the graph is connected
- Runs v-times to perform a search
- Runs v times in total
- Worst-case Runtime: `Theta(v^2)`

- Optimizations we can do:
  - Use adjacency lists and heaps
  - Running time decreases to `Theta(e + v logv)`
    - Which simplifies to `Theta(elogv)

### Negative-cost edges
- Not always sensible. But sometimes it is
- What about negative-cost cycles??
  - Do something over-and-over to keep subtracting cost

### More on shortest path
- How do we adapt single-source to shortest-path?
  - Just break the loop once we see the destination enter the known set

### More on map routing
- How do we route a trip to Seattle?
  - Don't worry about side roads (at least, at first)
  - Maps represent 3 "types" of roads
    - Side roads (maybe just within your ZIP code)
    - Main roads (maybe just within your state)
    - Highway (maybe just within your continent)
  - Break the problem up
    - Assume you start on a "side road"
    - Transition to a "main road"
    - Transition to a "highway"
    - Get as close to your destination as possible using the highway system
    - Transition to a main road and get as close as possible to destination
    - Transition to a side road and get to your destination
  - Each of these subgraphs are way more tractable
- What about Waze?
  - Estimates speed based on crowdsourced data
  - "Re-runs" algorithm at will to update

### Travelling Salesperson Problem
- Given a number of cities and the costs of travelling from any city to any other city, what is the least-cost simple cycle that visits every city?
- Really important problem for
  - UPS, Federal Express, USPS
  - Any transport delivery system
  - Cost = distance because more fuel is used
- Assume graph is fully connected

- Naive solution: `Theta((n-1)!)`

#### Analysis
- Hamiltonian path: a path in a connected graph that visits every vertex exactly one
  - Hamiltonian cycle: a Hamiltonian path that ends where it started (one that is also a cycle)
- The traveling salesperson problem is thus to find the least-weight Hamiltonian path in a connected, weighted graph
- The size of the solution space is `Theta((n-1)! / 2)`
  - Because reversing a cycle is exactly identical in terms of cost
  - So half of the solution space is redundant
  - But this doesn't affect the running time

- TSP is known *NP-Complete*
- No known efficient solution beyond trying every possible
- But there are ways to get a somewhat efficient solution (a *heuristic*)
  - It might just not be the *most* efficient path
- What's usually the least expensive way to get between two US cities?
  - And is that significantly slower than the *best* algorithm?


#### Minimum spanning tree (MST)
**Note:** we can view trees as a kind of graph without cycles

- Suppose you have some network - transport system
  - Set of cities alongside roads, rail lines, air corridors connecting them
- Say you start from somewhere and want to get paths to everywhere else
  - Create a tree that spans the entire graph

A *spanning tree* of a graph *G* is a subgraph of *G* that contains every vertex of *G* and is a tree
- Any connected graph has a spanning tree
- Any two spanning trees of a graph have the same number of nodes
- Construct a spanning tree
  - Start with the graph
  - Remove an edge from each cycle
  - What remains has the same set of vertices but is a tree

#### Minimal spanning tree
- Spanning trees are simple
- But what if the edges have weights?
- Each tree has a different total weight
- Minimal-weight spanning tree: spanning tree with the minimal total weight

Formally:
- Given a connected and undirected graph G = (V, E), find a graph G' = (V, E') such that:
  - E' is a subset of E
  - |E'| = |V| - 1
  - G' is connected
  - Sum of weights along edges in E' is minimal

#### Generic minimal spanning tree album
##### Prim's algorithm
- Idea: grow a tree by adding an edge to the "known" vertices from the "unknown" vertices. Pick the edge with the smallest weight.

- Pick one node as the root.
- Incrementally add edges that connect a "new" vertex to the tree
- Pick the edge (u, v) where:
  - u is in the tree, v is not, AND
  - Where the edge weight is the smallest of all edges (for whom u is in the tree and v is not)

#### Analysis
Runtime is same as Dijkstra's: `Theta(e logv)`
- Correctness:
  - Suppose we have a partially built tree that we know is contained in some minimum spanning tree T
  - Let (u,v) in E, where u is known and v is unknown and has minimal cost
  - Unioning in (u,v) will always find the smallest weight

### Kruskal's MST Algorithm
- Look at every single edge on its own
  - Pick the edge of minimum weight that doesn't connect two already-unconnected nodes
    - Optimization: we can check this by "coloring" subgraphs
  - Add that edge to our "forest", merging two trees together

Runtime, when optimized: `Theta(e logv)`
