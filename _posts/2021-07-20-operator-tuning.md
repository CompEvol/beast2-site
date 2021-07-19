---
layout: post
title: Operator tuning
tags: []
---
<p style="color:gray">20 July 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

Operators can have tuneable parameters, such as the scale factor for the scale operator and window size for the real random walk operator. If the operator is allowed to optimise this parameter (when the `optimise` attribute is set to `true`, as is the default for most operators), this parameter is changed during the MCMC to get an optimal balance between boldness of them move and acceptance rate. For many operators, the target acceptance probability is 0.234, but for [Bactrian operators](http://www.beast2.org/2021/04/26/bactrian-proposals.html) it is usually a bit higher at around 0.4.


### autoOptimize

The way tuneable parameters are changed is determined by the operator schedule. The default operator schedule has the `autoOptimize` input that determines whether to automatically optimise operator settings (true by default). 

### autoOptimizeDelay

If `autoOptimize` is true, it does not start to optimise immediately, but waits a number of MCMC samples. The `autoOptimizeDelay` input determines the number of samples to skip before auto optimisation kicks in (default=10000). This is to ensure that most of burn-in is skipped, because this is time during the MCMC that proposals are not typical for the stationary part of the chain. It may be necessary to increase `autoOptimizeDelay` for large analyses. When there are many suggestions in the operator report at the end of the MCMC run, this is a sign that changing the delay can be beneficial.

### transform

After `autoOptimizeDelay` sampels are skipped, for every operator the number of accepted and rejected proposals are tracked and the parameter is tuned based on

* the desired operator acceptance probability
* the number of accepts and rejects so far
* logAlpha, which is the difference between the log posterior of previous state and the log posterior of proposed state + Hastings ratio
* the `transform` for the operator schedule. This can be `none`, `log`, or `sqrt` and is `none` by default.
 
To guarantee that the MCMC produces a proper sample, tuning should gradually be reduced to the point of not changing parameters, so the proposed change is proportional to 1/number of proposals (where number of proposals = number of accepts  + number of rejects). However, since 1/x drops down too quick sometimes, it is possible to use a log transform, that is use 1/log(x), or a square root transform (use 1/sqrt(x)). The latter is not recommended, since it may nor drop down fast enough, but the log transform should be OK for difficult to converge analyses.


### Operator report

At the end of an MCMC run, an operator report is printed that looks someting like this:

```
Operator                                              Tuning    #accept    #reject      Pr(m)  Pr(acc|m)
ScaleOperator(YuleBirthRateScaler.t:dna)             0.29041       1949       2021    0.03989    0.49093 Try setting scaleFactor to about 0.084
ScaleOperator(YuleModelTreeScaler.t:dna)             0.68394        393       3580    0.03989    0.09892 Try setting scaleFactor to about 0.827
ScaleOperator(YuleModelTreeRootScaler.t:dna)         0.71922        421       3587    0.03989    0.10504 
Uniform(YuleModelUniformOperator.t:dna)                    -      10305      29590    0.39894    0.25830 
SubtreeSlide(YuleModelSubtreeSlide.t:dna)            0.28411        541      19587    0.19947    0.02688 Try decreasing size to about 0.142
Exchange(YuleModelNarrow.t:dna)                            -        629      19238    0.19947    0.03166 
Exchange(YuleModelWide.t:dna)                              -          7       3998    0.03989    0.00175 
WilsonBalding(YuleModelWilsonBalding.t:dna)                -          6       3907    0.03989    0.00153 
ScaleOperator(KappaScaler.s:dna)                     0.57917         23         91    0.00133    0.20175 
DeltaExchangeOperator(FrequenciesExchanger.s:dna)    0.03331         58         70    0.00133    0.45313 Try setting delta to about 0.064

     Tuning: The value of the operator's tuning parameter, or '-' if the operator can't be optimized.
    #accept: The total number of times a proposal by this operator has been accepted.
    #reject: The total number of times a proposal by this operator has been rejected.
      Pr(m): The probability this operator is chosen in a step of the MCMC (i.e. the normalized weight).
  Pr(acc|m): The acceptance probability (#accept as a fraction of the total proposals for this operator).
```

In case the actual acceptance probability for an operator is not close enough to the target, there can be some suggestions to change the target. If you plan to run another replicate of the analysis, this should be done in the XML, or in BEAUti in the operators panel (use menu View/Show operators panel to show the list of operators).

Above, it is suggested to change the scaleFactor for the birth rate scaler to 0.084, so we can edit the XML and add the scale factor attribute to the birth rate scaler, giving

```
 <operator id="YuleBirthRateScaler.t:dna" spec="ScaleOperator" parameter="@birthRate.t:dna" weight="3.0" scaleFactor="0.084"/>
```

Alternatively, we can open the XML in BEAUti, go the the operators panel, open the birth rate scale operator and change the default value of 0.75 to 0.084.


If you plan to resume a run, you should edit the appropriate entry in the state file that you plan to use to resume the run. The state file contains information about acceptance and rejections for operators, as well as the value of the tuneable parameter (if there is one).

```
{"operators":[
{"id":"YuleBirthRateScaler.t:dna","p":0.2904106729716514,"accept":1949,"reject":2021,"acceptFC":947,"rejectFC":1796,"rejectIv":0,"rejectOp":0},
{"id":"YuleModelTreeScaler.t:dna","p":0.6839417140539132,"accept":393,"reject":3580,"acceptFC":298,"rejectFC":2421,"rejectIv":0,"rejectOp":0},
{"id":"YuleModelTreeRootScaler.t:dna","p":0.7192202184892902,"accept":421,"reject":3587,"acceptFC":346,"rejectFC":2405,"rejectIv":1064,"rejectOp":1064},
{"id":"YuleModelUniformOperator.t:dna","p":"NaN","accept":10305,"reject":29590,"acceptFC":7229,"rejectFC":20539,"rejectIv":0,"rejectOp":0},
{"id":"YuleModelSubtreeSlide.t:dna","p":0.28411428302239805,"accept":541,"reject":19587,"acceptFC":478,"rejectFC":13456,"rejectIv":8205,"rejectOp":8205},
{"id":"YuleModelNarrow.t:dna","p":"NaN","accept":629,"reject":19238,"acceptFC":480,"rejectFC":13292,"rejectIv":0,"rejectOp":0},
{"id":"YuleModelWide.t:dna","p":"NaN","accept":7,"reject":3998,"acceptFC":3,"rejectFC":2739,"rejectIv":2070,"rejectOp":2070},
{"id":"YuleModelWilsonBalding.t:dna","p":"NaN","accept":6,"reject":3907,"acceptFC":3,"rejectFC":2682,"rejectIv":1165,"rejectOp":1165},
{"id":"KappaScaler.s:dna","p":0.5791733977330605,"accept":23,"reject":91,"acceptFC":17,"rejectFC":64,"rejectIv":0,"rejectOp":0},
{"id":"FrequenciesExchanger.s:dna","p":0.03330741595312767,"accept":58,"reject":70,"acceptFC":35,"rejectFC":57,"rejectIv":0,"rejectOp":0}
]}
```

This is the meaning of the individual entries:

* "id" identifier of the operator as found in the XML
* "p": value of the tuneable parameter so far
* "accept": total number of accepted proposals, including those during auto optimisation delay
* "reject": total number of rejected proposals
* "acceptFC": number of accepts after optimisation delay, used for tuning
* "rejectFC": as acceptFC but rejects
* "rejectIv": rejected proposals because likelihood is infinite
* "rejectOp": rejected proposals because operator failed (sub-group of rejectIv)

To continue, you could edit the state file and replace 

```
{"id":"YuleBirthRateScaler.t:dna","p":0.2904106729716514,"accept":1949,"reject":2021,"acceptFC":947,"rejectFC":1796,"rejectIv":0,"rejectOp":0},
```

with

```
{"id":"YuleBirthRateScaler.t:dna","p":0.084,"accept":1949,"reject":2021,"acceptFC":947,"rejectFC":1796,"rejectIv":0,"rejectOp":0},
```

and then resume an MCMC run using this state file.
