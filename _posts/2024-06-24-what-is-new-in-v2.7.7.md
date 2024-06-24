---
layout: post
title: What is new in v2.7.7
tags: []
---

# TreeAnnotator

## Add CCD to the list of recommended packages for TreeAnnotator 

The maximum clade credibility (MCC) tree is the default methods used in TreeAnnotator for summarising tree posteriors.
However, it was shown (Berling et al, 2024) that the conditional clade distribution (CCD) method almost always performs better in terms of accuracy (closer Robinson-Foulds distance to the true tree) and specificity (summary trees of two independent runs are closer).
In fact, running the MCC tree on any moderately high entropy posterior almost always results in different MCC trees, unlike the CCD method.

This shows a collection of 20 summary trees on the example RSV2 data set that comes with BEAST2.
Left, the MCC trees showing considerable differences between the various runs, while CCD0 is much more stable.

![RSV2 MCC vs CCD0 point estimates](/images/RSV2MCCvsCCD0pointestimates.png)

Therefore, the MCC method will be discouraged from now on, and it is recommended to **use the CCD0** method instead.


## TreeAnnotator GUI to pick up correct topology setting method 

A bug caused TreeAnnotator to ignore the choice in the drop down box for the topology setting (though the command line versiono was fine). This is fixed now.

#	LogAnalyser 

##	Add threading option to loganalyser when running with the oneline option 

When performing well calibrated simulation studies (Mendes et al, 2024), it is necessary to summarise results from many BEAST runs, which can easily be done with `loganalyser` but can take up considerably time when there are many items in the trace log or trace logs are long.
LogAnalyser now has a `-threads <threadcount>` option that allow running this proces in parallel. 
Useful in combination with the `-oneline` mode.


##	Check added to prevent out of bound exception CompeVol/BeastFX/#81

Running loganalyser on trace files that are being written to by a BEAST process can results in an out-of-bound exception when the file is being written to between the time loganalyser determines how much memory to reserve by checking the file size, and actually parsing of the trace log.

A check to prevent such situations is added to prevent LogAnalyser crashing.

# BEAUti

##	Robustify for drag/dropping alignments

Using the BinaryCovarion template from the Babel package, drag/dropping did not work even if the alignment could be imported via the `File => Import alignment` menu. 
This should work now.

##	Enable importing of alignments in files with `.txt` extension

Downloading nexus files from the internet on some Windows and OS X computers sometimes got a `.txt` extension appended, which is not visible in file browsers. 
This makes it hard to diagnose, and was holding up quite a few people doing tutorials.
The alignment importer now deals with this by removing the file extension if it does not succeed in finding a suitable importer.

##	Remove HTML error messages

Some error messages were still lingering containing HTML tags, which does not format properly in JavaFX Alerts.
These should all be fixed now.

# BEAST

##	Allow launch through `java -jar launcher.jar`

Command line launch of BEAST directly this way was possible in version v2.6 for BEAST but reinstated now for v2.7.
It is recommended to start BEAST through the script in `\path\to\BEAST\bat\beast.bat` on Windows or `/path/to/beast/bin/beast` on OS X and Linux though, since it is guaranteed to use the correct java version.

##	Make alignment a StateNode

This enables alignments to be sampled, which can be useful when there is uncertainty about the alignment.

##	Make Randomizer thread aware for better replicability of threaded runs 

BEAST is usually deterministic, so starting with the same seed twice results in exactly the same output.

Multi-threaded algorithms that use the Randomizer class in different threads does not show this behaviour, since it depends on how much time the operating system assigns to each thread on when threads access Randomizer. This hinders debugging of for example automatic stopping MCMC algorithms, which use multiple MCMC chains in parallel, all accessing the same Randomizer object.

A solution that has minimal impact on other code would be for the Randomizer to check the thread name using `Thread.currentThread().getName()`. If the name is not set, the default `Randomizer.random` object can be used to generate random numbers. If the thread name is set, a new `MersenneTwisterFast` can be associate with the name, so that the order in which threads obtain random numbers does not matter.

This is somewhat fragile in that the programmer has to set the thread name (using `Thread.setName()`).

##	Make the `AdaptableOperatorSampler` ignore zero weight operators CompEvol/beast2#1136
	
Currently, the weight attributes of operators that are inputs for an `AdaptableOperatorSampler` are ignored.

For fixed tree analyses, the standard recipe is to set weights of operators that change the topology to zero (like Wilson Balding, Exchange operators), but since ORC operators also cause topology changes the story becomes more involved since operators like the `ORCInternalnodesOperator` that are input to an `AdaptableOperatorSampler` need to be removed by editing or commented out in the XML. This is not possible in BEAUti. To keep things simple along the lines of 'set weight to zero' it would be good if the `AdaptableOperatorSampler` could just ignore these operators.	



## References

L Berling, J Klawitter, R Bouckaert, D Xie, A Gavryushkin, A Drummond
A tractable tree distribution parameterized by clade probabilities and its application to Bayesian phylogenetic point estimation
bioRxiv, 2024.02. 20.581316

FK Mendes, R Bouckaert, LM Carvalho, AJ Drummond
How to validate a Bayesian evolutionary model
bioRxiv, 2024.02. 11.579856