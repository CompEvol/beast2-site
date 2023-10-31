---
layout: post
title: How to beat bad mixing
tags: []
---
<p style="color:gray">1 November 2023 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Here is a non-exhaustive list of options for dealing with bad mixing/non-convergence.

## Easy ways to detect non-nonvergence

There are two kinds of hints your MCMC has not converged:

* [ESS](https://www.beast2.org/what-is-ess/)s in Tracer are not reaching 200. This may be fixed by trying to increase ESS (Option 1 below).
* concatenating two or more chains in Trace give ESSs lower than 200, and distributions differ between different runs. This suggest the distribution is bimodal and the MCMC gets stuck in local optima and may require more drastic action (Options 2-5).



## Option 1: Try to increase ESS

If you have not reached ESSs of over 200, there are number of ways to [increase ESS](https://www.beast2.org/increasing-esss/), including reweighing of operators, and increasing log-frequency, but in most cases it may be just a matter of running the chains longer. 
BEAUti suggests a chain length of 10 million by default, but it really depends on your data and model whether that is not sufficient. 
Some analyses require chains of in the billions.

You can resume a previous run with BEAST, so no need to go through burn-in again. 
Before resuming, you want to can estimate how much longer you need by extrapolating from an existing run. 
For example, if you ran for 10 million steps and the worst ESS is 50 you may need another 30 million steps to get an ESS of 200. Update the chain length in the XML before resuming.

Make sure your MCMC runs as efficiently as possible (see [performance suggestions](http://www.beast2.org/performance-suggestions/index.html) for some hints) in order to reduce waiting time and electricity. 




## Option 2: Use Coupled MCMC or nested sampling with multiple particle

[Coupled MCMC](https://www.beast2.org/2021/09/01/coupled-mc3.html) (aka MC3, and MCMCMC) and [nested sampling](https://www.beast2.org/2018/10/01/nested-sampling2.html) are algorithms that, like MCMC, produce a posterior distribution. 
But unlike MCMC, not a single state, but multiple states are maintained (and run in parallel).
This allows for more efficient exploration of the state space, and reduced the chance of getting stuck in a local optimum.
Furthermore, Coupled MCMC tends to produce more ESS per second than MCMC, though it needs more threads, so more computational resources to run.



## Option 3: Use a simpler model

Sometimes there is just not enough signal in the data to estimate all parameters of the  most complete model, which shows up as different chains converging to different distributions or low ESSs.
Before running complex models with many parameters, it makes sense to run simpler models first in order to see whether you have some signal in the data.
For example, instead of using a GTR model, use a HKY model, instead of using a relaxed clock, use a strict clock, instead of using a relaxed clock on each partition, share the relaxed clock among partitions.
This way, the number of parameters can be reduced thus helping in mixing of the MCMC chains.



## Option 4: Use more informative priors (when parameters occasionally escape to far away values)

When there are parameters that are mostly sampled around the same value, but occasionally move far away, this shows up in the trace as spikes scattered around the trace, i.e. not like the desirable hairy caterpillar.
This can be kept in check by putting a more informative prior on such a parameter, for example by specifying an upper bound on the parameter.

Some models, in particular birth-death-sampling models, require strong priors on at least one of the parameters to converge because these parameters are practically unidentifiable (though in theory with a sufficient amount of data they are identifiable, but theory =/= practice).

The [help me choose](https://beast2-dev.github.io/hmc/) has more hints on choosing informative priors.


## Option 5: Reduce data by subsampling taxa/partitions

If none of the above helps, there is always the option of reducing the amount of data you include in the analysis.
This will reduce the run-time of the MCMC thus allowing for faster convergence.

When subsampling data, be sure to take random subsamples -- non-random samples tend to introduce unexpected biases.
You can run with multiple random subsamples and compare results to make sure the outcomes are robust and do not depend on a particular random sample.


