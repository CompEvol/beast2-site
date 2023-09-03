---
layout: post
title: Improved fixed tree and tree set support
tags: []
---
<p style="color:gray">1 Sep 2023 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

There has always been [fixed tree](https://www.beast2.org/fix-starting-tree/) support, but now there is BEAUti support for it as well, so no need to edit the XML any more. The [FixedTreeAnalysis](https://github.com/rbouckaert/FixedTreeAnalysis/) package furthermore has support for sampling from a posterior tree set for post-hoc analyses.

# When to use a fixed tree

It can be useful to run an analysis where the tree is fixed, for example

* when a tree is available from a previous analysis, but the sequence data is not.
* when estimating epidemiological parameters and using a computationally intensive tree prior, like compartimentalised models (e.g. MASCOT).
* when you are interested in the ancestral reconstruction of internal nodes (e.g. a discrete trait analysis (DTA)) on a large tree, and the joint analysis takes longer than a separate BEAST analysis to infer a tree, and another post-hoc DTA can be done quickly afterwards.

Be aware that using a single tree means that not all of the uncertainty in the tree posterior is captured, and there may be bias towards being over-confident in results.

## How to use a fixed tree

* Install the `FixedTreeAnalysis` package
* Select the `fixed tree analysis` template (menu `File => Templates => Fixed Tree Analysis` in BEAUti)
* Import the tree from file (menu `File => Import Fixed Tree`)
* Add partitions with data that link to the tree.

By default, the tree remains fixed to the starting tree throughout the analysis.
It is possible to keep the topology fixed but estimate the branch lengths by double clicking the tree partition in the partitions panel and check the `Allow node height changes` checkbox in the dialog that pops up.

<img src='https://raw.githubusercontent.com/rbouckaert/FixedTreeAnalysis/master/doc/fixed-tree-tutorial/figures/BEAUti-topology.png'/>

## Fixed tree tutorial

There is a [tutorial](https://github.com/rbouckaert/FixedTreeAnalysis/blob/master/doc/fixed-tree-tutorial/README.md) with more detail on how to set up a fixed tree analysis.


# When to use a posterior tree set

It can be useful to run a post-hoc analysis where a tree posterior is available in the form of a NEXUS tree log file (as produced by BEAST), for example

* when a tree posterior is available from a previous analysis, but the sequence data is not.
* when estimating epidemiological parameters and using a computationally intensive tree prior, like compartimentalised models such as MASCOT.
* when you are interested in the ancestral reconstruction of internal nodes (e.g. a discrete trait analysis (DTA)) on a large tree, and another post-hoc DTA can be done quickly afterwards.

Be aware that using a tree posterior instead of using a joint analysis has the potential to result in biased estimates of the parameters of interest. 

The tree will be uniformly sampled from the tree set during the MCMC (Pagel et al, 2004), so will change regularly while running the analysis so that parameters cannot over-optimise on a single tree.



## How to use a tree set

* Install the `FixedTreeAnalysis` package
* Select the `tree set analysis` template (menu `File => Templates => Tree Set Analysis` in BEAUti)
* Import the trees from file (menu `File => Add Tree Set`)
* Add partitions with data that link to the tree.



## Tree set tutorial

There is a [tutorial](https://github.com/rbouckaert/FixedTreeAnalysis/blob/master/doc/tree-set-tutorial/README.md) with more detail on how to set up a tree set analysis.



# Reference

Pagel M, Meade A, Barker D. Bayesian estimation of ancestral character states on phylogenies. Systematic biology. 2004 Oct 1;53(5):673-84.