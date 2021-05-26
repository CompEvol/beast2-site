---
layout: post
title: Bactrian proposals
tags: []
---
<p style="color:gray">26 April 2021 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Most operators that apply to continuous parameters in BEAST draw from a continuous distribution centered around zero. Bactrian operators introduced by [Yang & Rodríguez, 2013](https://doi.org/10.1073/pnas.1311790110) also draw from a distributions centered at zero, but aim to make sure the proposal, if accepted, leads to substantial change in parameter value.

We found that Bactrian operators in BEAST are about 15% more efficient in terms of [ESS](https://www.beast2.org/what-is-ess/) per number of states for relaxed clock models ([Douglas et al, 2021](https://doi.org/10.1371/journal.pcbi.1008322)). Bactrian operators take the same amount of time as standard operators, but tend to result in better mixing.



![Bactrian ESS vs Standard ESS](/images/BactrianESS.png)

This graph shows the ESS over 100 runs for 50 taxa sampled through time with standard operators and Bactrian versions of them with the same weights.

## Bactrian versions of random walk operators

The image below shows the difference between a normal distribution in red, which puts much of the probability mass near zero and thus has a tendency to propose small steps. In contrast, the Bactrian distribution in green is the sum of two normal distributions, one shifted up, the other shifted down. It puts most of the probability mass off-centre, so has a tendency to propose larger steps.

![BactrianDistribution.svg](/images/BactrianDistribution.svg)

<tiny style="color:gray">(image produced with https://www.desmos.com/calculator)</tiny>

It is still a symmetric distribution, so for random walk operators this means the Hastings ratio is 1. There is a parameter for the shape of the distribution (`m` for Gaussian Bactrian kernels, which defaults to 0.95, shown to be a good value in [Yang & Rodríguez, 2013](https://doi.org/10.1073/pnas.1311790110)), and a window size that is auto-optimised, just like the window size for standard random walk operators -- see [BactrianRandomWalkOperator](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/src/beast/evolution/operators/BactrianRandomWalkOperator.java).

## Bactrian versions of real-parameter operators

Scale parameters have a range of 0 to infinity, and random walk operators are less efficient in sampling them than scale operators. By converting the scale parameter to the range -Infinity to +Infinity by taking the log of its value, we can still perform a Bactrian random walk proposal. After the proposal, we transform back by taking the exponential of the proposed value, and compensating for this in the Hastings ratio -- see [BactrianScaleOperator](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/src/beast/evolution/operators/BactrianScaleOperator.java).

Likewise, for parameter `x` with a restricted range `[lower,upper]`, we can use a transformation `y=(upper - x) / (value - x)` do the proposal, and transform back. Again, this needs to be compensated in the Hastings ratio -- see [BactrianIntervalOperator](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/src/beast/evolution/operators/BactrianIntervalOperator.java)

There is also a Bactrian versions of delta exchange operator, which ensures the sum of all values in a multi-dimensional parameter remains constant -- see [BactrianDeltaExchangeOperator](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/src/beast/evolution/operators/BactrianDeltaExchangeOperator.java).


## Bactrian versions of tree operators

We introduce a number of new tree operators that benefit from Bactrian kernels.

The `Uniform` operator in BEAST moves the height of an internal node uniformly in the interaval between its closest child and its parent. As the operator name suggests, it does that by drawing from a uniform distribution. The `BactrianIntervalOperator` performs a proposal in the same interval, but using a Bactrian kernel instead of a uniform kernel.

The `TipDatesRandomWalker` operator performs a random walk for the node height of a leaf, drawing from a uniform or Gaussian distribution (if `useGaussian` is set to true) and tuneable window size. The `BactrianTipDatesRandomWalker` similarly draws from a uniform Bactrian distribution or normal Bactrian distribution if `useGaussian="true"`.

The `SubtreeSlide` operator moves an internal node along branches of a tree, potentially changing the topology. The size of the interval is by default drawn from a Gaussian distribution, or from a uniform distribution if `useGaussian="false"`. The `BactrianSubtreeSlide` does the same, but drawing the interval size from a Bactrian normal distribution, or a Bactrian uniform distribution if `useGaussian="false"`.

## Available kernel distributions

Bactrian operators sample from a `kernel distribution` from which a proposal is created.
The following kernel distributions are currently implemented in the BEASTLabs package, many of them propossed in [Thawornwattana et al, 2018](https://doi.org/10.1214/17-BA1084).

| Name    | Distribution         | Bactrian? |
|---------|----------------------|-----------|
| uniform |  uniform distribution | no |
| normal | normal distribution | no |
| laplace | Laplace distribution | no |
| t4 |   T-distribution with 4 degrees of freedom | no |
| cauchy | Cauchy distribution  | no |
| bactrian_normal | normal distribution  | yes |
| bactrian_laplace | Laplace distribution | yes |
| bactrian_triangle | triangle shaped distribution | yes |
| bactrian_uniform | uniform distribution | yes |
| bactrian_t4 |  T-distribution with 4 degrees of freedom | yes |
| bactrian_cauchy | Cauchy distribution | yes |
| bactrian_box | box-shaped distribution | yes |
| bactrian_airplane | airplane distribution | yes |
| bactrian_strawhat  | strawhat distribution |yes |
|--------------------|-----------------------|----|

The default kernel distribution is the Bactrian normal distribution.

The kernel distribution has class `beast.evolution.operators.KernelDistribution$Bactrian`

Bactrian has the following inputs:

* mode (mode): selects the shape of the distribution, choose one of uniform, 			normal,			laplace,			t4, 			cauchy,			bactrian_normal, 			bactrian_laplace,			bactrian_triangle, 			bactrian_uniform, 			bactrian_t4, 			bactrian_cauchy,			bactrian_box, 			bactrian_airplane, 			bactrian_strawhat  (optional, default: bactrian_normal)
* m (Double): standard deviation for Bactrian distribution. Larger values give more peaked distributions. The default 0.95 is claimed to be a good choice (Yang 2014, book p.224). (optional, default: 0.95)
* a (Double): parameter for box, airplane and strawhat kernels, ignored otherwise (optional, default: 0.0)




## Bactrian operator schedule

The easiest way to use Bactrian operators is to use the `BactrianOperatorSchedule` from the BEASTLabs package, which automatically replaces standard operators with Bactrian variants. The table below shows the mappings.


| Standard      | Bactrian              |
| --------------|-----------------------|
| ScaleOperator | BactrianScaleOperator | 
| RealRandomWalkOperator | BactrianRandomWalkOperator | 
| Uniform | BactrianNodeOperator | 
| UniformOperator | BactrianIntervalOperator | 
| DeltaExchangeOperator | BactrianDeltaExchangeOperator | 
| TipDatesRandomWalker | BactrianTipDatesRandomWalker | 
| UpDownOperator | BactrianUpDownOperator | 
| SubtreeSlide  | BactrianSubtreeSlide  |
| --------------|-----------------------|


### In BEAUti

Set up your analysis as per usual, then select the menu `View/Show operators panel`.
The operators panel appears and in at the bottom of the list of operators, there is an entry for the operator schedule. Select `Bactrian Operator Schedule` from the drop down box, and Bactrian operators will be used once BEAST is run. 

```
Note, the XML will still contain the standard operators.
```

Once BEAST is started, the `BactrianOperatorSchedule` will replace operators.


### Edit the XML

Another way of adding the `BactrianOperatorSchedule` is by editting the XML and adding

```
<operatorschedule spec="beast.evolution.operators.BactrianOperatorSchedule"/>
```

in the `run` element. Make sure there is not already an `operatorschedule` present (delete any if necessary).



## References

Douglas J, Zhang R, Bouckaert R. Adaptive dating and fast proposals: Revisiting the phylogenetic relaxed clock model. PLoS computational biology. 2021 Feb 2;17(2):e1008322.
[doi: 10.1073/pnas.1311790110](https://doi.org/10.1073/pnas.1311790110)

Yang Z, Rodríguez CE. Searching for efficient Markov chain Monte Carlo proposal kernels. Proceedings of the National Academy of Sciences. 2013 Nov 26;110(48):19307-12.
[doi: 10.1073/pnas.1311790110](https://doi.org/10.1073/pnas.1311790110)

Thawornwattana Y, Dalquen D, Yang Z. Designing simple and efficient Markov chain Monte Carlo proposal kernels. Bayesian Analysis. 2018;13(4):1037-63.
[doi: 10.1214/17-BA1084](https://doi.org/10.1214/17-BA1084)
