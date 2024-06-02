---
layout: post
title: Simulators in BEAST
tags: []
---
<p style="color:gray">1 June 2024 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

There are quite a few cases where a simulation study is useful, e.g. for testing whether a theory can be falsified. BEAST has a number of ways to simulate (hyper-)parameters, trees and sequence data.


## Simulate by MCMC

If you want to simulate trees and its parameters, it is possible to set up an analysis with the appropriate priors in BEAUti or craft an XML by hand, and use the `sampleFromPrior` option for BEAST to sample trees and tree prior hyper parameters.
For a given tree, the `beast.app.seqgen.SimulatedAlignment` can then be used to simulate an alignment.
The `SimulatedAlignment` can be used as a replacement of `Alignment`.
This can be useful in [well calibrated simulation studies](https://github.com/rbouckaert/DeveloperManual) (Mendes et al, 2024).

## `DirectSimulator`

The `beast.base.inference.DirectSimulator` provides a simulator that is more efficient than sampling from MCMC, and uses independent implementations for directly simulating parameter values from parametric distributions.
There are examples XML files included in the BEAST distribution, also available [here](https://github.com/CompEvol/beast2/blob/master/examples/testDirectSimulator.xml), [here](https://github.com/CompEvol/beast2/blob/master/examples/testDirectSimulator2.xml) and [here](https://github.com/CompEvol/beast2/blob/master/examples/testDirectSimulatorHierarchical.xml).
It requires hand crafting the XML.
Any Distribution that implements the `sample` method can be used, including the `YuleModel` for sampling trees and `Prior` for sampling parameters, but it is quite limited.

## The (re)MASTER packages

[MASTER](https://tgvaughan.github.io/MASTER/) (Vaughan & Drummond 2013) is a BEAST 2 package aimed at generating simulators for stochastic models of structured and unstructured population dynamics, but also allows for simulating other trees and networks.
It is more powerful than the DirectSimulator.

[ReMaster](https://tgvaughan.github.io/remaster) (Vaughan, 2024) is a faster, leaner complete rewrite of MASTER and has more capabilities than MASTER. There is ample [user documentation](https://tgvaughan.github.io/remaster/).


## LinguaPhylo

[LinguaPhylo](https://github.com/LinguaPhylo/linguaPhylo) (Drummond et al, 2024) is a probabilistic model specification language for reproducible phylogenetic analyses that allows simulation under a wide range of models.
It is more powerful than the reMaster and comes with its own GUI: LPhyStudio.

LPhyBEAST is a program that converts LPhy model specification, and a data block into a BEAST 2 XML input file. 
The lphybeast and LPhyBeastExt packages for BEAST 2 are required for BEAST 2 to be run on these XML files.


## References

AJ Drummond, K Chen, FK Mendes, D Xie. LinguaPhylo: a probabilistic model specification language for reproducible phylogenetic analyses [PLOS Computational Biology 19 (7), e1011226]
(https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1011226)

Mendes FK, Bouckaert R, Carvalho LM, Drummond AJ. How to validate a Bayesian evolutionary model. [bioRxiv. 2024:2024-02.](https://www.biorxiv.org/content/10.1101/2024.02.11.579856.abstract)

Vaughan TG, Drummond AJ. A stochastic simulator of birth-death master equations with application to phylodynamics. Mol Biol Evol 2013;30:1480–93.

Vaughan TG. ReMASTER: improved phylodynamic simulation for BEAST 2.7. [Bioinformatics. 2024 Jan 1;40(1):btae015](https://academic.oup.com/bioinformatics/article/40/1/btae015/7513691).