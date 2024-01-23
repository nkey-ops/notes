# Top-down Merge Sort
## Proposition F. 
Top-down mergesort uses between ½ N lg N and N lg N compares to
sort any array of length N.

## Proof: 
Let C(N) be the number of compares needed to sort an array of length N.
We have C(0) = C(1) = 0 and for N > 0 we can write a recurrence relationship that
directly mirrors the recursive sort() method to establish an upper bound:
C(N ) = C(⎣N/2⎦) + C(⎡N/2⎤) + N

The first term on the right is the number of compares to sort the left half of
the array, the second term is the number of compares to sort the right half, 
and the third term is the number of compares for the merge. 
The lower bound C(N ) >=  C(⎣N/2⎦) + C(⎡N/2⎤) + ⎣N/2⎦

follows because the number of compares for the merge is at least ⎣N/2⎦.
We derive an exact solution to the recurrence when equality holds and N is a
power of 2 (say N = 2n). 
First, since ⎣N/2⎦ = ⎡N/2⎤ = 2^(n - 1), we have

    C(2n ) = 2C(2^(n - 1))  + 2^n

Dividing both sides by 2n gives

    C(2^n )/2^n = C(2^(n-1)) / 2^(n-1) + 1

Applying the same equation to the first term on the right, we have

    C(2^n )/2^n = C(2^(n-2))/2^(n-2) + 1 + 1

Repeating the previous step n - 1 additional times gives

    C(2^n )/2^n =C(2^0)/2^0 + n

which, after multiplying both sides by 2n, leaves us with the solution

    C(N ) = C(2n ) = n 2n = N lg N

Exact solutions for general N are more complicated, 
but it is not difficult to apply the same argument to the inequalities 
describing the bounds on the number of compares 
to prove the stated result for all values of N. 
This proof is valid no matter what the input values are 
and no matter in what order they appear.

## Proposition G. 
Top-down mergesort uses at most 6N lg N array accesses to sort an
array of length N.

## Proof: 
Each merge uses at most 6N array accesses (2N for the copy, 2N for the
move back, and at most 2N for compares). 
The result follows from the same argument as for Proposition F.


## Mergesort Improvements:
### Use insertion sort for small subbarrays - 10 or 15%
### Test whether the array is already in order
    if two halfs are in order we will skip the merge

## Proposition H. 
Bottom-up mergesort uses between ½ N lg N and N lg N compares
and at most 6N lg N array accesses to sort an array of length N.

## Proof:
The number of passes through the array is precisely ⎣lg N⎦ (that is precisely
the value of n such that 2n  N < 2n1). For each pass, the number of array accesses
is exactly 6N and the number of compares is at most N and no less than N/ 2.


## Proposition I. 
No compare-based sorting algorithm can guarantee to sort N items
with fewer than lg(N !) ~ N lg N compares.

## Proof: 
First, we assume that the keys are all distinct, since any algorithm must be
able to sort such inputs. 
Now, we use a binary tree to describe the sequence of com-
pares. Each node in the tree is either a leaf i0 i1 i2 ... iN-1 that indicates that the
sort is complete and has discovered that the original inputs were in the order
a[i0], a[i1], ...a[iN-1], or an internal node i:j that corresponds to a com-
pare operation between a[i] and a[j], with a left subtree corresponding to the
sequence of compares in the case that a[i] is less than a[j], and a right subtree
corresponding to what happens if a[i] is greater than a[j]. Each path from the
root to a leaf corresponds to the sequence of compares that the algorithm uses to
establish the ordering given in the leaf. For example, here is a compare tree for


We never explicitly construct such a tree—it is a mathematical device for describ-
ing the compares used by any algorithm.

The first key observation in the proof is that the tree must have at least N ! leaves
because there are N ! different permutations of N distinct keys. If there are fewer
than N ! leaves, then some permutation is missing from the leaves, and the algo-
rithm would fail for that permutation.

The number of internal nodes on a path from the root to a leaf in the tree is the
number of compares used by the algorithm for some input.
We are interested in the length of the longest such path in the tree 
(known as the tree height) since it measures the worst-case 
number of compares used by the algorithm. 
Now, it is a basic combinatorial property of binary trees that 
a tree of height h has no more than 2h leaves—the tree of height h 
with the maximum number of leaves is perfectly balanced, or complete. 
An example for h = 4 is diagrammed on the next page.

Combining the previous two paragraphs, we have shown that any compare-based
sorting algorithm corresponds to a compare tree of height h with

    N! <= number of leaves <= 2h 

The value of h is precisely the worst-case number of compares, so we can take the
logarithm (base 2) of both sides of this equation and conclude that the number of
compares used by any algorithm must be at least lg N !.
The approximation lg N! ~ N lg N follows immediately 
from Stirling’s approximation to the factorial function.

## Proposition J. 
Mergesort is an asymptotically optimal compare-based sorting
algorithm.

## Proof: 
Precisely, we mean by this statement that both the number of compares used
by mergesort in the worst case and the minimum number of compares that any com-
pare-based sorting algorithm can guarantee are ~N lg N. Propositions H and I es-
tablish these facts.


# Notes
    ■ Mergesort is not optimal with respect to space usage.
    ■ The worst case may not be likely in practice.
    ■ Operations other than compares (such as array accesses) may be important.
    ■ One can sort certain data without using any compares.
