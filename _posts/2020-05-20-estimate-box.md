---
layout: post
title: Should I click the estimate box in BEAUti?
tags: []
---

<p style="color:gray">20 May 2020 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

The short answer is: if you have to ask, then most likely not.

There are some notable exceptions:

## Site model tab

### Substitution rate

If you have multiple partitions, then often there is variation of rates between the partitions, which can be captured by giving each partition its own relative rate. Clicking the `estimate` box for Substitution rate will achieve this. 

As a side effect (by default), the `Fix mean mutation rate` box will then be selected, which ensures that the substitution rate per site (so taking the length of partitions in account) will be 1 on average.


### Proportion invariable sites

If you want to include a proportion invariable sites in your site model, you should select the `estimate` box, and provide a positive starting value for the proportion.

![BEAUti site model panel](/images/estimateBoxSiteModel.png)


## Clock model tab

BEAUti will for most cases be able to find out whether the clock rate should be estimated or not, based on whether there are tip dates and/or priors on clade ages available in the analysis. So, by default, the estimate box is greyed out and you cannot change it.

If you do not have tip dates or age priors, but there is other timing information, in particular if you have a prior on the clock rate based on another independent analysis, you can enable the estimate box by selecting the `Mode => Automatic set clock rate` menu. Then, you can select the estimate box for clock rate, and specify its prior in the priors tab.


![BEAUti site model panel](/images/estimateBoxClockModel.png)


## Priors tab

Parametric distributions (like exponential, gamma, log normal, etc) come with parameters, and each of them can be estimated using so called hyper-priors (priors on prior parameters).


![BEAUti site model panel](/images/estimateBoxPriors.png)


If you click the estimate box for such a parameter, a hyperprior on that parameter will be added to the list of priors, which means you need to specify another prior. The effect will be that the variance of the prior will increase.
