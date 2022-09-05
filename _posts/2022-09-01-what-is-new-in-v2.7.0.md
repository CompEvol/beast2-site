---
layout: post
title: What is new in v2.7.0
tags: []
---

## BEAUti has had a facelift, allows dark mode

BEAUti now uses the JavaFX library instead of Swing, which looks a bit more modern and allows themes to be changed easily.
There are now entries under the `View` menu to choose themes, including `Dark mode`.

A lot of refactoring has been required to move to the JavaFX library, so not everything is back to its old standard yet, but hopefully most of the interface is sufficiently useful.
Do let us know by [logging an issue](https://github.com/CompEvol/BeastFX/issues) if you notice things are not working as expected.

The reason for the move to JavaFX is that is allows automatic user interface testing in Java 17. 
Automatic testing in the past has been crucial in identifying problems and without it it would not be responsible to move to Java 17 and we would be stuck with Java 8.

Another consequence is that JavaFX has to be available. 
Though expert users may be able to install both Java 17 and the corresponding correct version of JavaFX, this is a somewhat error prone and uncomfortable exercise, so BEAST is now only available packaged with Java 17.0.3 and JavaFX.


<img alt='BEAUti v2.7.0 default' src='/images/BEAUti.v2.7.0.default.png' width="49%"/>
<img alt='BEAUti v2.7.0 dark' src='/images/BEAUti.v2.7.0.dark.png' width="49%"/>


## BEAUti templates reorganised for better performance

BEAUti templates have been updated to include smarter operators (see "Faster MCMC convergence" below).
Out of date templates for *BEAST and the uncorrelated relaxed clock moved to the [BEAST_CLASSIC](https://github.com/BEAST2-Dev/beast-classic/) package.
Users are encouraged to install [StarBeast3](https://github.com/rbouckaert/starbeast3) and the [optimised relaxed clock](https://github.com/jordandouglas/ORC) (ORC) packages as replacement.
 
## "Help me choose" buttons added to BEAUti for direct targeted help

Where help is available, small help buttons are added to tabs and entries in BEAUti that link to the "[help me choose](https://beast2-dev.github.io/hmc/)" site, providing direct targeted help for the entries in the BEAUti panels. 
At the time of writing, there are 181 help me choose pages available, mostly for the Standard and StarBeast3 templates.
This is a living project and package developers can add more pages and link them by putting appropriate information in BEAUti templates.

## Faster MCMC convergence due to smarter operators

BEAUti templates have been updated to include the Tree Stretch and Epoch Flex operators   from the BICEPS package as replacement of the tree scaler for better mixing of trees.
Standard operators are replaced by [Bactrian operator](https://www.beast2.org/2021/04/26/bactrian-proposals.html) variants where available.
Each partition gets an adaptable variance multivariate normal (AVMN) operator that does proposals for all site model operators and potentially the mean clock rate (if only applied to that partition) and the tree scale.
Since the AVMN operator does not always converge well, it is wrapped in a [adaptable operator](https://www.beast2.org/2021/05/24/developing-operators.html) together with the (Bactrian) standard operator for each of the parameters involved.
Altogether, these result in better mixing of MCMC chains.

## DensiTree v3.0.0 included, which allows pairwise comparison of tree sets

The version of DensiTree is updated to v3.0.0, which has more node ordering heuristics, but mostly has better support for comparing two tree sets by

1. Displaying two tree stes at the same time in the same DensiTree, one mirrored.
2. Provide clade comparison panel showing the difference in clade support and clade height distributions.
3. Allow interactive exploration by selecting clades in one of the main DensiTree, clade list or clade comparison panel, which then highlights the same clade in the other panels.

<img src="/images/DensiTree.v3.0.png" alt="DensiTree v3.0"/>

## Uses latest version of libraries: java 17, javafx, commons math, etc.

The code has been modernised to use the latest libraries: `antlr-runtime-4.10.1.jar`, `commons-math3-3.6.1.jar`, `junit5` and of course `java17` and `javafx`.
This allows use of [newer java features](https://en.wikipedia.org/wiki/Java_version_history) and java 17 has long term support (till September 2029).


## Code reorganised, BEAST split into two packages: BEAST.base and BEAST.app

The code is reorganised to follow a more logical java package structure.
Furthermore, the applications (BEAST, BEAUTi, TreeAnnotator, etc.) have been split off into its own package `BEAST.app` and moved to its own repository [here](https://github.com/rbouckaert/BeastFX).


## Module like treatment of BEAST packages to prevent package library clashes

Previously, all libraries were loaded by the same class loader, which meant that if Package A uses library X version 1 and Package B uses library X version 2 it was not well defined which version of the library got loaded.
With v2.7.0, each package can load its own version of the library without clashing.
This means, BEAST packages for v2.6.x need to be updated to work with v2.7.0.
Not all packages have been updated yet, but this is work in progress.


