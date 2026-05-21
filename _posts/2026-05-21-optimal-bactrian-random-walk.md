---
layout: post
title: Optimal Bactrian random walk acceptance probabilities
tags: []
---
<p style="color:gray">21 May 2026 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Gelman et al. (1997) showed that random walk operators perform optimally with an acceptance probability of 0.234 for large dimensional parameters.
For lower dimensional parameters, higher acceptance probabilities are optimal:

```
Dimension	Optimal Acceptance Rate
1			  0.441
2			  0.352
5			  0.279
10			  0.257
∞	          0.234
```

This raises the question whether this is the same for Bactrian random walk operators.
Here, we experimentally verify what makes for optimal values.
We sample from a $k$ dimensional parameter that is IID standard normal distributed, and use a Bactrian random walk operator that is modified to sample all $k$ dimensions at the same time.
The standard BEAST random walk operator randomly picks a single dimension and only does a proposal for the chosen dimension, but to be comparable to the Gelman et al. (1997) set up we use the updated version.



We vary the dimension $k$ from 1, 2, 4, 8, 16 and 32 and the target acceptance probability from 0.05 to 0.5 in steps of 0.05, giving 9 different runs.
The observed probabilities varied a bit from the target acceptance probabilities, and these empirical acceptance probabilities are used in the graph.
The plot shows the ESS for the posterior as well as for the first dimension of the parameter.
Since the difference dimensions are IID, ESSs for other dimensions can be expected to be the same.
ESSs for the posterior and the parameter are fairly close to each other.

![Operator acceptance probability results plot](/images/OptimalBactrianRandomWalkExperiment.png)


Note that the y-axis is on a log-scale.
Perhaps somewhat surprisigin is that with every doubling of the parameter dimension $k$, the ESS halves.
So, moving more dimensions at the same time does not increase ESS, probably because the window-size of the random walk operator is tuned to become smaller, so overall amount of state space traversed per step is not increased.

The plot shows that having a target acceptance probability of 0.3 works well over a wide range of parameter dimensions.
Increasing the target probability to 0.4 does not make a lot of difference.
Reducing the target probability below 0.3 or increasing above 0.4 does have material impact on observed ESSs.





## Comparison with other parameters

The experiment was repeated for some other operators, from which we conclude that:

* the standard BEAST Bactrian random walk operator (i.e. the one that only changes a single dimension) is equally efficient as the multi-dimensional Bactrian random walk operator,
* compared to the non-Bactrian random walk operator, ESSs are about 50% higher (for all dimensions) at optimal acceptance probability, and
* compared to the AVMN operator (which changes all dimensions) things are a bit mixed: parameter ESSs are about equal to the Bactrian random walk operator, but posterior ESSs are significantly lower.

## Conclusions

Bactrian random walk operators are more efficient compared to non-Bactrian random walk (50% better) and AVMN operators, but of course this assumes all parameter dimensions are IID and adding dependencies may see the AVMN operator see outperform this operator.

Hard coded acceptance probability of 0.3 is not that bad for Random Walk Operator, even for higher dimension parameters.

## References

Gelman A, Gilks WR, Roberts GO. Weak convergence and optimal scaling of random walk Metropolis algorithms. The annals of applied probability. 1997 Feb;7(1):110-20.

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>