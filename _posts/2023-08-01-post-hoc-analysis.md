---
layout: post
title: Forgot to log something? Do a post hoc analysis!
tags: []
---
<p style="color:gray">1 August 2023 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Some times a posterior of an analysis is available, but some statistic based on that analysis, like an ancestral reconstruction, has not been logged. 
Most of the time, it is possible to reconstruct the state based on trace and tree logs.
If that is the case, instead of running the analysis again with a missing logger -- which can take a lot of time -- it is now possible to just calculate the logger value based on the trace and tree files representing the posterior.
This requires some XML editing of the original XML file by replacing the MCMC with a PostHocAnalyser.

## Requirements

* BEASTlabs package v2.0.1 or newer must be installed.
* The state must be able to be reconstructed from the trace and tree files.
* The trace and tree files have the same log frequencies.


## Setting up the XML

Start with a copy of the XML used to produce the posterior, then edit the XML as follows

1. replace MCMC with PostHocAnalyser
2. define mapping of trace and tree files to state nodes
3. replace loggers with the ones you want to calculate


## 1. replace MCMC with PostHocAnalyser

Replace spec attribute of the opening `run` element with `beastlabs.tools.PostHocAnalyser` 

```
    <run id="mcmc" spec="MCMC" chainLength="1000000">
```

so it looks something like this:


```
    <run id="mcmc" spec="beastlabs.tools.PostHocAnalyser" chainLength="1000000">
```

Leave the chain length -- it is ignored, so its value does not matter. The log that will be produced will just have as many entries as the input log file.


## 2. define mapping of trace and tree files to state nodes

There are two types of mappings: `TraceStateNodeSource` for trace log and `TreeStateNodeSource` for tree logs.


For a trace log, the following values need to be specified: 
* a file name in the `trace` attribute,
* a mapping of trace columns to state nodes.
* a reference to the state in the `state` attribute. 

The mapping of trace columns to state nodes are separated by commas, and have state node IDs on the left hand side of the `=` sign and trace log column names on the right hand, e.g. `kappa.s:dna=kappa` where `kappa.s:dna` the full ID in the XML and `kappa` the value being logged. 
When there are multiple partitions, it probably needs to be `kappa.s:dna=kappa.dna` since then the trace file will have `kappa.dna` instead of `kappa`.
If state parameter is multidimensional, the dimension (starting at 0!) can be defined within brackets, e.g. `freqParameter.s:dna[3] = freqParameter.4`.


```
<source spec="beastlabs.tools.TraceStateNodeSource" state="@state" trace="testPostHocAnalysis.log">
	birthRate.t:dna=birthRate,
	kappa.s:dna=kappa,
	freqParameter.s:dna[0] = freqParameter.1,
	freqParameter.s:dna[1] = freqParameter.2,
	freqParameter.s:dna[2] = freqParameter.3,
	freqParameter.s:dna[3] = freqParameter.4
</source>
```

To abbreviate long lists for multi-dimensional parameters, a shortcut is to only define the first index, and use no index in brackets, e.g. `freqParameter.s:dna[] = freqParameter.1`.
So, the above XML fragment is equivalent with 

```
<source spec="beastlabs.tools.TraceStateNodeSource" state="@state" trace="testPostHocAnalysis.log">
	birthRate.t:dna=birthRate,
	kappa.s:dna=kappa,
	freqParameter.s:dna[] = freqParameter.1
</source>
```

The mapping of trees requires
* a file name in the `treefile` attribute,
* the ID of the tree in the `treeID` attribute
* a reference to the state in the `state` attribute. 

For example:

```    	
<source spec="beastlabs.tools.TreeStateNodeSource" treeID="Tree.t:dna" state="@state" treefile="testPostHocAnalysis.trees"/>
```

If you want to get meta data from the tree into a parameter, in the body specify a comma separated list of `state node ID=meta data label` pairs, for example

```    	
<source spec="beastlabs.tools.TreeStateNodeSource" treeID="Tree.t:dna" state="@state" treefile="testPostHocAnalysis.trees">
	BranchRates.c:dna=rate,
	Deme.dna=deme
</source
```

assigns the meta data `rate` with state node `BranchRates.c:dna`, and meta data `deme` with state node `Deme.dna`. The dimension is determined by the node nr.


It is possible to have multiple entries for trace files and/or tree files for example with Star BEAST analysis.


## 3. replace loggers with the ones you want to calculate


Instead of logging every value again, remove all existing loggers from the XML.
Add a new logger that logs to a file that differs from the input files in order not to overwrite them.

Make sure the `logEvery` attribute is the same (or a divider of) the log frequency used to generate the input files. Setting `logEvery` to `1` surely does that.

For example, to log the tree height and length, add the following:


```
<logger id="tracelog" spec="Logger" fileName="posthoc.log" logEvery="1">
     <log id="TreeHeight.t:dna" spec="beast.base.evolution.tree.TreeStatLogger" tree="@Tree.t:dna"/>
</logger>
```
