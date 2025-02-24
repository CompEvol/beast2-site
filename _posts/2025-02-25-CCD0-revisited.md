---
layout: post
title: CCD0 revisited
tags: []
---
<p style="color:gray">1 March 2025 by 
<a href="mailto:berlinglars96@gmail.com">Lars Berling</a>, 
<a href="mailto:tobia.ochsner@hotmail.com">Tobia Ochsner</a>, 
<a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a>, 
and <a href="mailto:a.drummond@auckland.ac.nz">Alexei Drummond</a></p>

The high dimensionality and non-Euclidean nature of treespace complicates summarizing the central tendency and variance of the posterior distribution in treespace.
Typically, we use the sampled trees from an MCMC run as this distribution. We call this a sample distribution.
However, since the space of trees increases super-exponentially with the number of taxa, a sample of several thousand trees typically misses the majority of trees with non-negligible posterior probability even for moderate size problems.

Because of this, we are in need of a better way of estimating the true probabilities of both the sampled and unsampled trees and to find a tree with highest probability.

### CCDs

To get better estimates of tree probabilities and the underlying posterior distribution we utilize conditional clade distributions (CCDs).
These are distributions parameterised by observed clades or clade splits in a sample of trees from MCMC and offer excellent estimates of the true posterior distribution.

 - CCD0: parameterised by observed clade frequencies

 - CCD1: parameterised by observed clade split frequencies

 - CCD2: parameterised by observed clade split frequencies conditioned on the parent split


## What makes a distribution tractable

We consider a probability distribution over a set of trees (on the same taxa) a tractable tree distribution if some common tasks can be performed efficiently in practice.
Example tasks are computing the probability of a tree and retrieving the tree with maximum probability.
As the main quality criteria for a tractable tree distribution we consider its accuracy, that is, how well it estimates the probability of trees, in particular of those in the 95% credibility set.
In simulation studies we can also test whether a distribution contains the true tree.
If we generate a type of distribution for the same data multiple times, we can consider the precision and the stability, that is, how much the probabilities of trees and how much the accuracy change, respectively.

## What is the best distribution?

We find that the best choice of distribution depends on the number of samples and the difficulty of the problem. 
First, CCD0 is best for few samples in terms of accuracy, precision, and stability, then CCD1 catches up and becomes the best method in the mid range, while CCD2 and the sample distribution require a huge number of sampled trees to become competitive.

## Comparison with MCC

In fact, running the MCC tree on any moderately high entropy posterior almost always results in different MCC trees, unlike the CCD method. This shows a collection of 20 summary trees on the example RSV2 data set that comes with BEAST2.
Left, the MCC trees showing considerable differences between the various runs, while CCD0 is much more stable.

![RSV2 MCC vs CCD0 point estimates](/images/RSV2MCCvsCCD0pointestimates.png)


## Comparison with HIPSTR

Baele et al. (2024) recently proposed the HIPSTR algorithm to obtain a point estimate based on a sample of trees. 
On the datasets used in their paper, HIPSTR is faster than the CCD-MAP estimates, but produces a tree with a lower product of clade credibilities than the CCD0-MAP algorithm. 
The authors used the first version of the CCD package without the performance improvements described below. 
This led to CCD0-MAP not finishing in a reasonable time for some of the datasets. 
The new version of CCD addresses this issue and significantly improves the runtime.

### EBOV (516)

| Method               | log(MCC)    | Runtime |
| -------------------- | ----------- | ------- |
| HIPSTR               | −950.08     | **57s** |
| CCD1                 | -1752.89    | 171s    |
| CCD0                 | **-923.66** | 11 min  |

### EBOV (1610)

| Method               | log(MCC)     | Runtime |
| -------------------- | ------------ | ------- |
| HIPSTR               | −3039.60     | **6s**  |
| CCD1                 | -4686.60     | 22s     |
| CCD0                 | **-2864.92** | 44s     |

### Simulated EBOV (1610)

| Method               | log(MCC)     | Runtime |
| -------------------- | ------------ | ------- |
| HIPSTR               | -1786.64     | **81s** |
| CCD1                 | -3543.36     | 194s    |
| CCD0                 | **-1760.87** | 6min    |

### SARS-CoV-2 (3959)

| Method               | log(MCC)     | Runtime |
| -------------------- | ------------ | ------- |
| HIPSTR               | -8630.85     | **21s** |
| CCD1                 | -11540.45    | 65s     |
| CCD0                 | **-8325.58** | 6min   |

### SARS-CoV-2 (15616)

| Method               | log(MCC)      | Runtime |
| -------------------- | ------------- | ------- |
| HIPSTR               | -51352.61     | **85s** |
| CCD1                 | -58636.38     | 150s    |
| CCD0                 | **-49836.26** | 9h      |

### HIPSTR as a special case of CCD0

The HIPSTR algorithm is a special case of the CCD0 algorithm, where the CCD graph is not expanded to include all clades splits compatible with sampled clades.

## The CCD0 expansion problem

CCD0 expands the graph by searching for all clades C in the set of clades S, whether there are other clades C1 and C2 in S that are disjoint and their union equals C.

First, we put all clades C in S into buckets so that bucket_i contains clades of size i only.
Note that if clade C has size n, we only need to check buckets 1 up to (n+1)/2, since any match in the bucket i will come with a complementary match in bucket (n-i).

### Trick 1: only check the smaller bucket of i and n-i

The number of clades in buckets can vary quite a lot.
Though usually most of the clades are small, there can be a lot of larger clades as well, and the distribution of clades can be multimodal, as in the example shown here:

![clade size distribution](/images/cladeSizeDistribution.png)

We can exploit this by checking clades inside the smaller of bucket i and bucket n-i as candidates for C1.
In practice this appears to speed up things by a factor of about two.

### Trick 2: sort buckets by lowest taxon

If we sort the clades in buckets by the lowest ordered taxon in the clade, we do not need to check every clade C1 in a bucket: if the lowest ordered taxon in C is quite large, we can start at the first clade in the bucket that contains that taxon.
All clades before that one will contain at least one taxon that is outside C, namely a taxon that is lower in the ordering than the lowest in C.
We keep track for each bucket and for each taxon where the appropriate clade is located through a lookup table.
So, we incur a little bit of overhead from sorting, but that is made up for in less checks.
In practice, we gain another factor two speed up.


### Trick 3: also sort by highest taxon

We can add another criterion to the sort to tie-break clades with the same lowest taxon using the highest ordered taxon.
For a clade C with lowest ordered taxon j highest ordered taxon k, we only need to check clades in the range for a bucket starting at the first clade with lowest ordered taxon j and ending at the last taxon with highest ordered taxon k.

A similar lookup table as for lowest taxon is maintained for each bucket and each taxon, so determining the range is very fast.

Furthermore, trick 1 can be refined to check the sizes of ranges for buckets i and n-i to select the bucket with the shortest range.
We gain another 30% speed up -- not as impressive as tricks 1 and 2, but it sure helps for large clade sets.


### Computational complexity

It is not quite clear what the computational complexity is of the algorithm we now get, but in practice it seems that a quadratic function fits quite well.

![compute time of expand](/images/expandComplexity.png)


## Conclusion

Our research indicates that the CCD0-MAP tree and the greedy consensus should be the preferred point estimators for Bayesian phylogenetic inference of time-trees.
The CCD0-MAP will always be fully resolved.
The traditional restriction to choose among sampled trees imposes such a high cost that earlier concerns about using unsampled trees as point estimates are no longer justified.
CCDs provide better estimates of individual tree probabilities than the sample distribution, especially for difficult phylogenetic problems.
However, selecting the most suitable CCD model for a given dataset remains a challenging issue that requires further investigation.

Given our results, we propose retiring the MCC-from-sample point estimator in favour of the more accurate and reliable alternative of CCD-MAP trees.


## References

L Berling, J Klawitter, R Bouckaert, D Xie, A Gavryushkin, A Drummond
Accurate Bayesian phylogenetic point estimation using a tree distribution parameterized by clade probabilities
[PLOS Computational Biology 21.2 (2025)](https://doi.org/10.1371/journal.pcbi.1012789)

Guy Baele, Luiz M. Carvalho, Marius Brusselmans, Gytis Dudas, Xiang Ji, John T. McCrone, Philippe Lemey, Marc A. Suchard, Andrew Rambaut
[bioRxiv 2024.12.08.627395](https://doi.org/10.1101/2024.12.08.627395)
