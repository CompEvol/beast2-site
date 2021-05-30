---
layout: post
title: Uniform tree priors -- why not use them
tags: []
---
<p style="color:gray">31 May 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

BEAUti does not support a 'uniform' tree prior. It would not be too hard to implement (in 
fact, the XML below does just that) but a uniform prior does not make a lot of sense. 
Let's have a look at a Yule prior, a coalescent prior with constant populations size and a 
uniform prior. For ease of comparison, we also put a uniform prior on the root height 
(lower=0.95, upper=1.05) so that we can easily compare the time of the first split in the 
tree after the root, the second split, etc.. Here are the distributions of splits ages for
the three tree priors when using 20 taxa, so 18 internal node heights.

![yule tree prior](/images/yule-treeprior.svg)
![coalescent tree prior](/images/coalescent-treeprior.svg)
![uniform tree prior](/images/uniform-treeprior.svg)

Splits of the Yule prior tend towards zero, and with the coalescent prior even more so.
On the other hand, the uniform prior shows splits approximately uniformly spread through 
time, with higher concentration near t=0 and t=1 (the average root age). So, a uniform 
tree prior implies time till the next split is independent of how many lineages there are 
present, unlike the Yule and coalescent priors. In other words, if there a say k-1 
lineages that can split into k lineages, it will take equally long as when there are k-2
lineages, or k+10 lineages.

If we go forward in time from the root to the leafs, with n taxa there are n-2 internal
nodes, so n-2 split times, and we expect these times to be more or less evenly spread out 
between the leafs and root. Unlike birth-death processes, where more lineages mean a 
higher chance of observing a split at one of these lineages.

If we go backward in time, under a coalescent process there is a higher chance for two 
lineages to coalesc when there are more lineages, implying a shorter time between coalescent intervals when there are more lineages. When coming nearer the root where there are fewer lineages, the time between coalescent events will thus be larger, unlike with a uniform prior.

I do not know of a tree generating process that has the property of equally sized coalescent intervals, and it seems counter-intuitive that having many  lineages present does not result in more speciation, hence shorter times between splits with increasing numbers of lineages. Therefore, it does not seem sensible to me to use a uniform prior.

If you know a tree process that does fit a uniform prior, please let me know! 

## XML for Yule prior

Below is the XML for generating a sample from a Yule prior used to create the figure above.
To change the number of taxa, add or delete entries in the range attribute of the plate 
element.

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<beast namespace="beast.evolution.operators:beast.evolution.alignment:beast.core" version="2.6">    

<run id="mcmc" spec="MCMC" chainLength="10000000">
    <state id="state" storeEvery="5000">
        <tree id="Tree.t:dna" name="stateNode">
            <taxonset id="TaxonSet.dna" spec="TaxonSet">
            	<plate var="n" range="0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19">
	            	<taxon id="t$(n)" spec="Taxon"/>
	            </plate>
            </taxonset>
        </tree>
        <parameter id="birthRate.t:dna" name="stateNode">1.0</parameter>
    </state>

    <distribution id="prior" spec="util.CompoundDistribution">
        <distribution id="YuleModel.t:dna" spec="beast.evolution.speciation.YuleModel" birthDiffRate="@birthRate.t:dna" tree="@Tree.t:dna"/>
        <distribution id="YuleBirthRatePrior.t:dna" spec="beast.math.distributions.Prior" x="@birthRate.t:dna">
            <distr spec="beast.math.distributions.Uniform" upper="10"/>
        </distribution>
        <distribution id='rootprior' spec='beast.math.distributions.MRCAPrior' tree='@Tree.t:dna'>
      		<taxonset idref="TaxonSet.dna"/>
            <distr id='normalDistr' spec='beast.math.distributions.AlmostUniform' lower="0.95" upper="1.05"/>
        </distribution>    
    </distribution>

    <operator id="YuleBirthRateScaler.t:dna" spec="ScaleOperator" parameter="@birthRate.t:dna" scaleFactor="0.5" weight="3.0" optimise="false"/>
    <operator id="YuleModelTreeScaler.t:dna" spec="ScaleOperator" scaleFactor="0.9" tree="@Tree.t:dna" weight="3.0" optimise="false"/>
    <operator id="YuleModelTreeRootScaler.t:dna" spec="ScaleOperator" rootOnly="true" scaleFactor="0.9" tree="@Tree.t:dna" weight="3.0" optimise="false"/>
    <operator id="YuleModelUniformOperator.t:dna" spec="Uniform" tree="@Tree.t:dna" weight="30.0"/>
    <operator id="YuleModelSubtreeSlide.t:dna" spec="SubtreeSlide" tree="@Tree.t:dna" weight="15.0" size="0.1" optimise="false"/>
    <operator id="YuleModelNarrow.t:dna" spec="Exchange" tree="@Tree.t:dna" weight="15.0"/>
    <operator id="YuleModelWide.t:dna" spec="Exchange" isNarrow="false" tree="@Tree.t:dna" weight="3.0"/>
    <operator id="YuleModelWilsonBalding.t:dna" spec="WilsonBalding" tree="@Tree.t:dna" weight="3.0"/>

    <logger id="screenlog" logEvery="100000">
        <log idref="prior"/>
        <log id="ESS.0" spec="util.ESS" arg="@prior"/>
    </logger>

    <logger id="treelog.t:dna" fileName="$(filebase).trees" logEvery="1000" mode="tree">
        <log idref="Tree.t:dna"/>
    </logger>

</run>

</beast>
```

## XML for Coalescent prior

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<beast namespace="beast.evolution.operators:beast.evolution.alignment:beast.core:beast.evolution.tree.coalescent" version="2.6">    

<run id="mcmc" spec="MCMC" chainLength="10000000">
    <state id="state" storeEvery="5000">
        <tree id="Tree.t:dna" name="stateNode">
            <taxonset id="TaxonSet.dna" spec="TaxonSet">
            	<plate var="n" range="0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19">
	            	<taxon id="t$(n)" spec="Taxon"/>
	            </plate>
            </taxonset>
        </tree>
        <parameter id="popSize.t:dna" spec="parameter.RealParameter" name="stateNode">0.3</parameter>
    </state>

    <distribution id="prior" spec="util.CompoundDistribution">
         <distribution id="CoalescentConstant.t:dna" spec="Coalescent">
            <populationModel id="ConstantPopulation.t:dna" spec="ConstantPopulation" popSize="@popSize.t:dna"/>
            <treeIntervals id="TreeIntervals.t:dna" spec="TreeIntervals" tree="@Tree.t:dna"/>
        </distribution>
        <distribution id="PopSizePrior.t:dna" spec="beast.math.distributions.Prior" x="@popSize.t:dna">
            <distr id="Exponential.1" spec='beast.math.distributions.Uniform'  upper="10.0"/>
         </distribution>
        
        <distribution id='rootprior' spec='beast.math.distributions.MRCAPrior' tree='@Tree.t:dna'>
      		<taxonset idref="TaxonSet.dna"/>
            <distr id='normalDistr' spec='beast.math.distributions.AlmostUniform' lower="0.95" upper="1.05"/>
        </distribution>    
    </distribution>

    <operator id="PopSizeScaler.t:dna" spec="ScaleOperator" parameter="@popSize.t:dna" scaleFactor="0.75" weight="3.0"/>
    <operator id="ModelTreeScaler.t:dna" spec="ScaleOperator" scaleFactor="0.9" tree="@Tree.t:dna" weight="3.0" optimise="false"/>
    <operator id="ModelTreeRootScaler.t:dna" spec="ScaleOperator" rootOnly="true" scaleFactor="0.9" tree="@Tree.t:dna" weight="3.0" optimise="false"/>
    <operator id="ModelUniformOperator.t:dna" spec="Uniform" tree="@Tree.t:dna" weight="30.0"/>
    <operator id="ModelSubtreeSlide.t:dna" spec="SubtreeSlide" tree="@Tree.t:dna" weight="15.0" size="0.1" optimise="false"/>
    <operator id="ModelNarrow.t:dna" spec="Exchange" tree="@Tree.t:dna" weight="15.0"/>
    <operator id="ModelWide.t:dna" spec="Exchange" isNarrow="false" tree="@Tree.t:dna" weight="3.0"/>
    <operator id="ModelWilsonBalding.t:dna" spec="WilsonBalding" tree="@Tree.t:dna" weight="3.0"/>

    <logger id="screenlog" logEvery="100000">
        <log idref="prior"/>
        <log idref="popSize.t:dna"/>
        <log id="ESS.0" spec="util.ESS" arg="@prior"/>
    </logger>

    <logger id="treelog.t:dna" fileName="$(filebase).trees" logEvery="1000" mode="tree">
        <log idref="Tree.t:dna"/>
    </logger>

</run>

</beast>
```

## XML for uniform prior

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<beast namespace="beast.evolution.operators:beast.evolution.alignment:beast.core" version="2.6">    

<run id="mcmc" spec="MCMC" chainLength="10000000">
    <state id="state" storeEvery="5000">
        <tree id="Tree.t:dna" name="stateNode">
            <taxonset id="TaxonSet.dna" spec="TaxonSet">
            	<plate var="n" range="0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19">
	            	<taxon id="t$(n)" spec="Taxon"/>
	            </plate>
            </taxonset>
        </tree>
    </state>

    <distribution id="prior" spec="util.CompoundDistribution">
        <distribution id='rootprior' spec='beast.math.distributions.MRCAPrior' tree='@Tree.t:dna'>
      		<taxonset idref="TaxonSet.dna"/>
            <distr id='normalDistr' spec='beast.math.distributions.AlmostUniform' lower="0.95" upper="1.05"/>
        </distribution>    
    </distribution>

    <operator id="YuleModelTreeScaler.t:dna" spec="ScaleOperator" scaleFactor="0.9" tree="@Tree.t:dna" weight="3.0" optimise="false"/>
    <operator id="YuleModelTreeRootScaler.t:dna" spec="ScaleOperator" rootOnly="true" scaleFactor="0.9" tree="@Tree.t:dna" weight="3.0" optimise="false"/>
    <operator id="YuleModelUniformOperator.t:dna" spec="Uniform" tree="@Tree.t:dna" weight="30.0"/>
    <operator id="YuleModelSubtreeSlide.t:dna" spec="SubtreeSlide" tree="@Tree.t:dna" weight="15.0" size="0.1" optimise="false"/>
    <operator id="YuleModelNarrow.t:dna" spec="Exchange" tree="@Tree.t:dna" weight="15.0"/>
    <operator id="YuleModelWide.t:dna" spec="Exchange" isNarrow="false" tree="@Tree.t:dna" weight="3.0"/>
    <operator id="YuleModelWilsonBalding.t:dna" spec="WilsonBalding" tree="@Tree.t:dna" weight="3.0"/>

    <logger id="screenlog" logEvery="100000">
        <log idref="prior"/>
        <log id="ESS.0" spec="util.ESS" arg="@prior"/>
    </logger>

    <logger id="treelog.t:dna" fileName="$(filebase).trees" logEvery="1000" mode="tree">
        <log idref="Tree.t:dna"/>
    </logger>

</run>

</beast>
```