---
layout: post
title: Adaptive Metropolis Coupled MCMC(MC3) works!
tags: []
---
<p style="color:gray">16 January 2020 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

[A while ago](/2016/09/27/metropolis-coupled-mcmcmc3-works.html), I was wondering whether Metropolis coupled MCMC (aka MCMCMC, MC3, or parallel tempering) is actually useful for BEAST, and in particular:

* Can get us out of local optima, where MCMC by itself has trouble?
* Can produce better effective sample size (ESS) per computer cycle?

And the answer is `yes` to both of these. The dataset DS1 has proven to be problematic for BEAST using the standard set of operators (H&ouml;hna and Drummond, 2011; Maturana Russel et al., 2018), but MC3 has no trouble getting convergence. By implication, that answers the second question as well, but a more practical question perhaps would have been whether the ESS per wall clock second would improve. Nicola M&uuml;ller did some experiments, and with delta temperature of 0 (so all chains are 'cold' chains), an thus an acceptance rate of 1, the ESS goes up roughly in proportion with the number of chains (M&uuml;ller &amp; Bouckaert, 2020). So, MC3 can certainly help bring down the total time required to wait for a BEAST run.

In the past few years, there have been a number of analyses that turned out to be badly mixing, and where the BEAST operators were just not smart enough to traverse the state space efficiently. For those cases, MC3 did help on occasion, so though MCMC certainly requires less computation, and I think should be used when possible, MC3 is a useful tool in case MCMC does not converge, and can help speed up ESS per unit of time.


There is now a new implementation of MC3 in the CoupledMCMC package, which is more convenient than the previous one, and automatically optimises the temperature schedule. Instead of specifying a difference in temperatures, which is dependent on the data and kind of analysis you are running, you can just specify a target switch probability. The default of 0.234 has been argued for (Kone and Kofke, 2005; Atchad&eacute;  et al., 2011), so in practice there is no need to do any tuning at all.



## How to set up your BEAST2 analysis to run with coupled MCMC/parallel tempering 

Make sure the CoupledMCMC package is [installed](/managing-packages/).

### By using BEAUti

Start BEAUti and from the `File/Templates` menu select the `CoupledMCMC` template. This sets up a Standard template but with MC3 instead of MCMC. If you want to run a different analysis than a standard one, there are the following options below.

### By using the conversion app

After you installed the `CoupledMCMC` package (version 0.1.5 or better), the `MCMC2CoupledMCMC` app becomes available in the app launcher.

>
> 1. Create MCMC analysis in BEAUti with any of the available templates, save as `mcmc.xml`
> 
> Now there are 2 ways to proceed:
> 
> 2a. from a terminal, run
>
>  /path/to/beast/bin/applauncher MCMC2CoupledMCMC -xml mcmc.xml -o mc3.xml
>
> This creates a file `mc3.xml` containing a CoupledMCMC analysis with the same model/operators/loggers etc as the `mcmc.xml` analysis.
>
> 2b. from BEAUti, use menu `File > Launch apps`, select `MCMC to Coupled MCMC converter` from the available apps, fill in form and click OK
>

### By editing an XML file in a text editor

In order to set up a pre-prepared xml to run with coupled MCMC, open the  `*.xml` and change the MCMC line in the xml.

To do so, go to the line with:

```
<run id="mcmc" spec="MCMC" chainLength="....." numInitializationAttempts="....">
```

To have a run with coupled MCMC, we have to replace that one line with:

```
<run id="mcmc" spec="beast.coupledMCMC.CoupledMCMC" chainLength="10000000" chains="4" target="0.234" logHeatedChains="true" deltaTemperature="0.1" optimise="true" resampleEvery="1000" >
```
* `chainLength` (Long): Length of the MCMC chain i.e. number of samples taken in main loop (required)
* `chains` (Integer):  defines the number of parallel chains that are run. The first chain is the one that explores the posterior just like a normal MCMC chain. All other chains are what's called *heated*. This means that MCMC moves of those chains have a higher probability of being accepted. While these heated chains don't explore the posterior properly, they can be used to propose new states to the one cold chain. (optional, default: 2)
* `target` (Double): target acceptance probability of swaps (optional, default: 0.234)
* `logHeatedChains` (Boolean): if true, log files for heated chains are also printed (optional, default: false)
* `resampleEvery` (Integer): number of samples in between resampling (and possibly swappping) states (optional, default: 100)
* `tempDir` (String): directory where temporary files are written (optional, default: )
* `deltaTemperature` (Double): temperature difference between the i-th and the i-th+1 chain (optional, default: 0.1)
* `maxTemperature` (Double): maximal temperature of the hottest chain (optional)
* `optimise` (Boolean): if true, the temperature is automatically optimised to reach a target acceptance probability (optional, default: true)
* `optimiseDelay` (Integer): after this many epochs/swaps, the temperature will be optimized (if optimising is set to true) (optional, default: 100)
* `preSchedule` (Boolean): if true, how long chains are run for is scheduled at the beginning (optional, default: true)
and it has all the inputs of the normal MCMC, such as `state`, `operator`, `distribution`, etc.


### References:

Atchad&eacute; , Y. F., Roberts, G. O., and Rosenthal, J. S. (2011). Towards optimal scaling of Metropolis-coupled markov chain Monte Carlo. Statistics and Computing, 21(4), 555–568.

H&ouml;hna, S. and Drummond, A. J. (2011). Guided tree topology proposals for Bayesian phylogenetic inference. Systematic biology, 61(1), 1–11.

Kone, A. and Kofke, D. A. (2005). Selection of temperature intervals for parallel-tempering simulations. The Journal of chemical physics, 122(20), 206101

Maturana Russel, P., Brewer, B. J., Klaere, S., and Bouckaert, R. R. (2018). Model selection and parameter inference in phylogenetics using nested sampling. Systematic biology, 68(2), 219–233.

Mueller, N. and Bouckaert, R. (2020). Adaptive parallel tempering for BEAST 2. BioRxiv. doi: [https://doi.org/10.1101/603514](https://doi.org/10.1101/603514)
