---
layout: post
title: Developing efficient BEAST operators
tags: []
---
<p style="color:gray">24 May 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

When developing new operators for BEAST there are two goals

* making correct proposals (see [developer guide](https://github.com/rbouckaert/DeveloperManual) on how to test them)
* making them efficient.

Here we consider efficiency of proposals, for which there are three factors to take into account:

* the boldness of the proposal: bigger jumps may mean more efficient exploration of the state space.
* acceptance probability: higher acceptance may mean more efficient exploration, but is often only possible with not very bold proposals.
* the computational cost in generating a proposal and then calculating the updated posterior.



## Bolder proposals

[Bactrian proposals](http://www.beast2.org/2021/04/26/bactrian-proposals.html) are a way to avoid making proposals that change the state space in small steps. The consequence is that Bactrian proposal are bolder than proposals based on uniform or Gaussian random values.



## Optimise acceptance through auto optimisation

Some balance between boldness and acceptance can be achieved by automatic optimisation. If the operator has a tunable parameter which can be set through the `get/setCoercableParameterValue()` methods, this tunable parameter can be (re)stored via the state file. After every MCMC step, the operator class is notified whether it was accepted or rejected through the `accept()` and `reject()` methods, which update the `m_nNrRejected`, `m_nNrAccepted`, `m_nNrRejectedForCorrection`, and `m_nNrAcceptedForCorrection` members. Furthermore, the `optimize()` method is called, which you can use to update the coercable parameter. For an example for how to update a scale factor, see the [`ScaleOperator`](https://github.com/CompEvol/beast2/blob/6b5a0c225df7c774ece4864d385c7d55a5df5ee4/src/beast/evolution/operators/ScaleOperator.java#L276) class and for example of a window size update see the [`RealRandomWalkOperator`](https://github.com/CompEvol/beast2/blob/6b5a0c225df7c774ece4864d385c7d55a5df5ee4/src/beast/evolution/operators/RealRandomWalkOperator.java#L84) class.

The `getTargetAcceptanceProbability()` method in `Operator` lets you set the level of acceptance that is aimed for by optimisiation: higher acceptance targets usually lead to less bold moves, while lower targets lead to bolder moves. On balance, a level of 0.234 seems to be optimal for a wide range of classic operators, while for Bactrian operators a higher level (0.3 - 0.4) can be optimal.



## Selecting the best operator

If you have multiple candidate operators or a new operator candidate for an existing one, determining which one is best can be done by running multiple analyses and observing [ESS](http://www.beast2.org/what-is-ess/)s and run times to find optimal operators. However, this requires careful setting of weights and other operator parameters, which can be tedious and time consuming. The process can to some extend be automated using the `AdaptableOperatorSampler` from the ORC package (Douglas et al, 2021). The `AdaptableOperatorSampler` automatically considers boldness, acceptance probability and calculation time for a proposal and uses these to determine operator weights. Here is an example with a `BactrianNodeOperator` which moves heights of nodes between its parent and children, the `SubtreeSlide` which moves nodes along branches, and a fictitious new candidate for moving internal nodes. Here is an example with three operators:

```
<operator id="AdaptableOperatorSampler" spec="orc.operators.AdaptableOperatorSampler" tree="@Tree" weight="45.0">
   <operator id="UniformOperator" spec="BactrianNodeOperator" tree="@Tree" weight="45.0"/>
   <operator id="SubtreeSlide" spec="SubtreeSlide" tree="@Tree" weight="30.0" size="0.03" optimise="false"/>
    <operator id="NewMove" spec="my.package.operators.NewMove" tree="@Tree" weight="10.0" scaleFactor="0.15"/>
</operator>
```

Note that the weights of the operators inside the `AdaptableOperatorSampler` are ignored -- initially, operators will be sampled uniformly. The weight of the `AdaptableOperatorSampler` itself however is used as normal, and perhaps a good value is the sum of weights of the operators inside it.


The `AdaptableOperatorSampler` takes a while (determined by `burnin`) before measuring boldness, acceptance and time for the operators. By default, boldness is measured as the sum of differences in parameter or node heights squared, but if you specify a tree metric (using the `metric` input) you can use other measures for tree distances like Robinson Foulds or RNNI. After another `learnin` steps it starts changing operator weights. It has the following inputs:

* parameter (Function): list of parameters to compare before and after the proposal. If the tree heights are a parameter then include the tree under 'tree' (optional)
* tree (Tree): tree containing node heights to compare before and after the proposal (optional) 
* operator (Operator): list of operators to select from
* burnin (Integer): number of operator calls until the learning process begins (default: 1000) 
* learnin (Integer): number of operator calls after learning begins (ie. at burnin) but before this operator starts to use what it has learned (default: 100 x number of operators) (optional)
* uniformp (Double): the probability that operators are sampled uniformly at random instead of using the trained parameters (default 0.01 in ORC v1.0.2 and earlier, but 0.1 in later versions) 
* metric (TreeMetric): A function for computing the distance between trees. If left empty, then tree distances will not be compared (optional)
* weight (Double): weight with which this operator is selected (required)



The `AdaptableOperatorSampler` stores information in the state file (at the end, where operator information is stored) and contains among other things the following information for the XML fragment above.


Tip: run `egrep "{|}" beast.xml.state > beast.json` where you replace `beast.xml.state` with the name of your state file, and open `beast.json` in a web-browser for much nicer formatting than the state file provides.


```
param_mean_sum	"[0.1500565421442715]"
param_mean_SS	"[0.02267449706602849]"
numAccepts	    "[ 751953,  366999,  31868]"
numProposals	"[2512475, 1214120, 297648]"
operator_mean_runtimes	"[0.4886742948722717, 0.3574444165274914, 0.7862141018905646]"
mean_SS	"[[0.0013286448308873302], [4.1795419527996787E-4], [4.395970191816695E-4]]"
weights	"[0.6631638370649471, 0.28804870042162256, 0.04878746251343036]"
operators	
0	
id	"UniformOperator"
p	3.0161224156080126
accept	752174
reject	1760640
acceptFC	751149
rejectFC	1760114
rejectIv	0
rejectOp	0
1	
id	"SubtreeSlide"
p	0.03
accept	367130
reject	847321
acceptFC	366176
rejectFC	846183
rejectIv	314368
rejectOp	314368
2	
id	"NewMove"
p	0.2502998822070235
accept	31885
reject	266092
acceptFC	31850
rejectFC	265602
rejectIv	59080
rejectOp	59080
```

The important ones to look at are

* `operator_mean_runtimes` shows average run times for the operators in milli seconds. It is an array with one entry for each operator and operators are listed in the `operators` entry.
* `mean_SS` mean sum of squares of changes in state space, effectively the boldness of the operators. The `mean_SS` is updated only when the proposal is accepted.
* `numAccepts`/`numProposals` shows how often operators are accepted and proposed after burnin.

Together they end up in new weights for the operators. In the above, 66% goes to the uniform, 29% to the sub-tree slide and just 5% to the new operator. The new operator does not fare well because it takes long to run: the `operator_mean_runtimes` shows it takes more than twice the sub-tree slide more than 50% more than the uniform. Furthermore, the `mean_SS` shows the change in node heights is a third of the uniform but slightly better than the sub-tree slide. Looking at the `numAccepts`/`numProposals` entries, we see that the new operator gets accepted about 10% of the time, while the other two have acceptance rates of about 1/3. Together, this means a large down-weighting of the new operator. So, back to the drawing board for this operator.


**Note 1**: The `AdaptableOperatorSampler` still randomly picks any of the operators 1% of the time (or more, if you set `uniformp` to some higher level). This explains why the accept and reject counts still go up for operators that have extremely low weights.


**Note 2**: You should not put operators together that work on different parts of the tree. For example, adding both tree scaler and root height scaler in the same `AdaptableOperatorSampler` probably leads to a lot of root height scaling, since it is cheap to calculate and, because the root has less restrictions than the complete tree to scale. Consequently, most of the weight will be assigned to the root scaler and taken away from the tree scaler, which can result in bad mixing.

## Acknowledgements

Thanks to Jordan Douglas for helpful comments and suggestions.

# References

Douglas J, Zhang R, Bouckaert R. Adaptive dating and fast proposals: Revisiting the phylogenetic relaxed clock model. PLoS computational biology. 2021 Feb 2;17(2):e1008322. [DOI](https://doi.org/10.1371/journal.pcbi.1008322)





