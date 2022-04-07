---
layout: post
title: What is new in v2.6.7
tags: []
---

##  Fix auto-set clock rate in BEAUti when independent trees are involved

When two or more independent trees are part of the analysis, and there is no timing information available, clock rates should be fixed at 1 for all trees. If trees are linked, it makes sense to estimate clock rates for all but the first partition, which is the default behaviour for XML produced by BEAUti. A bug that made clock rates estimated when trees were independent is fixed in this release.

##  Loading two dimensional data into trait dialog robustified

Two dimensional traits like geographical locations were not properly imported (in fact, they were mostly ignored) in the trait dialog when importing from file. Somehow, a bug was introduced, but with this release it should be possible again to import two dimensional traits from file again.

##  Make NexusParser more robust and allow Fasta imports to select from all data types

Fasta file format was only allowed to be nucleotide or amino acid when importing alignments into BEAUti. The list of data types is now extended to available data types, and can be programmatically extended by packages even further. This means package developers can specify alignments for import in BEAUti easier than before.

##  Fix TreeAnnotator `-heights` processing issue

The `-heights keep` option of TreeAnnotator was not functioning as expected: it should not change heights of internal nodes, but was changing them -- this bug is fixed.

##  Make `Prior.sample()` obey parameter bounds

Previously, bounds defined on `RealParameter`s were ignored, so samples could be generated outside these bounds.

##  Fix TipDateRandomWalker to ensure heights are always >= 0

The operator on tip dates was allowed to move node heights below zero. Though strictly speaking this is not incorrect, it does confuse any post-processing tool: since there is no information about absolute heights in the tree file, these tools assume that the youngest tip is at zero (potentially offset by a user defined date), and put the rest of the nodes at a height relative to the youngest tip. So, with tip sampling, there should always be a youngest tip fixed at zero, and sampled tips should not be allowed to go below zero. The `TipDateRandomWalker` operator is fixed to guarantee no tip gets a negative height.

##  Remove superfluous new lines in XML produced by BEAUti

On some operating systems, XML produced by BEAUti introduced a new line for every line in the XML. This double-spacing made the XML files harder to read. XML files should be more compact now.

##  SimulatedAlignment correctly links indices of tree to alignment

Previously, tips of the tree did not reliably link to taxa in the alignment, making simulation studies where information on tips were considered unreliable. This is fixed now.

## Some additional small changes 

*  Option 'ascii' added to Logger to allow it to convert output to ascii or not
*  Add upper limit to delta exchange optimization 
*  Made tree input to RatesStatistic required to ensure appropriate behaviour



## New packages

The following packages were added since the last BEAST release:

* [SNAPPER](https://github.com/rbouckaert/snapper) for fast multi species coalescent inference with SNP data, 
* [StarBeast3](https://github.com/rbouckaert/starbeast3) for fast multi species coalescent inference with sequence data for many loci,
* [OBAMA](https://github.com/rbouckaert/obama/) for Bayesian model averaging with amino acid site models, and
* [BICEPS](https://github.com/rbouckaert/biceps) for efficient inference using coalescent epoch models and Yule skyline models.

Also keep an eye out on new packages in the [package-extra.xml](https://github.com/CompEvol/CBAN/blob/master/packages-extra.xml) repository where [SPEEDEOM](https://github.com/rbouckaert/speedemon) for species delimitation, [contacTrees](https://github.com/NicoNeureiter/contacTrees) for jointly inferring phylogies and contact events from linguistic data and [online](https://github.com/rbouckaert/online) (for online analyses) packages were added recently.

