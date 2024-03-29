
# DUKE UNIVERSITY ME555 / ECE551D -miniproject

Path Planning
===========================================================================

This mini-project option is writing a path planning program, such as an
autonomous mobile robot might use. There is a progression of steps that use
Dijkstra's algorithm and the A* algorithm.

There are four steps for this assignment (each worth 20 points of the
functitonality score):

Step 1
  Read in a graph representation from a file.
Step 2
  Use Dijkstra's algorithm to calculate the shortest path from a start to
  goal node.
Step 3
  Use Dijkstra's algorithm to calculate the shortest path when there are
  obstacles.
Step 4
  Use the A* algorithm to calculate the shortest path when there are
  obstacles.

Permitted domain knowledge resources (in addition to the other permitted
resources like AoP and cplusplus.com/reference):
  - Introduction to Autonomous Robots by Nikolaus Correll (especially
    chapter 4)
    https://github.com/correll/Introduction-to-Autonomous-Robots/releases/tag/v1.9.2
  - Wikipedia pages on Dijkstra's algorithm and A*
  - How to check if two lines intersect
    https://www.geeksforgeeks.org/check-if-two-given-line-segments-intersect/

Step 1
===========================================================================

This step involves designing your graph structure and reading in a
description of a grid map from a file to create a graph.

This step should create the program "path-step1", which takes one command
line argument, the name of the file to read the grid map from. A grid map
file has two sections, one for the nodes beginning with "$nodes" and one
for the edges beginning with "$edges".

Each node line has the format

nodeid x-coord y-coord

such as

2 1.0 0.0
8 1.0 0.4

meaning the first node's id is 2, its x-coordinate is 1.0, and its
y-coordinate is 0.0. Node 8 has the same x-coordinate and a different
y-coordinate. 

Each edge line has the format

node1 node2

such as

2 8

meaning the two nodes with id 2 and 8 are connected by an undirected
edge. It can be inferred that the weight of this edge is 0.4, since that is
the Euclidean distance between nodes 2 and 8 from their coordinate
information. 

Your program should read the grid map information from this file and store
it in a graph data structure. The file must have a $nodes section with zero
or more nodes and an $edges section with zero or more edges, each with the
specified format. The nodes may be listed in any order, but you may assume
the IDs start at 0, and there are no gaps in the numbering once all nodes
have been read. If there are any errors in the format of the input file,
you should give an appropriate error message and exit with a failure
status.

You are free to implement the graph structure however you like, but here
are some suggestions.

  - A Point class that implements distanceFrom.
  - A Graph class that implements addNode and addEdge and overloads
    operator<<.
  - The node type is unsigned integer.
  - The edge information is stored as an adjacency list with a vector of
    maps. The index of the vector is the node ID, and the value is a map of
    its adjacencies as pairs of adjacent node and weight of the edge.
  - Spatial information about the nodes is stored separately from the
    adjacency list as a vector of points, where the index of the vector is
    the node ID, and the value is its x- and y-coordinate information.

Once your program reads in the grid map description, it should print it out
in the following format:

points represented by nodes (x,y) in order of node ID, separated by a
space, on a single line
each edge, one per line, indicating start node and each of its adjacencies
in order of node ID

For example, the output might be:

(0,0) (0.5,0) (1,0) (0,0.5) (0.5,0.5) (1,0.5) 
0: 1,0.5 3,0.5 4,0.707107
1: 0,0.5 2,0.5 3,0.707107 4,0.5 5,0.707107
2: 1,0.5 4,0.707107 5,0.5
3: 0,0.5 1,0.707107 4,0.5
4: 0,0.707107 1,0.5 2,0.707107 3,0.5 5,0.5
5: 1,0.707107 2,0.5 4,0.5

We recommend writing your code in a re-usable way for later parts, i.e. you
should write as little code as possible in the source file with this step's
main.

Once you have thoroughly tested this step, add/commit/push before
continuing to the next step.

Step 2
===========================================================================

In this step, you will implement Dijkstra's algorithm to find the shortest
path.

This step should create the program "path-step2", which takes three command 
line arguments, the name of the file to read the grid map from, the start
node, and the goal node. As in Step 1, it should read in a description of
the grid map and store the information in a graph structure. Then, it
should implement Dijkstra's algorithm to find the shortest path from the
start node to the goal node, where the cost of the path is the sum of the
weights of the edges (recall that each weight is the Euclidean distance
between two nodes.) 

For each next best path you identify, the program should print the path and
its distance, such as

0 6 13 : 1.04031

meaning the best path from the start node to 13 is node 0 to 6 to 13 with
cost (distance) 1.04031. The algorithm is finished when the shortest path
to the goal node has been found or it can be determined that there is no
path. 

In order to be consistent with our implementation, if there are multiple
paths with the lowest cost, choose the one to the node with the smallest
node ID. If the start or goal node is not a node in the grid map, you
should print an appropriate error message and exit failure. If there is no
path from the start node to goal node, you should print "No path exists"
and exit with a failure status. 

For example, running this program with grid_map.txt, start node 0, and goal
node 23 would yield the best path:

0 1 2 9 16 23 : 2.92094

(It would also print the path at each step of the algorithm.) Note that
there are multiple best paths from 0 to 23, but this is the one our
algorithm chooses because it prioritizes lower node IDs.

Again, you are free to implement this in any way you prefer. We suggest:

  - A Path class that encapsulates the cost and ordered nodes for each
    path and has an overloaded operator<<.
  - A public method in Graph to find the shortest path from start to
    goal. This function might maintain a vector of best paths and a set of
    completed nodes.

You should write as little code as possible in the source file with this
step's main, writing most of it in your classes or other source files for
reusability.

Once you have thoroughly tested this step, make sure Step 1 still works
well and add/commit/push before continuing to the next step.

Step 3
===========================================================================

In this step, you will add the capability for your program to accomodate
obstacles.

This step should create the program "path-step3", which takes four command 
line arguments, the name of the file to read the grid map from, the name of
the file to read the obstacles from, the start node, and the goal node. It
should read the description of the grid map into a graph structure, read
the obstacles, remove those edges obstructed by the obstacle, and use
Dijkstra's algorithm to find the shortest path from the start node to the
goal node avoiding any obstacles.

The obstacle file has the header "$obstacles" followed by zero or more
obstacles, one per line. An obstacle is a list of two or more nodes that
form a line segment or polygon. For example, obstacle.txt contains one
obstacle connecting nodes 2, 14, and 8.

You can indicate the obstacle in your graph in any way you prefer. You can
remove edges that would impinge on the obstacle or update their weights to
represent "no edge exists." It is up to you whether you remove edges that
are inside the obstacle or not, and you may assume that the start and goal
node (if valid) are not on the interior of an obstacle.

For the example grid map and obstacle file, the best path would be:

0 6 13 20 21 22 23 : 3.18062

which is longer than the path without obstacles. From the unobstructed
path, edges 1-2, 2-9, and 9-16 are no longer viable.

A subproblem is checking if two line segments intersect. Here is one way to
do this:

Two line segments 1-2 and 3-4 intersect if
  - 1-2-3 and 1-2-4 have different orientations* and
  - 3-4-1 and 3-4-2 have different orientations

          o 4
         /|\
     1 o -+- o 2
         \|/ 
          o 
	  3

or they are collinear in the special case where 1-2-3, 1-2-4, 3-4-1, and
3-4-2 are all collinear and
  - the x-projections of 1-2 and 3-4 intersect and
  - the y-projections of 1-2 and 3-4 intersect

*Orientation of three points can be determined by calculating two slopes and
see if the second increases or decreases from the first. That is, for three
points (x1, y1), (x2, y2), and (x3, y3):

z = (y2 - y1)*(x3 - x2) - (x2 - x1)*(y3 - y2)

The points have orientation
  - clockwise if z > 0
  - counterclockwise if z < 0 and
  - collinear if z = 0

For this step, we suggest:

  - A member function of Graph that takes a vector of nodes representing an
    obstacle, which would
    o construct an edge for each pair of nodes,
    o iterate over all edges in the graph and for each edge, check if it
      intersects any of the obstacle's edges and remove the graph edge if
      so.
  - An Edge class that encapsulates two points and implements an intersect
    method
  - A helper function or member function of Point or Edge that determines
    the orientation of three points

You should write as little code as possible in the source file with this
step's main, writing most of it in your classes or other source files for
reusability.

Once you have thoroughly tested this step, make sure Steps 1 and 2 still
work well and add/commit/push before continuing to the next step.

Step 4
===========================================================================

In this step, you will implement the A* shortest path algorithm.

This step should create the program "path-step4", which takes one optional
and four required command line arguments. If the option "-a" is given, the
program should use the A* algorithm to find the shortest path. If not, the
behavior should use Dijkstra's algorith and be identical to the program in
step 3. The four required arguments are the name of the file to read the
grid map from, the name of the file to read the obstacles from, the start
node, and the goal node. This program should read the description of the
grid map into a graph structure, read the obstacles, remove those edges
obstructed by the obstacle, and use the A* algorithm to find the shortest
path from the start node to the goal node avoiding any obstacles.

You should read the description of A* (pronounced A-star) on Wikipedia and
in the Correll text. The basic idea is to maintain a notion of which
direction the goal is in and prioritize paths that go closer to the goal.

Specifically, like Dijkstra's algorithm, A* begins at the start node and at
each step gets the adjacencies from the current node, computes the new path
to each of them, and updates the best paths so far if a better path has
been found, then marks the current node as completed. Then, then next best
path to a node is selected, updating the current node. The difference is
that the value used to determine priority of a path to node N is the sum of
the cost of the start node to N plus the distance from N to the goal node,
for which we will use Euclidean distance in this assignment.

For the example grid map and obstacle file, the best path would be:

0 7 13 20 21 22 23 : 3.18062

Note that this path has the same cost as the one found with Dijkstra's but
is different because paths heading roughly toward the goal had a lower
total cost than others with the same cost as measured from start to N.

See if you can implement this algorithm with relatively small changes to
your existing shortest path code.

Once you have thoroughly tested this step, make sure Steps 1, 2, and 3
still work well and add/commit/push before running grade.

Review the overall README and make sure your TESTING.txt is polished and
you have considered all of the elements of code quality.
