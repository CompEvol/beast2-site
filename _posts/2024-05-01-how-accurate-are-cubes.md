---
layout: post
title: How accurate are cubes?
tags: []
---
<p style="color:gray">1 May 2024 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

MCMC is the workhorse of BEAST analyses, however is limited in the number of taxa it can take.
The main issue is the problematic shape of tree space, which does not allow for a convenient representation in Euclidian space.
However, if we are willing to compromise a little by assuming a fixed ordering of taxa, then we can represent all rooted time trees that can be drawn on the particular ordering of taxa.
This representation is referred to as a 'cube', and it forms a tractable tree distribution that represents both topology and node ages by a single point in n-1 dimensional space.
The entries in that space can be considered the height of the gap between two consecutive taxa in the ordering.

![Cube representation](/images/cube.svg)

This idea was recently explored by [Bouckaert, 2024](https://doi.org/10.1101/2023.10.19.563180), and an algorithme designed to quickly find the cube for various site models and tree priors and a strict clock.

Obviously, not all trees can be represented. For example, for the cube displayed above, the topology of the clade over C, D and E can be ((C,D),E) if h<sub>3</sub> is lower than h<sub>4</sub> or (C,(D,E)) if h<sub>3</sub> is higher than h<sub>4</sub>, but the topology ((C,E),D) is impossible.
This raises the question, how much do we loose by using only a cube to represent the tree posterior.
Since not all topologies can be represented any more, does that mean that uncertainty estimates are still accurate?
The good news is that it passes [well calibrated simulation studies](https://github.com/rbouckaert/DeveloperManual/) (Mendes et al, 2024), and can accurately estimate tree heights and lengths as well as recover site model parameters and hyper paraemters for the tree prior.

However, to establish whether clade support estimates are still accurate, we took all clade support estimates from the well calibrated simulation study and sorted them in bins of associated accuracy.
Then, for each of the true trees used to generate the data, for every clade in that tree we looked up the bin that the analysis predicted it would be in.
This count determines the height of the bar for the bins.
Altogether, we can summarise results in a bar graph, like so.

![Over confident clade probability estimation](https://raw.githubusercontent.com/rbouckaert/DeveloperManual/master/figures/clades100over.png)

Results for cube space: appart from very small and very large probabilities, clade support tends to be slightly over estimated.

![Correct clade probability estimation](https://raw.githubusercontent.com/rbouckaert/DeveloperManual/master/figures/clades100mcmc.png)

Results for MCMC: most bars end up close to the x=y line, so we conclude that clade support estimates are accurate.


The fact that clade probabilities for cubes are a bit overconfident is no surprise, realising that since not all of tree space is represented, some clades are just not part of the posterior (e.g. like clade CE in the example above).
The fact is that it does not seem too far off, making a cube a convenient representation of posterior distributions.
If this can be made to work for a wider range of models, it may be a good compromise between doing a full Bayesian analysis through MCMC, which accurately represents uncertainty, and approximations by a single tree.
There are still quite a lot of things that need developing, like support for relaxed clocks, tip dated taxa, etc., but it seems a fruitful way to scale up Bayesian analyses.



## References

RR Bouckaert
Variational Bayesian Phylogenies through Matrix Representation of Tree Space
([bioRxiv, 2023.10. 19.563180](https://doi.org/10.1101/2023.10.19.563180)) [PeerJ 2024](https://peerj.com/articles/17276)

FK Mendes, R Bouckaert, LM Carvalho, AJ Drummond
How to validate a Bayesian evolutionary model
[bioRxiv, 2024.02. 11.579856](https://doi.org/10.1101/2024.02.11.579856)