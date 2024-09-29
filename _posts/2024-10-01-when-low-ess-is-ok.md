---
layout: post
title: When low ESSs in Tracer can be OK
tags: []
---
<p style="color:gray">1 October 2024 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Usually, it is recommended for effective sample sizes ([ESSs](https://www.beast2.org/what-is-ess/)) to be at least 200, especially for posterior, prior and likelihood, and there are severals [tips](https://www.beast2.org/increasing-esss/) and [tricks](https://www.beast2.org/2019/08/01/increasing-ess.html) to increase the ESS.
However, there are some instances where an ESS of less than 200 is acceptable, but be careful in judging when these situations apply.
Here are a few examples where low ESSs are OK:

## Skyline plot population and group sizes

One such case is the parameters for the Bayesian skyline plot. 
The population sizes and groups sizes together with the tree determine the population function.
It is not uncommon for the ESSs of population and group sizes to be quite low, due to multi-modality of these parameters.
This can habben when two consecutive population sizes are quite close in one mode and then one of these population size parameters to be grouped with the next group in the other mode.
Since both group sizes and population sizes have to move at the same time to switch between modes, this can happen rather infrequently causing low ESSs.

The population function defined by these different modes usually is not that different.
Since it is the population function that is of interest, low ESSs for the individual dimensions of the populations size and group size parameters can be ignored.

The group and population sizes still need to be logged for doing a demographic reconstruction (e.g. in Tracer), so they still end up in the trace log and will be visible in Tracer.


## Indicator parameters that are stuck

In stochastic variable selection, an indicator variabel is used to determine whether part of the model should be used or not.
For example, in the [bModelTest](https://github.com/BEAST2-Dev/bModelTest/wiki) site model there are indicator variables to decide whether the gamma rate heterogeneity model should be used or not.
Likewise, the discrete trait model uses indicator variables to decide which rates to set to non-zero in the rate matrix.

When the data pushes very hard towards one model over the other, the indicator variable will be stuck at either true or false, and the ESS cannot be determined.
In this case it is acceptable for the ESS to be ignored.
Note that when the indicator variable is only very occasionally (perhaps even for a single sample) switched to the other value, the ESS will be very high, possibly close to the number of samples in the trace log.

## Unused dimensions of parameters under reversible jump

Under reversible jump, parameters can have variable dimension.
Since Tracer requires each column to be completely populated, such parameters need to log dimensions, even if they are not used.
This used to be the case for bModelTest, where all 6 dimensions of the substitution model's rate parameter were logged: when HKY dominates during the MCMC, most of the time the higher dimensions of the rate parameter are not sampled.
This results in low ESS for those under-sampled parameters.
Currently, bModelTest only logs the resulting rates for AC, AG, AT, CG, CT, and GT, which should have good ESSs.

## Posteriors from nested sampling

Nested sampling implemented in the [NS](https://github.com/BEAST2-Dev/nested-sampling/) package provides an alternative to creating a sample from the prior.
However, nested sampling samples likelihoods in increasing order, and the generated posteriors therefore has a trace for the likelihood that is monotonically increasing.
This results in ESS estimates in Tracer that are very low, since it is based on autocorrelation and all samples are independent samples in a posterior obtained from nested sampling.
The actual ESS is therefore the total number of samples in the trace log, and ESS estimates in Tracer should be ignored.

NB Burn-in in Tracer should be set to zero for nested sampling posteriors.

## Nuisance parameters

Sometimes ESSs can be slightly low for parameters that are not of interest, like the kappa parameter for the HKY model when the research question revolves around the age of the tree.
Care must be taken to let these somewhat reduced ESSs slip though: make sure the distributions of individual MCMC runs match.

It is much preferred though to fix the problem by increasing the weight of operators that do proposals for these nuisance parameters, run the MCMC a bit longer, or apply any of the other techniques [to increase ESS](http://www.beast2.org/2019/08/01/increasing-ess.html).

## Low ESS on individual runs, but good on combined runs

It is standard practice to run your analysis multiple times from different starting positions (i.e. using different seeds for the random number generator).
It is possible for MCMC -- being a randomised algorithm -- to get stuck in local optima, and the way to detect this is by doing multiple runs.
If individual runs show low ESS for some parameter values, but combining the trace logs results in good ESS values, this is confirmation these parameters were sampled from the same distribution.
