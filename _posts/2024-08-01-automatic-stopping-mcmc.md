---
layout: post
title: Automatic stopping MCMC
tags: []
---
<p style="color:gray">1 August 2024 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Usually, when running an MCMC analysis, the first thing to do is open Tracer to check the trace log. The main things to look out for are

* are the effective sample sizes ([ESSs](https://www.beast2.org/what-is-ess/)) high enough, especially for the posterior, prior and likelihood, and
* are independent runs converging to the same distribution for all parameters.

And if one of these two checks fails, usually the first thing to try is to resume the MCMC to obtain a longer chain.
The thing that is missing from this process is a sense of how well the tree sample is mixed.
The tree height, tree length and tree prior entries in the trace log only capture summary statistics of the sampled trees, but by themselves do not provide a good insight in how well the trees mix.

## Automatic stopping

Recently, a new package became available that tries to address all of the above:

* it automatically assesses how long to run an MCMC before it can be stopped, and
* it considers metrics on tree space.

It achieves this by running two MCMC chains in parallel and periodically checks various stopping criteria.
The default stopping criteria are:

* `TraceESS`: this measures the ESS on selected traces, by default only posterior, likelihood and prior, but more can be added.
There are exceptions where the ESS does not tell about the quality of mixing, such as population sizes for the Bayesian skyline plot, or indicator variables that are stuck at true or false in methods that us stochastic variable selection (e.g. bModelTest, DTA).
Since it is not possible beforehand to determine whether a parameter should be included or not, only posterior, likelihood and prior are set up by default, since we know that the ESSs of those metrics should be high.
* `TreePSRF`: this tracks two metrics
	(1) a metric that compares the variance of RNNI distances within the individual chains with that of the variance when the tree sets of the individual chains are merged. This is similar to the R-hat metric (Gelman & Rubin, 1992).
	(2) tree ESS (as per Lanfear et al, 2016) but with growing number of focal trees for stability of ESS estimates.

When all stopping criteria pass, the MCMC stops and produces a combined trace and tree logs with burn-in removed from the two independent MCMC chains.
So, there should be no more need to resume an MCMC analysis when using the ASM method.

For more details, see Berling et al, 2023.

## In practice

Instructions for usage for the ASM package is available [here](](https://github.com/rbouckaert/asm/blob/main/README.md).
Essentially, it is a matter of installing the package and selecting ASM on the MCMC panel in BEAUti.

<img width="916" alt="asm" src="https://raw.githubusercontent.com/rbouckaert/asm/main/doc/asm-select.png">

You can set parameters by clicking the TreePSRF button for the Gelman-Rubin-like criterion or TraceESS button for the trace-ESS criterion.

<img width="916" alt="asm-options" src="https://raw.githubusercontent.com/rbouckaert/asm/main/doc/asm-options.png">



## References

Lars Berling, Remco Bouckaert, and Alex Gavryushkin.
Automated convergence diagnostic for phylogenetic MCMC analyses
BioRxiv 2023 [DOI:10.1101/2023.08.10.552869](https://doi.org/10.1101/2023.08.10.552869)

Gelman A, Rubin DB. Inference from iterative simulation using multiple sequences. Statistical science. 1992 Nov;7(4):457-72.

Lanfear R, Hua X, Warren DL. Estimating the effective sample size of tree topologies from Bayesian phylogenetic analyses. Genome biology and evolution. 2016 Aug 1;8(8):2319-32.