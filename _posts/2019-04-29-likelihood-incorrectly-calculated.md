---
layout: post
title: The "Likelihood incorrectly calculated" message
tags: []
---
<p style="color:gray">29 April 2019 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>



## What does "Likelihood incorrectly calculated" mean?

BEAST runs MCMC efficiently by caching some to the calculations from the previous state. For example, if the birth-rate of the Yule prior is proposed to be changed, only the Yule prior and prior on the birth rate will change. A change of birth-rate does not affect any of the calculations of the tree likelihoods, so these do not need to be recalculated. Though this makes the MCMC much more efficient, in practice caching is a source of errors. To keep developers honest, BEAST checks every 10.000 samples whether the posterior calculation obtained while caching equals the posterior. If these two posteriors do not agree (up to a small error due to machine precision) the "Likelihood incorrectly calculated" message is produced. When errors are very small (that is, if most of the significant digits agree) this may still not be problematic, since they can be the result of adding many small errors. But after 100 such messages BEAST stops, since this is an indication it is very likely there is somethings substantially wrong.

Developer tip: In fact, if the `-Dbeast.debug=true` directive is passed to java, BEAST will do this check every 3rd sample for the first 6.000 samples. This is useful for developers that want to diagnose this problem quickly. With this flag, BEAST will stop after the first time this test fails.

Developer tip 2: If you run in an IDE, the beast.core.MCMC class has a flag `printDebugInfo` which you can set to true and which then prints out the operators and accept/reject history of the MCMC chain, which usually helps diagnose the problem. Also, you can change the posterior checking frequency in this line:

```
            if (debugFlag && sampleNr % 3 == 0 || sampleNr % 10000 == 0) {
```

Changing the 3 to 2 makes it check every second sample, and setting it to 1 makes it check every sample. I usually first find a seed that reliably triggers the problem with the checking frequency as low as possible, then set a breakpoint to start stepping through the code where the difference occurs to find the issue. It usually has to do with the `store()`, `restore()` and `requiresRecalculation()` methods not setting the right flags indicating things can be cached or not.

## What if there is a large positive posterior?

If the error message shows that the posterior is a large positive number the problem may be due to numerical instability. (Note that positive posteriors are fine in theory, since the reported posterior is actually the log of the posterior for which priors are not normalised, but when this error occurs the posterior is probably outside the range where it can be calculated in a stable manner.) What may have happened is that some of the parameters (maybe clock rates or birth rates) became very small or large resulting, which then due to numerical instability gives a large positive posteriors. You can check the trace log in `Tracer` to see which parameter escaped to an unrealistic value, and adjust the prior or the upper and lower bounds for these parameters to make sure they remain close to a value that is reasonable.

## Parameters escaping to extreme values

Large positive posteriors are an indication of scale parameters escaping to extreme values (infinity or zero), but this does not always happen. 
Parameter values can become close to the upper bound to what a `double` value can take (about 1.8e308) and still cause problems with numerics.
It is therefore good practice to check the log files for parameters that become very large (over 1e300), and consider bounding their values.

To change the bounds of a parameter, in BEAUti in the priors panel in the prior for the parameter, you can click the button with value and upper/lower bounds of the parameter. A dialog pops up where you can then change these bounds.

## Parameters are not escaping -- how can I solve this problem?

Another possibility is that there is a bug in the software. To find out which part of the posterior is causing the problem, BEAST prints out a report showing the various components that make up the posterior, with values of the latest state and that of the previous state (obtained when caching calculations).

For example, like so:

	Likelihood incorrectly calculated: 22434.79392302204 != 22245.890864308945(188.90305871309465) Operator: beast.evolution.operators.UniformOperator
	P(posterior) = 22229.65327980081 (was 22462.440037085827)  **
		P(prior) = 37514.99822738245 (was 37514.99822738245)
			P(YuleModel.t:Holo_tree) = 37550.69500540567 (was 37550.69500540567)
			P(YuleBirthRatePrior.t:Holo_tree) = 0.0 (was 0.0)
			P(GammaShapePrior.s:ITS_muscle) = -2.991983723440776 (was -2.991983723440776)
			P(GammaShapePrior.s:matK_muscle) = -0.9104975851808744 (was -0.9104975851808744)
			P(GammaShapePrior.s:trnLF_muscle) = -0.3950317780000696 (was -0.3950317780000696)
			P(PropInvariantPrior.s:ITS_muscle) = 0.0 (was 0.0)
			P(PropInvariantPrior.s:matK_muscle) = 0.0 (was 0.0)
			P(RateACPrior.s:ITS_muscle) = -2.027576474423646 (was -2.027576474423646)
			P(RateACPrior.s:matK_muscle) = -3.3420863110806613 (was -3.3420863110806613)
			P(RateACPrior.s:trnLF_muscle) = -3.5503387840350413 (was -3.5503387840350413)
			P(RateAGPrior.s:ITS_muscle) = -2.8018974392067912 (was -2.8018974392067912)
			P(RateAGPrior.s:matK_muscle) = -3.4264022719415315 (was -3.4264022719415315)
			P(RateAGPrior.s:trnLF_muscle) = -2.9406542401232456 (was -2.9406542401232456)
			P(RateATPrior.s:ITS_muscle) = -2.0799467915877106 (was -2.0799467915877106)
			P(RateATPrior.s:matK_muscle) = -0.8833869384226044 (was -0.8833869384226044)
			P(RateATPrior.s:trnLF_muscle) = -1.6680239598979003 (was -1.6680239598979003)
			P(RateCGPrior.s:ITS_muscle) = -1.591803743802282 (was -1.591803743802282)
			P(RateCGPrior.s:matK_muscle) = -1.1618796007032353 (was -1.1618796007032353)
			P(RateCGPrior.s:trnLF_muscle) = 22.212387147091274 (was 22.212387147091274)
			P(RateGTPrior.s:ITS_muscle) = -1.775371545218943 (was -1.775371545218943)
			P(RateGTPrior.s:matK_muscle) = -2.5928054732911754 (was -2.5928054732911754)
			P(RateGTPrior.s:trnLF_muscle) = -3.3289713181799545 (was -3.3289713181799545)
			P(MeanRatePrior.c:Holo_clock) = 0.0 (was 0.0)
			P(ucldStdevPrior.c:Holo_clock) = -1.1015692392075245 (was -1.1015692392075245)
			P(Crown_Holo.prior) = -15.447117654446167 (was -15.447117654446167)
			P(Root_Holo.prior) = -3.891820298110627 (was -3.891820298110627)
			P(Rooting_tree.prior) = 0.0 (was 0.0)
		P(likelihood) = -15285.344947581641 (was -15052.558190296622)  **
			P(treeLikelihood.ITS_muscle) = -5897.0963242519865 (was -5897.0963242519865)
			P(treeLikelihood.matK_muscle) = -6455.213158007265 (was -6455.213158007265)
			P(treeLikelihood.trnLF_muscle) = -2933.035465322392 (was -2933.035465322392)
	At sample 49190000
	Likelihood incorrectly calculated: 22462.440037085827 != 22229.65327980081(232.78675728501912) Operator: beast.evolution.operators.Uniform


The entries marked with a double asterisk are the components that are probably causing a problem. In the example above, the prior is correctly calculated, but something is wrong with the likelihood, though it is not quite clear which of the likelihoods is the cause of this. 

If you see this happening, and the component is specific to a particular BEAST package, it is probably best to contact the developer of that package.

