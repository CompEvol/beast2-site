---
layout: post
title: Is this the prior you want?
tags: []
---

<p style="color: gray;">March 2023 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

When putting a time calibration on a node in a tree, for example, through an MRCA prior or Clade Age fossil prior, it is even more important than usual to sample from the prior to make sure that the age distribution you define on the node is indeed the prior you intended to use. 
Here is why: time calibrations are interacting with other tree priors, like the Yule. 

The difference between the node calibration and the actual prior distribution for the node can be substantial. 
To demonstrate, open BEAUti, import the `examples/nexus/dna.nex` file from the `BEAST.app` directory into the Standard template, and add an MRCA prior on the root with log-normal distribution (mean=100.0, mean in real space checked, sigma=0.5). 
Leaving the rest to default (Yule tree prior, JC69 substitution model, strict clock, all with (bad) default hyperpriors). Here is the distribution of the root height sampled from the prior:

![rootheight](/images/miscalibration.svg)


The green distribution is a log-normal with mean of 100, while the red distribution has a mean of about 63.
This means there is a discrepancy of more than 50% between actual and intended distribution.

In this case, a contributing factor is the variance of the calibration: its 95% HPD is 22 to 200, so about an order of magnitude between the lower and upper bound.

Reducing the variance by setting sigma to 0.15 gives a 95% HPD interval 72 to 131, and the discrepancy is greatly reduced:
![rootheight](/images/miscalibration2.svg)
However, the sampled mean is about 95.8, even though we expect 100.

Reducing the variance by setting sigma to 0.05 gives a 95% HPD interval of 90 to 110, and the mean becomes 99.5 and the discrepancy almost vanishes:
![rootheight](/images/miscalibration3.svg)

So, reducing the variance is one way to reduce this effect. 
But since there is no way to know beforehand, sampling from the prior is the recommended way to ensure you know what prior you are actually using.

## Fixing the differences

When differences are small, you may decide to ignore them.

If differences are unacceptable large, you can change the parameters of the time calibration to fit your prior believe, which requires a bit of experimentation and sampling from the prior.
For example, setting the mean to 100/63=158.7 in the example above gives the desired mean of 100.

However, a better solution is to use a calibrated version of the tree prior (Heled & Drummond, 2012).
Currently, calibrated version of the Yule or Birth death priors are available.
Even with calibrated priors, it is still good practice to sample from the prior and verify the calibration behaves like intended to make sure there are no unexpected interactions between priors (and possibly between various calibrations).


## More about priors

As mentioned above, the default priors are pretty bad (in particular the clock and birth rate prior) contributing to the problem.
The [help me choose](https://beast2-dev.github.io/hmc/#priors) site has more information about setting priors properly.


## Acknowledgements

Thanks to Michael Matschiner for inspiring this post.

## Reference

Heled J, Drummond AJ (2012) Calibrated Tree Priors for Relaxed Phylogenetics and Divergence Time Estimation. Systematic Biology 61(1):138-149. <a href="http://doi.org/10.1093/sysbio/syr087">DOI:10.1093/sysbio/syr087</a>