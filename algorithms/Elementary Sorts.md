# Definitions:
    An inversion is a pair of entries that are out of order in the array

# Algorithms types

    In place
    Not in place

# Selection Sort
Uses outer loop to pass through all the values in the list
On each pass uses inner loop (which starts from the place where 
    the outer loop has stopped)
    to find the biggest or smallest value
Upon finding it replaces the last value of the outer loop with this one 

    Time: O N * N - 1
    Space: O (1)

    Exchanges: N 

## Proposition A. 
Selection sort uses ~N^2 / 2 compares and N exchanges to sort an
array of length N.

## Proof: 
You can prove this fact by examining the trace, which is an N-by-N table
in which unshaded letters correspond to compares. About one-half of the entries
in the table are unshaded—those on and above the diagonal. The entries on the
diagonal each correspond to an exchange. 

More precisely, examination of the code reveals that, 
for each i from 0 to N - 1, there is one exchange and N - 1 - i compares, 
so the totals are N exchanges and 
(N - 1) + (N - 2) + . . . + 2 + 1+ 0 = N(N- 1) / 2 ~ N 2 / 2 compares.


```
public class Selection {
    public static void sort(Comparable[] a) { // Sort a[] into increasing order.
        int N = a.length;
        // array length
        for (int i = 0; i < N; i++) { // Exchange a[i] with smallest entry in a[i+1...N).
            int min = i;
            // index of minimal entr.
            for (int j = i + 1; j < N; j++)
                if (less(a[j], a[min]))
                    min = j;
            exch(a, i, min);
        }
    }
    // See page 245 for less(), exch(), isSorted(), and main().
}
```


# Insertion Sort
Uses outer loop to iterate through the whole array.\
On each pass it will compare the new item with the previous ones 
    until it finds the place where.
A new item will be after a smaller one or before a bigger one(in case of
ascending order)
Places it there and the shifts all the items after it to the right one time. 

    Time: O(N^2 /2) Best: N
    Space O(1)

    Exchanges: O(N^2 / 4) 
    Best: 0 

## Notes 
Fast on sorted or nearly sorted arrays or small.

## Proposition B. 
Insertion sort uses ~ N^2 / 4 compares and ~N^2 / 4 exchanges to sort
a randomly ordered array of length N with distinct keys, on the average.\
The worst case is ~ N^2 / 2 compares and ~ N^2 / 4 exchanges and the best case is N - 1 compares
and 0 exchanges.
## Proof: 
Just as for Proposition A, the number of compares and exchanges is easy to
visualize in the N-by-N diagram that we use to illustrate the sort. 
We count entries below the diagonal—all of them, in the worst case, 
and none of them, in the best case. 
For randomly ordered arrays, we expect each item to go about halfway back,
on the average, so we count one-half of the entries below the diagonal.

The number of compares is the number of exchanges plus an additional term
equal to N minus the number of times the item inserted is the smallest so far.
In the worst case (array in reverse order), 
this term is negligible in relation to the total; 
in the best case (array in order) it is equal to N - 1.

    public class Insertion {
        public static void sort(Comparable[] a) { // Sort a[] into increasing order.
            int N = a.length;
            for (int i = 1; i < N; i++) { // Insert a[i] among a[i-1], a[i-2], a[i-3]... ..
                for (int j = i; j > 0 && less(a[j], a[j - 1]); j--)
                    exch(a, j, j - 1);
            }
        }
        // See page 245 for less(), exch(), isSorted(), and main().
    }



## Notes 
Fast on sorted or nearly sorted arrays and tiny ones;

    ■ An array where each entry is not far from its final position
    ■ A small array appended to a large sorted array
    ■ An array with only a few entries that are not in place

## Proposition C. 
The number of exchanges used by insertion sort is equal to the
number of inversions in the array, and the number of compares is at least equal to
the number of inversions and at most equal to the number of inversions plus the
array size minus 1.

## Proof: 
Every exchange involves two inverted adjacent entries and thus reduces the
number of inversions by one, and the array is sorted when the number of inver-
sions reaches zero. Every exchange corresponds to a compare, and an additional
compare might happen for each value of i from 1 to N-1 (when a[i] does not
reach the left end of the array).



# Shellsort
An improved version of insertion sort
Loops through the array similar to Insertion Sort's way,
but instead of piking each item only every h-th item is picked.
When the end is reached it starts picking from the second item
until it makes h of this loops.

After all those iterations a new h is determent based on a certain function. 
A new h will be smaller by k times.
A new cycle will be started.
This procedure is repeated until h is 1 


The general idea is to fix Insertion sort's problem 
where item is moved to its adjacent "cell" but instead 
move it h cells in a certain direction.
In such cases when the smallest item is at the end will be solved much faster

    Time: Average: N^(3/2) 
    Space O(1)


    public class Shell {
        public static void sort(Comparable[] a) { // Sort a[] into increasing order.
            int N = a.length;
            int h = 1;
            while (h < N / 3)
                h = 3 * h + 1; // 1, 4, 13, 40, 121, 364, 1093, ...
            while (h >= 1) { // h-sort the array.
                for (int i = h; i < N; i++) { // Insert a[i] among a[i-h], a[i-2*h], a[i-3*h]... .
                    for (int j = i; j >= h && less(a[j], a[j - h]); j -= h)
                        exch(a, j, j - h);
                }
                h = h / 3;
            }
        }
        // See page 245 for less(), exch(), isSorted(), and main().
    }


