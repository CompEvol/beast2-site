---
layout: post
title: Punctuated vs gradual evolution -- the gamma spike model
tags: []
---
<p style="color:gray">1 May 2025 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>


Te debate between [punctuated equilibrium](https://en.wikipedia.org/wiki/Punctuated_equilibrium) and [gradual evolution](https://en.wikipedia.org/wiki/Phyletic_gradualism) is a central topic in evolutionary biology.
The standard clock models in BEAST2 including the popular strict and relaxed clocks assume gradual evolution alone. 
Even though the relaxed clock allows rate variation among branches, it does not allow estimation of spikes in evolution on the tree.

Now, there is new model in BEAST2 that allows you to test how much your data supports gradual or punctuated evolution: the gamma spike model.
Incorrectly assuming gradual evolution alone can result in biased time estimates.

## The gamma spike model

The gamma spike model is a clock model that allows a spike in evolution at every branching event in the tree.
These spikes are all independent of each other, but are sampled from a gamma distribution -- hence the name of the model.
Furthermore, there is a (typically log-normal, but that can be changed) relaxed clock representing gradual evolution.
So, for a given branch $$i$$, the amount of evolution is the sum of the contribution of the branch rate ($$r_i$$) times branch length ($$\tau_i$$) and that of the spike ($$s_i$$), that is $$r_i\tau_i + s_i$$ -- see image bottom right.
Douglas et al (2025)) show that spikes are identifiable, which is perhaps surprising since every branch has two independent parameters that determines that overall amount of evolution on a branch.

![clockmodels](/images/gammaspikes.png)

Note that the spike is in units of expected fraction of mutations per site, while the branch rate times branch length is in expected mutations per site per unit of time.
Keep this in mind when setting up the priors.

The implementation in BEAST 2 is set up so that all spikes can be switched on or off, allowing you to test whether there is punctuated evolution present in your data.

## When to use the gamma spike model

* If you want to test whether your data is spike like
* If you have reason to believe there is variation of rates between different branches in the tree. 

The gamma spike model is a clock model for all situations since it uses model averaging.
When the model indicator is estimated, the gamma spike model will fall back on a relaxed clock model if there is no punctuated evolution in your data.
There is not much overhead in using the gamma spike model when this is the case.

However, if there is punctuated evolution, this can affect time estimates in the tree, and the model will show where most of these spikes are.


## Practicalities: tutorial + hypothesis testing + examples

There is a [tutorial](https://github.com/jordandouglas/GammaSpikeModel) on how to set up an analysis using the gamma-spike-model (tl;dr -- install the `gammaspike` package, select `Gamma Spike Relaxed Clock` in the clock model panel, adjust priors to taste).

Testing the hypothesis that there is gradual evolution alone vs spikes in evolution can be done by stochastic variable selection: the `useSpikeModel` flag will be set to 1 if spikes are used and set to 0 when only gradual evolution is involved.

The tutorial has hints on speeding up convergence specific to the model.
Also, there are XML examples files illustrating how to combine the gamma spike model with stubs -- unobserved splits in the tree into unsampled clades, which impact the amount of spike for a branch.

## References

Douglas, J., Bouckaert, R., Harris, S., Carter, C., & Wills, P. (2025). 
Evolution is coupled with branching across many granularities of life.
Royal Society Proceedings B. In press 2025 [preprint](https://www.biorxiv.org/content/10.1101/2024.09.08.611933.full.pdf)


<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>