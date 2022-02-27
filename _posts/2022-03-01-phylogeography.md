---
layout: post
title: Phylogeography in BEAST 2
tags: []
---
<p style="color:gray">1 March 2022 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

There are quite a few phylogeographical models in BEAST. This post attempt to give an overview of the available models and some clues on how to choose between them. The first thing to decided is whether to represent sample locations as discretised (e.g. by grouping samples in countries or regions) or continuous (i.e. latitude-longitude pairs). If you can reasonably assume that the migration process follows a random walk, continuous locations are more appropriate and point locations are more informative then when they are grouped in discrete clusters. This is usually the case for land dwelling plants and animals, viruses carried by land dwelling hosts, and hunter-gatherer societies. However, viruses on human hosts in modern times tend to spread through travel, and do not follow a random walk process, but rather jump between "demes". A discretised sampling location is more suited for such cases

## Discrete models

### Discrete trait model

The discrete trait model is in the [BEAST_CLASSIC](https://github.com/BEAST2-Dev/beast-classic/) package ([tutorial](https://github.com/BEAST2-Dev/beast-classic/releases/download/v1.3.0/ARv2.4.1.pdf)). It adds a new partition containing a site representing the discretised location and  uses Bayesian stochastic variable selection to reduce the number of parameters that need to be estimated. Inference tends to be fast compared to structured methods, and may be the only option if there are many demes. However, the discrete trait evolves along the branches without taking the tree generating process in account, which can have a big effect on the reconstruction. To include the tree generating process, you need to use one of the structured models.

### Structured coalescent models

There is the pure implementation of the structured coalescent model [Multi Type Tree](http://tgvaughan.github.io/MultiTypeTree) ([tutorial](https://taming-the-beast.org/tutorials/Structured-coalescent/)) package, which allows 3, perhaps 4 demes before it becomes computationally intractable. 

The [MASCOT](https://taming-the-beast.org/tutorials/Mascot-Tutorial/) ([tutorial](https://taming-the-beast.org/tutorials/Mascot-Tutorial/)) package approximates the structured coalescent, and allows more demes. Also, MASCOT allows the migration rate matrix to be informed by extra features through a generalised linear model (GLM). These feature can include information like whether demes are bordering each other, flight passenger numbers between demes, amount of trade between demes, etc. Time can be sliced over different epochs, allowing different rates of migration per epoch informed by differences in indicators in the different epochs. It allows you to determine what variables influence migration.

Another approximation to the structured coalescent can be found in the [SCOTTI](https://bitbucket.org/nicofmay/scotti) package. The approximate models have been applied to over ten demes.

### Structured birth/death models

If a birth death model is more appropriate for the tree prior than a coalescent model, the structured birth death model can be used. It is implemented in the [BDMM](https://github.com/denisekuehnert/bdmm) package ([tutorial](https://taming-the-beast.org/tutorials/Structured-birth-death-model/). A strong priors on at least one of the parameters in the model may be required to get convergence, and you want to perform some sensitivity analysis to such priors.

The BDMM model can distinguish different epochs and allow for different rates in each of the epochs. Rate matrices cannot be informed by GLM (yet).


## Continuous models


### Random walk on plane

The random walk on a plane model is implemented in the [BEAST_CLASSIC](https://github.com/BEAST2-Dev/beast-classic/) package. The internal node locations are explicitly sampled during MCMC, which makes it not very efficient. A more efficient alternative is to use a random walk on a sphere model.


### Random walk on sphere

This model is implemented in the [GEO_SPHERE](https://github.com/BEAST2-Dev/beast-geo) package ([tutorial](https://github.com/BEAST2-Dev/beast-geo/releases/download/v1.2.0/phylogeography_s.pdf)), and integrates out internal node locations. It takes the curvature of the earth in account, so compensates for distances between two longitudes at the equator compared to the distance between the same longitudes nearer the poles.


### Landscape aware model

One intuitively might think that landscape has a big influence on migration. After all, traversal of large deserts or extended bodies of water is not trivial. However, random walks are like ants: they tend to get everywhere, even with large obstacles in the way. So, when using landscape aware models one must have solid argumentation to justify going through the complexity of adding it to the model, and  it is desirable to have a sensitivity analysis to show the impact of making the assumption of non-homogeneous rates throughout the space.

There are several ways to capture variability of migration rates, e.g. through transforming space ([tutorial](https://github.com/BEAST2-Dev/beast-geo/releases/download/v1.2.0/phylogeography_s.pdf)) or by running a random walk on a graph. The graph can be any shape or form, and can for example only cover a land area, thus not allowing migration over seas (implemented in the [BREAK_AWAY](https://github.com/rbouckaert/break-away) package). Documentation is rather sparse, so it may be necessary to contact me if you want to use graph based models at this stage.

### Break away model

The founder-dispersal model  is implemented in the [BREAK_AWAY](https://github.com/rbouckaert/break-away) package ([post](http://www.beast2.org/2018/03/12/break-away-phylogeography.html)). It assumes at every internal node that one population migrates away, while the other population remains in the same place. Consequently, internal node locations are always at sample locations.
Where the random walks on plane and sphere usually result in reconstruction of the root near the centre of the samples, the break-away model can reconstruct the root at any of the sample locations, depending on the shape of the tree. 

## Disclaimer

These are the models I am aware of -- if I missed any, please let me know and I can add them to the list.