---
layout: post
title: What to do with too much data
tags: []
---
<p style="color:gray">1 Aug 2025 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

Even after you tried all [performance suggestions](https://www.beast2.org/performance-suggestions/index.html), like using BEAGLE for tree likelihood calculations and coupled MCMC,
sometimes, with the current state of the art, doing a full Bayesian analysis is out of reach.
Here are some ideas in no particular order you can try to perform a Bayesian analysis with BEAST2 that allow dealing with datasets that are too large.

## Subsampling sequences

Create a number of random subsamples of the sequences (or genes or loci) and compare results of the independent runs for the research question of interest.
When the answer is not sensitive to the subsample, it can be assumed that the answer will be the same for the full analysis.
Be aware some methods are rather sensitive to sampling bias, for example the discrete trait model aka discrete phylogeography (Layan et al, 2023).

## Super tree method

Another approach is to create a subsample that covers most of the tree and form a skeleton tree analysis with only these sequences.
Then, split the remainder of the alignment into smaller subsets and combine with the skeleton subsample.
The [TreeGrafter](https://www.beast2.org/2019/05/27/babel-tools.html#treegrafter) tool in the [Babel](https://github.com/rbouckaert/Babel) package can be used to graft the subsampled trees into the skeleton trees.
The smaller subsets should form monophyletic clades, so this approach is not always practical.

## Try Targeted BEAST operators

Recently, a new set of operators have been developed (Bouckaert et al, 2025).
You might be interested in the [Targeted BEAST](https://github.com/nicfel/targetedbeast/) package, which provides new MCMC operators that have been shown allow scaling up analyses to up to 10.000 Influenza sequences.
These operators appear particular effective when there is not a lot of variation in the sequences, e.g. densely sampled pathogen outbreaks.
These operators may not be effective for other kinds of data.
Setting up the operators can be done in BEAUti -- see [README](https://github.com/nicfel/targetedbeast/blob/main/README.md).

## Variational Bayes

[Variational Bayes](https://github.com/rbouckaert/cubevb/) (Bouckaert, 2024) can provide a [good approximation](https://www.beast2.org/2024/05/01/how-accurate-are-cubes.html) to a full Bayesian analysis.
It considers a large subspace of the complete tree space (so is an approximation), but runs an order of magnitude faster than full MCMC.
It requires manual editing of the XML -- see examples and data provided with the CubeVB package.

## Online MCMC

[Online MCMC](https://github.com/rbouckaert/online/) can extend an existing analysis with more data.
So, it allows splitting up the analysis and extending it with more sequences later on.
You can start with a small representative subsample, and keep on adding data till you are out of patience.


Work is continuously under way to improve the efficiency of MCMC operators and other ways to allow more data to be analysed, but for now the above are some practical things you can try when the default MCMC analysis is not powerful enough.



## References

RR Bouckaert
Variational Bayesian Phylogenies through Matrix Representation of Tree Space
([bioRxiv, 2023.10. 19.563180](https://doi.org/10.1101/2023.10.19.563180)) [PeerJ 2024](https://peerj.com/articles/17276)

Bouckaert RR, Weidemüller PH, Esquivel Gomez LR and Müller NF.
Improving the Scalability of Bayesian Phylodynamic Inference through Efficient MCMC Proposals.
[BioRxiv](https://www.biorxiv.org/content/10.1101/2025.06.18.660471v1),
[doi:10.1101/2025.06.18.660471](https://doi.org/10.1101/2025.06.18.660471),
2025

Layan M, Müller NF, Dellicour S, De Maio N, Bourhy H, Cauchemez S, Baele G. 
Impact and mitigation of sampling bias to determine viral spread: Evaluating discrete phylogeography through CTMC modeling and structured coalescent model approximations. 
Virus evolution. 2023 Jan 1;9(1):vead010.