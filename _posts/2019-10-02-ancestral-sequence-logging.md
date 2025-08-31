---
layout: post
title: Logging ancestral sequences
tags: []
---
<p style="color:gray">2 October 2019 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>
Updated 1 September 2025

There are two options:
* log complete ancestral sequences at all internal nodes of a tree in a tree file
* log complete ancestral sequences at the MRCA of a single clade in a trace file


## Log sequences at all internal nodes 

To log complete ancestral sequences at all internal nodes of a tree, you can use the beast-classic package, version 1.4.1 or better.

Simply add a logger to the XML that looks similar to this: 

``` XML
<logger id="AncestralSequenceLogger" fileName="ancestral.trees" logEvery="10000" mode="tree">
	<log id="atreeLikelihood" 
		spec="beastclassic.evolution.likelihood.AncestralSequenceLogger" 
		data="@data" 
    	siteModel="@SiteModel.s:data"
		branchRateModel="@branchRates.c:data
		tree="@Tree.t:data" 
		tag="seq"
		/>
</logger>
```

You can copy/paste the fragment just before the closing run tag `</run>` and update the attributes as follows:

* make `data`, `siteModel`, `branchRateModel` and `tree` point to the same objects as the treeLikelihood you want to log.
If you set up the XML in BEAUti, changing 'data' to the partition name should do the trick most of the time.
* set `fileName` and `logEvery` to your taste.

and it should run.

In more detail:

* `id` is a unique identifier. It does not matter what you call it, as long as it is unique for the XML file.
* `fileName` is the file used for logging the trees to.
* `logEvery` is the frequency of logging; once every `logEvery` MCMC samples, an entry is added to the log.
* `mode` must be `tree`.
* `spec` must be `beast.evolution.likelihood.AncestralSequenceLogger`.
* `data`, `siteModel`, `branchRateModel` must point to the same as for the treelikelihood that you would like to log ancestral sequences for.
* `tree` can refer to the tree of the treelikelihood, but you can also specify a Newick tree (e.g., an MCC tree for the analysis).
* `tag` is the label used in the tree file for labeling the sequence.

The log file is a tree file that has a single meta-data item, the reconstructed sequence, which could look something like this (for a short alignment of just 10 sites):

```
tree STATE_10000 = (((((((((1[&seq="ATGCGTTTGG"]:0.02979784503786547,4[&seq="ATGCGTTTGG"]:0.02979784503786547)[&seq="ATGCGTTTGG"]:0.0013316887030746238,((2[&seq="ATGCGTTTGG"]:0.005244509868025032,19[&seq="ATGCGTTTGG"]:0.005244509868025032)[&seq="ATGCGTTTGG"]:8.764871247808198E-4,((21[&seq="ATGCGTTTGG"]:...

```


There is an optional flag on `AncestralSequenceLogger`:

* `useMAP` this is an optional flag (set to true of the default false), which determines whether to use maximum aposteriori assignments or sample from the distribution.

## Logging on a fixed tree

Though the BEAST analysis typically estimates the tree, so topologies change through the MCMC analysis, it may be useful to do the reconstruction on a fixed tree. You can specify a tree in Newick format and add the following fragment to the XML, just before the closing beast tag `</beast>`, but after the closing run tag `</run>`:

```XML
<tree spec='beast.base.evolution.tree.TreeParser' id='NewickTree' 
  taxa='@data' IsLabelledNewick="true" 
  newick="((your,(tree,goes)),here)"/>
```

If you point `tree` attribute of the AncestralSequenceLogger to NewikcTree, so it reads as `tree="@NewickTree"` assuming you keep the id as above, the logger will use the Newick tree for logging.



## Log complete ancestral sequences at the MRCA of a single clade in a trace file

The BEASTLabs package has a class `beastlabs.evolution.likelihood.AncestralStateLogger` that can be used to log a sequence at the MRCA of a clade, and produces a trace log that can be inspected in Tracer.
There is en example file here: [testAncestralStateLogger.xml](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/examples/testAncestralStateLogger.xml).

Essentially, for each clade in the tree that you are interested in, you have to add the tree logger like so:

```XML
   <logger id="ancestral state" fileName="ringe_proto_germanic.log" logEvery="1000">
        <log id="AncestralStateLogger" spec="beastlabs.evolution.likelihood.AncestralStateLogger" data="@bin.ringe" branchRateModel="@RelaxedClock.c:ringe" tree="@Tree.t:ringe" siteModel="@SiteModel.s:ringe">
            <taxonset spec='TaxonSet' id="ProtoGermanic">
                <taxon id="oldnorse" spec="Taxon"/>
                <taxon id="gothic" spec="Taxon"/>
                <taxon id="oldhighgerman" spec="Taxon"/>
                <taxon id="oldprussian" spec="Taxon"/>
                <taxon id="oldenglish" spec="Taxon"/>
            </taxonset>
        </log>
    </logger>
```
Make sure the data, tree, branch rate model and site model point to the items that you are interested in (they should match the ones in the TreeLikelihood over the data).

Furthermore, you need to specify a taxon set.
Be careful when using a taxon used elsewhere: instead of defining a new taxon with the same id, use idref to refer to the place that the taxon was already defined, like so `<taxon idref="oldnorse"/>`.

AncestralStateLogger has two flags that may be useful:
* `logIndividualSites`: if true, tree log gets one entry for every site, if false complete sequence is logged. Default: false
* `useMAP`: whether to use maximum aposteriori assignments or sample. Default: false

