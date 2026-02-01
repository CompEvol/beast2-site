---
layout: post
title: Automatic Operator Tuning in BEAST2
tags: []
---
<p style="color:gray">1 February 2026 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

During an MCMC BEAST run, operators that have tuning parameters can be automatically updated to improve the efficiency of the operator.
The algorithm used in BEAST is based on the Robbins-Monro stochastic approximation method outlined in Andrieu & Thoms (2008).
At first sight, this algorithm is rather counterintuitive: the overall goal is to change the tuning parameter of the operator in such a way that the acceptance probability of any proposal approaches the target acceptance probability.
This target probability can differ from operator to operator, but usually is around the range 0.234 to 0.4.

## Naive implementation

Given the aim is to approximate the target acceptance probability $p^*$, one would think that just measuring the acceptance probability up to a given point in the MCMC would be an easy way to decide whether to create bolder or less bold proposals, and adapt the tuning parameter accordingly.

In BEAST, the number of accepts and number of rejects for every operator is recorded in the `m_nNrRejected` and `m_nNrAccepted` members respectively. 
Furthermore, after a certain number of samples (specified in the operator schedule's `autoOptimizeDelay` input, which is 10000 by default), `m_nNrRejectedForCorrection` and `m_nNrAcceptedForCorrection` register the rejects and accepts after some sort of burn-in.
Based on these members, it is easy to calculate the empirical acceptance probability, e.g. as $\hat{p}=$ `(m_nNrAcceptedForCorrection + 0.5) /(m_nNrRejectedForCorrection + m_nNrAcceptedForCorrection + 1.0)`.

The reason not to do this is that it is very well possible the chain is still in burn-in, which can lead to non-typical behaviour of the operator.
Furthermore, there will be stretches of state space where the MCMC operator may just be ineffective, which would distort the reject count and could lead to tuning the operator to be too conservative.
What is an alternative?

## The Robbins-Monro stochastic approximation method

This is the implementation used in BEAST2.
It treats the Metropolis Hastings acceptance probability $\alpha$ of the last sample as a "noisy observation" of the sampler's  performance and uses it to update the tuning parameter ($\sigma$) of the operator.
Here $\log(\alpha)=$ `newLogLikelihood - oldLogLikelihood + logHastingsRatio`. 
		
If the last sample had a very high $\alpha$ (accepted easily), the step size was likely too small (inefficient). 
If $\alpha$ was very low (rejected), the step size was too large.
So, we update $\sigma$ for step $n_1$ as

$$\log(\sigma_{n+1}) = \log(\sigma_n) + \gamma_n (min(\alpha_n,1) - p^*)$$

where  
* $\alpha_n$: The Metropolis Hastings  acceptance probability of the **last sample**.
$P^*$: The desired goal.
* $min(\alpha_n,1)$ the acceptance probability of the MCMC algorithms at step $n$
* $\gamma_n$: A "gain" factor that decreases over time to ensure convergence.
The gain factor in BEAST2 is $1/n$ by default, but can be changed in the operator schedule via the `transform` input.

This works since $min(\alpha_n,1)$ is the expected acceptance probability for the proposal at step $n$.
It is independent of burn-in or any other form of observed acceptance rate for the last $k$ steps.

No further parameters (like `autoOptimizeDelay`) need to be specified.

## What can go wrong

The reason I had to dive into this ancient and obscure part of the MCMC machinery is that during testing it turned out some operators did not perform well.
This could be traced back to the fact that when an operator directly rejects (returns a Hastings ratio of $-\Infty$) $\alpha$ was not updated.
Only when a finite Hastings ratio was returned was $\alpha$ recalculated.
This is fixed now by setting $\log(\alpha)=-\Infty$, and the unit tests was fixed as well.

## References

Andrieu, C., & Thoms, J. (2008). "A tutorial on adaptive MCMC." Statistics and Computing. [pdf](https://www2.stat.duke.edu/~sschmid/Courses/Stat376/Papers/AdaptiveMC/AndrieuTutorial2008.pdf)