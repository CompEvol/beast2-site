---
layout: post
title: Constraining trees
tags: []
---
<p style="color:gray">12 April 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

When there is overwhelming prior knowledge about some clade in the tree being present, it may be useful to add a prior to the analysis that ensures the clade is monophyletic. The simplest way to do is is to add an MRCA (most recent common ancestor) prior in BEAUti in the priors panel.

There are various ways to add such priors: MRCA prior, multiple monophyletic priors, MRCA priors with rogues, almost MRCA priors, and fossil priors to name a few.


## Adding a single monophyletic constraint

Set up the rest of your analysis in BEAUti, then go to the priors panel.

* Click the `+ add priors` button at the bottom of the screen.
* (Optional) Possibly, if you have packages installed, a dialog pops up allowing you to choose different types of priors to add. Select `MRCA prior`, then click `OK`.
* (Optional) If there is more than one tree in the analysis, a dialog pops up that allows you to choose the tree to apply the constraint to. Select the appropriate tree, then click `OK`.
* A dialog pops up that allows you to specify a set of taxa to constrain, and a label to identify the clade. Select the appropriate taxa, and put in an appropriate name (you can always change it later), then click `OK`.
* The prior is added to the list of priors in the priors-tab, and next to the prior it shows a checkbox with `monophyletic` in it. Check it to ensure the clade will remain monophyletic throughout the analysis.

## Importing MCRA priors from NEXUS file

When you have your analysis in a NEXUS file, you can add a `sets` block to define monophyletic constraints. 

```
# Define monophyletic clades
begin sets;
taxset tocharian = tocharian_a tocharian_b;
taxset anatolian = hittite lycian luvian;
end;
```

These sets will be imported in BEAUti and for each set an MRCA prior is added and appear in the priors panel with the monophyletic checkbox already checked. In the example above, there will be two priors added named `tocharian.prior` and `anatolian.prior`. By the way, you can also add an age distribution for the clade in an assumptions block with a `calibrate` entry like, e.g. `calibrate tocharian = offsetlognormal(1650,200,0.9)` (without semicolon!) but that is off-topic now.


## Adding a monophyletic constraints with rogues

It may not always be clear whether a clade is monophyletic due to other taxa being present that may or may not be part of a clade. For example, an ancient sample A may be classified with contemporary sample B, but there may be uncertainty whether sample C split off before or after the MRCA of A and B. However, it may be known that D is outside the clade AB, but may be going together with C or not. So, the following scenarios are valid:

![MRCAWithRogues](/images/MRCAWithRogues.svg)

A single MRCA prior will not be sufficient, but the `MRCAPriorWithRogues` will allow you to specify the above as follows:

* Install the BEASTLabs package ([installing packages](http://www.beast2.org/managing-packages/)).
* Set up the analysis with normal MRCA priors as outlined above.
* Then open the XML file in a text editor and do search/replace MRCAPrior/MRCAPriorWithRogues.
* Add a taxon set that represents the rogues by adding a rogues element in the MRCAPrior, which could looks something like this:

```
<distribution id="clade_AB.prior" spec="beast.math.distributions.MRCAPriorWithRogues" monophyletic="true" tree="@Tree.t:dna">
     <taxonset id="clade_AB" spec="TaxonSet">
         <taxon id="A" spec="Taxon"/>
         <taxon id="B" spec="Taxon"/>
     </taxonset>
     <rogues id="clade_C" spec="TaxonSet">
         <taxon id="AC spec="Taxon"/>
     </rogues>
 </distribution>
```

Note that if a taxon is already specified elsewhere (like in another MRCA prior), you should use a referral to is `<taxon idref="C"/>`. The XML parser used when starting BEAST will complain about duplicate IDs otherwise.


## Add many monophyletic constraints

When you have only a handful of monophyletic constraints, setting these up in BEAUti will be fine, but if you have many, it may be easier to add a `MultiMonophyleticConstraint` and specify your constraints using a tree in Newick format. The tree does not need to be a binary tree, so something like (A,B,(C,D,E)) would be perfectly fine to represent the two constraints on ABCDE and CDE. An added benefit is that checking for multiple constraints can be done with a single traversal of the tree, so is a bit more efficient than having a single MRCA prior for each clade.

Adding a `MultiMonophyleticConstraint` can be done in BEAUti in the priors panel. Make sure the BEASTLabs package is [installed](http://www.beast2.org/managing-packages/)).

* Set up the rest of the analysis, go to the priors panel and click the `+ add priors`.
* A dialog pops up where you can choose `Multipe monophyletic constraint`.
* (Optional) If there is more than one tree in the analysis, a dialog pops up that allows you to choose the tree to apply the constraint to. Select the appropriate tree, then click `OK`.
* An entry appears in the priors panel, click the small triangle left next to the new entry. 
* Two entries appear: one labelled Newick where you can enter the constraints as a Newick tree.
* The `Is Binary` entry is to indicate the inferred tree is binary, which is should be, so make sure the checkbox next to IsBinary is checked.






## Mix of MRCAPriors and a MultiMonophyleticConstraint

When you have multiple constraints, specified in a mixture of MRCA priors and multi monophyletic constraints, you can combine these in a single distribution through `MultiMRCAPriors`, which can be more efficient if you have many constraints. This requires some XML editing using a text editor and assumes some familiarity with BEAST XML: 

* find the MultiMonophyleticConstraint distribution element and rename `spec="beast.math.distributions.MultiMonophyleticConstraint"` with `spec="beast.math.distributions.MultiMRCAPriors"`.
* make sure there is a closing tag, i.e. if the distribution element ends in `/>` change that to `></distribution>`.
* move all MRCA priors into the new distribution element.
 








## Adding a single monophyletic constraint for analysis that won't start

Sometimes it is difficult to find a starting tree that conforms to all MRCA priors added to the analysis. For these cases, it can help to add `AlmostMRCAPrior`s from the [`AlmostDistributions`](https://github.com/rbouckaert/AlmostDistributions) package. See [README](https://github.com/rbouckaert/AlmostDistributions/blob/master/README.md) for how to install the package. The difference between an Almost MRCA prior and a standard MRCA prior is that when the clade is not monophyletic it does not return -infinity, but puts a large penalty on the clade not being monophyletic. As a result, an invalid starting tree will not stop the MCMC like with the standard MRCA prior. Once a proposal is done that moves the tree into a valid state (i.e. makes the clade monophyletic) it will never accept a proposal that makes the tree non-monophyletic again.

To use `AlmostMRCAPrior`s, 

* [install the AlmostDistributions package](https://github.com/rbouckaert/AlmostDistributions/blob/master/README.md),
* Set up the analysis with normal MRCA priors as outlined above, 
* Then open the XML file in a text editor and do search/replace MRCAPrior/AlmostMRCAPrior.
* Save the XML


## Adding monophyletic constraints with fossil calibrations

You can add MRCA priors and add a distribution on the height of the clade, but if you have more information about the fossilisation process, you can use a `FossilPrior` from the [CladeAge](file:///Users/remco/Downloads/cladeage20160609.pdf) package instead.

