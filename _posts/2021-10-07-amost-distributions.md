---
layout: post
title: Almost Distributions
tags: []
---
<p style="color:gray">7 October 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

For complex analyses with at large number of priors it can be hard to find a starting state that satisfies all constraints, so one or more of the priors return a negative infinity at the start of the MCMC run and BEAST will not start.

The [AlmostDistributions](https://github.com/rbouckaert/AlmostDistribution) package contains a number of distributions that behave mostly like standard distributions, but are a bit more lenient towards the constraints: instead of returning negative infinity if a value is out of the range of support, a large penalty value is returned instead. This can sometimes help a run take off. Over time, the MCMC will move parameter or tree clades into range.

## AlmostUniform

The `AlmostUniform` distribution is a parametric distribution that can be used as alternative for the `Uniform` distribution in parameter priors or for node age calibrations. For values between the lower and upper bound, the `AlmostUniform` distribution behaves the same as the `Uniform` distribution, but for values outside the range where the `Uniform` distribution returns negative infinity for the log-density, the `AlmostUniform` distribution returns a small penalty value. The value is calculated to encourage the value to move towards the range between lower and upper bound when using MCMC, and is calculated as  

```-penalty * (x-centre) * (x-centre)```

where `penalty` a user defined penalty values (default 10000), `centre` is the middle of the interval defined by lower and upper bound, and `x` is the value for which the log-density is calculated. Initially, the value of `x` may be outside the range, but during the MCMC, the value of `x` will move towards the desired bounds and once `x` is between lower and upper bound it will be practically impossible to escape from the desired range (though in theory it can still happen).

* The `AlmostUniform` distribution can handle bounds that are infinite, but in that case the centre will be defined as the bound that is finite.

* The `cummulativeProbability` and `inverseCumulativeProbability` methods return the same values as the `Uniform` distribution.


## AlmostNormal

The `AlmostNormal` distribution is a parametric distribution that behaves like the `Normal` distribution when `x` is in the 95% HPD of the normal distribution. For values of `x` more than two standard deviations from the mean, a penalty similar to that of the `AlmostUniform` distribution is returned for the `logdensity` method but with the mean of the normal distribution as centre.


* The `cummulativeProbability` return 0 for `x` more than two standard deviations smaller than the mean, and 1 for `x` more than two standard deviations larger than the mean. In between, it returns the cumulative probability of the normal but normalised to ensure it is 0 at `mean-2*sigma` and 1 at `mean+2*sigma`.

* The `inverseCumulativeProbability` methods only returns values between `mean-2*sigma` and `mean+2*sigma`.


## AlmostLogNormalDistributionModel

The `AlmostLogNormalDistributionModel` is a parametric distribution that behaves like the `LogNormalDistributionModel` distribution, but only in the 0.025 to 0.975 probability range of the log normal with the same parameters. Outside that range, it returns a small log density, calculated as

```
-penalty * (lower - x)
```

when `x` less than the lower bound and

```
-penalty * (x - upper);
```

when `x` higher than the upper bound.

## AlmostMRCAPrior

The `AlmostMRCAPrior` can be used as alternative for the `MRCAPrior`, which allows you to make clades monophyletic. Unlike the `MRCAPrior`, the `AlmostMRCAPrior` will not return -Infinity when the clade is not monophyletic, but produce a large penalty value instead. This can be handy when there are many tree constraints and finding a valid starting tree is hard. A previous <a href="/2021/04/12/constraining-trees.htmll">post</a> on constraining trees already explains how to use the `AlmostMRCAPrior`.

## AlmostMultiMRCAPrior

The `AlmostMultiMRCAPrior` extends the [`MultiMRCAPriors`](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/src/beast/math/distributions/MultiMRCAPriors.java), and can handle multiple monophyletic constraints. When one or more `AlmostMRCAPrior`s are used instead of `MRCAPrior`s, the associated clades may violate monophyly constraints but still return a finite (though very small) log probability.



To use [AlmostDistributions](https://github.com/rbouckaert/AlmostDistribution) see
[installation instructions](https://github.com/rbouckaert/AlmostDistributions/blob/master/README.md)

## Reference

Remco Bouckaert. AlmostDistributions package for BEAST 2. 2021 DOI: 10.5281/zenodo.5348449 [![DOI](https://zenodo.org/badge/109171998.svg)](https://zenodo.org/badge/latestdoi/109171998)