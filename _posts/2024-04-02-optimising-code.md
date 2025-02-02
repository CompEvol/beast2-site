---
layout: post
title: Optimising code -- the case of CCD0
tags: []
---
<p style="color:gray">2 April 2024 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Here, we look at some techniques to speed up Java code, with the calculation of CCD0 as a working example.

## Background

A posterior tree set -- like a tree posterior produced by BEAST -- can be represented by a conditional clade distribution (Hoehna & Drummond, 2012, Larget, 2013).
Conditional clade distributions can be parameterised in the classic way (known as CCD1) where every clade C observed in a tree set is split into clades C1 and C2 as observed in trees containing C.

CCD0 is an alternaive parameterisation (Berling et al, 2024) where every clade C observed in the tree set is split into clades C1 and C2 as observed in the whole tree set. 
So C1 and C2 may not be necessarily observed in the same tree, unlike CCD1, but if there are trees containing C1 and other trees containing C2 and the union of C1 and C2 form C then it will be taken in account for CCD0.
This leaves a computational problem: given a set of clades observed in a tree set, find all splits.
A naive implementation would check for every clade C and for every pair C1,C2 whether C1 and C2 are disjoint and C is their union, which is O(n^3) for n clades, making it too inefficient for the 76392 clades in our example tree set.



## Pre-computing candidates

By grouping clades into bins of the same clade size, we can reduce the set of clades being considered: for a given clade C of size k, we only need to consider clades C1 of size k/2 to k.
Since C2=C/C1, we can calculate C2 and since C is of size k and C1 of size at least k/2 clade C2 is of size at most k/2, so all combinations of smaller clades (if viable) will be considered as well.
What is left is to see whether C2 exists at all in the tree set, and if so, update the list of clade splits for clade C.

We used a clade set with 76392 clades, and this reduced calculation time from 80 minutes to 5 minutes.


## Finding low hanging fruit with a profiler

Running a profiler showed that `BitSet`s used to represent clades were using most of the calculation time checking whether one set contains another, so that was an immediate target for optimisation.
Inspection of the code showed it was using this code:

```
	BitSet copy = (BitSet) container.clone();
	copy.and(contained);
	copy.xor(contained);
	return copy.isEmpty();
```

which can be condensed to 

```
	BitSet copy = (BitSet) container.clone();
	copy.and(contained);
	return copy.equals(contained);
```

with a savings of 10% in calculation time.


## Exploiting structure of the problem

Note that when a C clade is monophyletic (that is, is present in all trees in the tree set), any sub-clade C1 within C will never be able to combine with another clade C2 to form a new clade C' that has taxa outside C since C' will never be observed (otherwise C would not be monophyletic).
We can exploit this property by removing all sub-clades of monophyletic clades from the set of candidates (i.e. from the bins) once they have been processed.
Depending on how many monophyletic clades are present in a tree set and how big they are, this can result in large computational savings (50% for our example).


## Parallelisation

Finding all splits of observed clades C is [embarrassingly parallelisable](https://en.wikipedia.org/wiki/Embarrassingly_parallel).
Therefore, running the method with multiple threads can be expected to increase efficiency.
Care must be taken not to have multiple threads update data structures at the same time, so there is some coordination required making the speed-up with n threads somewhat less than n times.
Still it reduces calculation time with 10 threads by a factor of just under 5 on a 2021 Apple M1 Pro CPU with 10 cores (8 performance and 2 efficiency).


## Unrolling loops & removing boundary checks

As mentioned above, the most used data structure in CCD0 is the `java.util.BitSet` used to represent which taxa are part of a clade.
The `BitSet` uses an array of `long`s representing the individual bits, as well as an index to indicate the length of the array of `long`s in use.
The individual methods of `BitSet`, like doing an `and` or `or` operation require looping o
ver the array.
Furthermore, care must be taken to deal with bit-sets of different size.

Since we know all bit-sets in CCD0 are of the same fixes size (namely of the size of number taxa in the tree), we can use this to prevent boundary checks.
Furthermore, instead of using arrays, for small trees, the `java.util.BitSet` is replaced by a custom bit-set with the appropriate number of `long`s replacing the array.
For trees up to 64 taxa, we use `BitSet64` with just one `long` member. For up to 128 taxa we use `BitSet128` with two `long` members, up to 192 taxa `BitSet192` with three `long`s and up to 256 taxa `BitSet256` with four `long` members.
All operations are done on the individual members, and pre- or post-condition checks are skipped.
This results in a reduction from 33 to 18 seconds for our data set, almost 2x speed-up.


## Conclusion

In total, reduction in calculation time from 80 minutes to 18 seconds for a tree set with about 70 thousand clades makes that CCD0 now is not only a more accurate but als a very practical alternative to MCC for finding summary trees (Berling et al, 2024 for details).

Update: after being confronted with tree sets of over 3.5 million clades, some more algorithmic optimisations were done, reducing calculation time to below 3 seconds for this dataset 70k clade dataset. 
The new code is available in v1.0.2 of the [CCD](https://github.com/CompEvol/CCD) package.

## References

L Berling, J Klawitter, R Bouckaert, D Xie, A Gavryushkin, A Drummond
A tractable tree distribution parameterized by clade probabilities and its application to Bayesian phylogenetic point estimation
[bioRxiv, 2024.02. 20.581316](https://www.biorxiv.org/content/10.1101/2024.02.20.581316.abstract)

Sebastian Hoehna and Alexei J. Drummond. Guided tree topology proposals for
Bayesian phylogenetic inference. Systematic Biology, 61(1):1–11, 2012. doi:10.595
1093/sysbio/syr074.

Bret Larget. The estimation of tree posterior probabilities using conditional clade
probability distributions. Systematic Biology, 62(4):501–511, 2013. doi:10.1093/
sysbio/syt014.

