---
layout: post
title: Reducing log file size
tags: []
---
<p style="color:gray">1 October 2023 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

Trace and tree logs can become quite large, especially with large numbers of taxa.
Here are a number of ways to reduce the file sizes.

## Set pre-burn-in on MCMC

Pre-burnin is like burn-in, except it does not log the samples.
Be aware that for post-processing it is not necessary any more to remove burn-in. 
Most post-processing programs (like Tracer) assume a default of 10% burn-in, so you need to change these settings when analysing the logs.

The pre-burn-in can be set in the MCMC panel of BEAUti, or you can edit the XML in a text editor and add the `preBurnin` attribute for the `run` element.


## Set `logEvery` so that the number of samples=10000

Every logger has a `logEvery` attribute that determines how many MCMC samples are skipped before saving a state in the trace a tree logs.
Usually, it does not make sense to have more than 10 thousand samples in the log, since it will not result in better accuracy of the represented distributions in the trace log, assuming the chain mixed well.

By dividing the length of the MCMC chain by 10 thousand, you can calculate what `logEvery` should be. 
For example, for a chain length of 10 million, we get 10,000,000/10,000 = 1e7/1e4=1e3 = 1000, which are the default settings for the Standard template in BEAUti.


## Set `dp` on TreeWithMetaDataLogger for tree loggers

The `dp` attribute represents the number of decimal places to use writing branch lengths, rates and real-valued metadata. 
If you use -1 full precision is used, and this is the default. 
This means branch lengths and rates are logged in about 17 significant digits. 

For example

```
1[&rate=0.18136058857153339]:0.08642712109963069
```

By setting `dp` to something lower, e.g. `dp="5"` the tree file would ouput

```
1[&rate=0.18136]:0.08642
```

which is half the size.
To set the `dp`, in BEAUti go to the MCMC panel, click on the triangle next to the tree log, and select the button with `TreeWithMetaDataLogger.t:partition` next to it (where `partition` the name of the partition). A dialog pops up where you can set the entry for `Dp`.

Alternatively, edit the XML in a text editor, and search for the `log` element with `TreeWithMetaDataLogger` in it. Add `dp="5"` to set the number of significant digits to 5.
For example like so:

```
<log id="TreeWithMetaDataLogger.t:dna" dp="4" spec="beast.base.evolution.TreeWithMetaDataLogger" tree="@Tree.t:dna"/>
```