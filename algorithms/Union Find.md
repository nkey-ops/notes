## Definitions.

*Sites:* objects.  
*Connections:* pairs.  
*Connected components / components:* equivalence classes.  
*Component identifier:* site that represents all the connected components with itself.  

*The size of a tree:* is its number of nodes.  
*The depth of a node in a tree:* is the number of links on the path from it to the root.  
*The height of a tree:* is the maximum depth among its nodes.   
*Percolation threshold:* is the critical value of occupation probability.  


# Dynamic Connectivity
The input is a sequence of pairs of integers, where each integer represents an object of some
type and we are to interpret the pair p q as meaning “p is connected to q.” 
We assume that “is connected to” is an equivalence relation, 
which means that it is

    ■ Reflexive :  p is connected to p.
    ■ Symmetric :  If p is connected to q, then q is connected to p.
    ■ Transitive : If p is connected to q and q is connected to r, 
                   then p is connected to r.

## Applications 
### Networks 
The are might be a computer network and we need to decide whether
we should open a connection for 2 computers if they aren't connected.

### Variable-name equivalence
In certain programming languages some variables can be declared as equivalent 
and after certain amount of such declaration a computer needs to decide
whether two given variables are equivalent or no.

### Mathematical sets
Determine whether two variables are a part of the same set.

    Union-find cost model.
    When studying algorithms to imple- ment the union-find API, we
    count array accesses (the num- ber of times an array entry is
    accessed, for read or write).

[[#Union-find implementation]]

# Quick Find
The idea behind Quick Find is to to link every site with 
its component identifier DIRECTLY.

    Find: O(1)
    Union: Ώ:(N + 3), Ό(2N + 1)

```java
public int find(int p) { 
    return id[p]; 
}

public void union(int p, int q) { 
    // Put p and q into the same component.
    int pID = find(p);
    int qID = find(q);
    // Nothing to do if p and q are already
    if (pID == qID) return;
    // Rename p’s component to q’s name.
    for (int i = 0; i < id.length; i++)
        if (id[i] == pID) id[i] = qID;
    count--;
}
```


## Proposition F 
The quick-find algorithm uses one array access for each call to
find() and between N + 3 and 2N + 1 array accesses for each call to union() that
combines two components.

### Proof 
Immediate from the code. 
Each call to connected() tests two entries in the
id[] array, one for each of the two calls to find(). 
Each call to union() that combines two components does so by making two calls to find(),
testing each of the N entries in the id[] array, and changing between 1 and N - 1 of them.


# Quick Union
Quick Union works as an improved version of quick find.
It's implemented by referencing one component identifier with another on in case
of union operation.
This way we'll get a chain of sites that lead to it's ultimate component 
identifier if we try to perform the find operation

    Find:  Ώ (1), O(n)
    Union: Ώ (2), O(2n + 1)

```java
private int find(int p) { 
  // Find component name.
  int v;
  while (p != (v = id[p])) p = v;

  return p;
}
public void union(int p, int q) { 
  // Give p and q the same root.
  int pRoot = find(p);
  int qRoot = find(q);
  if (pRoot == qRoot) return;
  id[pRoot] = qRoot;
  count--;
}
```

## Proposition G 
The number of array accesses used by find() in quick-union is 1
plus the twice the depth of the node corresponding to the given site.\
The number of array accesses used by union() and connected() is the cost of the two find()
operations (plus 1 for union() if the given sites are in different trees).


# Weighted Quick Union
It's and improvement of Quick Union that aims to reduce the maximal 
height of the tree of a components from N to log N 

    Find: O(log n )
    Union: O(2log n)


[[#Weighted Quick-Union Implementation]]

## Proposition H
The depth of any node in a forest built by weighted quick-union for 
N sites is at most lg N.

### Proof 
We prove a stronger fact by (strong) induction:  
The height of every tree of size k in the forest is at most lg k. 

The base case follows from the fact that the tree height is 0 when k is 1. 
By the inductive hypothesis, assume that the tree height of a
tree of size i is at most lg i for all i < k. 

When we combine a tree of size i with a tree of size j with i <= j and i + j = k, 
we increase the depth of each node in the smaller set by 1, 
but they are now in a tree of size i + j = k, 
so the property is preserved because
1 + lg i = lg(i + i ) <=  lg(i + j ) = lg k.


## Corollary 
For weighted quick-union with N sites, the worst-case order of growth
of the cost of find(), connected(), and union() is log N.

### Proof 
Each operation does at most a constant number of array accesses for each
node on the path from a node to a root in the forest.



| algorithm            |  constructor |  union      | find       |
| -------------------- | ------------ | ----------- | ---------- |
| quick-find           |      N       |     N       |   1        |  
| quick-union          |      N       | tree height | tree height|
| weighted quick-union |      N       | lg N        | lg N       |   
| weighted quick-union |      N       | very, very nearly, but   | 
  with path compresson                | not quite 1 (amortized ) |
| impossible           |      N       | 1           |   1        |


# Basic steps for solving fundamental problems

■  Decide on a complete and specific problem statement, including identifying
   fundamental abstract operations that are intrinsic to the problem and an API.

■  Carefully develop a succinct implementation for a straightforward algorithm,
   using a well-thought-out development client and realistic input data.

■  Know when an implementation could not possibly be used to solve problems on
   the scale contemplated and must be improved or abandoned.

■  Develop improved implementations through a process of stepwise refinement,
   validating the efficacy of ideas for improvement through empirical analysis,
   mathematical analysis, or both.

■  Find high-level abstract representations of data structures or algorithms in 
   operation that enable effective high-level design of improved versions.

■  Strive for worst-case performance guarantees when possible, but accept good
   performance on typical data when available.

■  Know when to leave further improvements for detailed in-depth study to skilled
   researchers and move on to the next problem.


# Union-find implementation
```java
public class UF {
  private int[] id;
  private int count;

  // access to component id (site indexed)
  // number of components
  public UF(int N) { // Initialize component id array.
    count = N;
    id = new int[N];
    for (int i = 0; i < N; i++) id[i] = i;
  }

  public int count() {
    return count;
  }

  public boolean connected(int p, int q) {
    return find(p) == find(q);
  }

  public int find(int p) {}

  public void union(int p, int q) {}

  // See page 222 (quick-find),page 224 (quick-union) and page 228 (weighted).
  public static void main(String[] args) { // Solve dynamic connectivity problem on StdIn.
    int N = StdIn.readInt();
   // Read number of sites.
    UF uf = new UF(N);
    // Initialize N components.
    while (!StdIn.isEmpty()) {
      int p = StdIn.readInt();
      int q = StdIn.readInt();
      // Read pair to connect.
      if (uf.connected(p, q)) continue; // Ignore if connected.
      uf.union(p, q);
      // Combine components and print connections
      StdOut.println(p + " " + q);
    }
    StdOut.println(uf.count() + " components");
  }
}
```


# Weighted Quick-Union Implementation
```java
public class WeightedQuickUnionUF {
  private int[] id;
  // parent link (site indexed)
  private int[] sz;
  // size of component for roots (site indexed)
  private int count;

  // number of components
  public WeightedQuickUnionUF(int N) {
    count = N;
    id = new int[N];
    for (int i = 0; i < N; i++) id[i] = i;
    sz = new int[N];
    for (int i = 0; i < N; i++) sz[i] = 1;
  }

  public int count() {
    return count;
  }

  public boolean connected(int p, int q) {
    return find(p) == find(q);
  }

  private int find(int p) { // Follow links to find a root.
    while (p != id[p]) p = id[p];
    return p;
  }

  public void union(int p, int q) {
    int i = find(p);
    int j = find(q);
    if (i == j) return;
    // Make smaller root point to larger one.
    if (sz[i] < sz[j]) { id[i] = j; sz[j] += sz[i]; } 
    else               { id[j] = i; sz[i] += sz[j]; }
    count--;
  }
}
```
