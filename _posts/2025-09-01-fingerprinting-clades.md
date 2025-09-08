---
layout: post
title: Fingerprinting clades -- optimising CCD0 revisited
tags: []
---
<p style="color:gray">1 September 2025 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

[Previously](https://www.beast2.org/2024/04/02/optimising-code.html), we looked at some general optimisation techniques applied to calculating the conditional clade distribution. In particular CCD0 (Berling et al, 2025), this requires finding all clades $$S$$ and all pairs of clades $$C1$$, $$C2$$ in $$S$$ such that $$C1$$ and $$C2$$ disjoint and $$C1\cup C2$$ form a clade $$C$$ in $$S$$.

We group clades from $$S$$ in buckets of the same size, so that we can check for all $$C$$ in a bucket of size $$k$$ whether $$C1$$ is in bucket of size $$i$$, then $$C2$$ must be in bucket of size $$k-i$$.
only checking whether the smaller of buckets[another 
This allows for some particular optimisations, such as only checking the smallest of buckets $$i$$ and bucket $$k-i$$, sorting entries in buckets by the lowest taxon in a clade and checking only entries in a bucket that have equal or higher starting taxon than starting taxon in $$C$$, and something similar for the last taxon (see [this post](https://www.beast2.org/2025/03/01/CCD0-revisited.html) for more details).

## Fingerprinting clades

Recently, a preprint of the paper on Delphy (Varilly et al, 2025) pointed out a nice technique to manage clade sets.
Instead of BitSets (as used in the current implementation of CCDs) us a fingerprint.
Each taxon is assigned a number of type long randomly generated, and for each clade the fingerprint is calculated as the exclusive or of the fingerprints of its two child clades.

Since xor is commutative ($$A \oplus B = B \oplus A$$), the order in which child clade coalesc does not matter for the fingerprint of clade $$C$$:
the fingerprint is in fact always equal to the xor of all the taxa in the clade.

The most expensive test in building a CCD0 is determining whether $$C1\cup C2=S$$ under the condition $$C1\cap C2=\emptyset$$, since this requires BitSet operators that are computationally expensive.
Fortunately, fingerprints can help out here: if $$C1\cup C2=S$$ then
$$f(C1) \oplus f(C2) = f(S)$$ were $$f(.)$$ is the fingerprint of a clade.
This is a necessary but not sufficient condition to test $$C1\cup C2=S$$ with $$C1$$ and $$C2$$ disjoint: if $$C1=\{A\}, C2=\{A,B,C\}, S=\{B,C\}$$, that is $$C2$$ is the parent clade of $$C1$$ and $$S$$, we still have $$f(C1) \oplus f(C2) = f(S)$$ since $$f(C1) \oplus f(S) = f(C2)$$.
Another necessary condition is that the size of $$C1$$ plus the size of $$C2$$ must be equal to the size of $$S$$.
Note that this implicitly tests that $$C1$$ and $$C2$$ are disjoint.
If one of these conditions fails, we can be sure there is no match.
If these conditions succeed, we can be almost sure there is a match, with a very small probability we get a false positive due to randomness of the fingerprints of taxa.
However, this probability is vanishingly small, even for large taxon sets (see Varilly et al, 2025).


## Performance

Implementing this for the CCD0 point estimate in TreeAnnotator, we can measure the performance difference for large tree sets.
Most optimisations not involving BitSets still apply.
For a tree set with 6636 taxa, 9000 trees, 282812 clades and 2271899 clade splits (the updated global language tree set from Bouckaert et al, 2022) there is about a 10x speedup in calculating the point estimate.
The point estimate does not differ from that of the implementation using BitSets.


## References

L Berling, J Klawitter, R Bouckaert, D Xie, A Gavryushkin, A Drummond
Accurate Bayesian phylogenetic point estimation using a tree distribution parameterized by clade probabilities
[PLOS Computational Biology 21.2 (2025)](https://doi.org/10.1371/journal.pcbi.1012789)

Bouckaert R, Redding D, Sheehan O, Kyritsis T, Gray R, Jones KE, Atkinson Q. Global language diversification is linked to socio-ecology and threat status. SocArXiv. 2022 Jul 15.

Varilly P, Schifferli M, Yang K, Burcham T, Cronan P, Glennon O, Jacks O, Laning E, Marrs L, Oba K, Yeung S. Delphy: scalable, near-real-time Bayesian phylogenetics for outbreaks. bioRxiv. 2025 Mar 26:2025-03.


<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
