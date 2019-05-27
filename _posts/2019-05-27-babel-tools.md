---
layout: post
title: Tree processing, clade comparison, LTT plots, and other Apps
tags: []
---
<p style="color:gray">29 April 2019 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

There are a number of handy utilities in the <a href="https://github.com/rbouckaert/Babel">Babel</a> package, which can be launchers with the `AppLauncher` that comes with BEAST. After you <a href="http://www.beast2.org/managing-packages/">installed</a> the Babel package, you can start them by selecting `File >> Launch Apps` menu in BEAUti, or you can start them from a terminal, by passing the App name to the `applauncher` program that comes with BEAST. For instance, if you want to convert a NEXUS tree file to  Newick tree file, you do so using

	Windows: 
	\path\to\BEAST\applauncher Nexus2Newick -trees nexus.trees -out newick.trees

	OS X: 
	/Applications/BEAST\ 2.5.2/bin/applauncher Nexus2Newick -trees nexus.trees -out newick.trees

	Linux: 
	/path/to/beast/bin/applauncher Nexus2Newick -trees nexus.trees -out newick.trees

where `\path\to\` and `/path/to/` is the directory where BEAST is uncompressed on Windows and Linux respectively.


Babel has the following Apps:

* CladeSetComparator to validate tree sets converged to the same distribution
* LineagesThroughTimeCounter for creating LTT plots
* SpanningTree for sanity checking cognate sets

To manipulate trees
* AdjustTipHeight
* LeafSplitter
* TreeGrafter
* TreeRelabeller
* TreeTransitionMarker
* MakeUltraMetric
* TreeEpochScaler handy when you have deep branches, but most of the detail is from recent splits.
* FamilyFilter

Converting file formats
* Nexus2Newick
* Newick2Nexus
* Phy2Nexus

More details below.


## CladeSetComparator

Match clades from two tree sets and print support for both sets so they can be plotted in an X-Y plot
* <code style="background:lightgrey">tree1</code> 	source tree (set) file
* <code style="background:lightgrey">tree2</code>  	source tree (set) file
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified
* <code style="background:lightgrey">svg</code> 	svg output file. if not specified, no SVG output is produced.
* <code style="background:lightgrey">burnin</code> <integer>	percentage of trees to used as burn-in (and will be ignored)

<center>
	<img width="60%" height="60%" src="/images/CladeSetComparison.png"/>
</center>

Output produced by `CladeSetComparator` plotting the clade support of one tree set against another. The blue lines indicate a 25% offset from the x-equals-y line. Trees from the <a href="https://taming-the-beast.org/tutorials/MEP-tutorial/">time stamped data</a> tutorial.

## LineagesThroughTimeCounter

Produce table for lineages through time plot with 95%HPD bounds
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">out</code> 	output file. Print to stdout if not specified
* <code style="background:lightgrey">svgout</code> 	if specified, produce SVG file with graph
* <code style="background:lightgrey">burnin</code> <integer>	percentage of trees to used as burn-in (and will be ignored)
* <code style="background:lightgrey">resolution</code> <integer>	number of steps in table
* <code style="background:lightgrey">reverseSVGAxis</code> reverse x-axis, that is go forward in time instead of backward
* <code style="background:lightgrey">maxX</code> <double>	maximum value for x-axis. Automaticlly deduced if < 0
* <code style="background:lightgrey">maxY</code> <double>	maximum value for y-axis. Automaticlly deduced if < 0

<center>
	<img width="60%" height="60%" src="/images/LTTplot.png"/>
</center>

Lineage through time plot for human respiratory syncytial virus subgroup A (using trees from <a href="https://taming-the-beast.org/tutorials/MEP-tutorial/">time stamped data</a> tutorial).

## SpanningTree

Creates spanning trees of cognate sets.
* <code style="background:lightgrey">nexus</code> <filename>	nexus file containing cognate data in binary format
* <code style="background:lightgrey">kml</code> <filename>	kml file containing point locations of languages
* <code style="background:lightgrey">cognate</code> <filename>	cognate file listing labels for each column
* <code style="background:lightgrey">background</code> <filename>	image map in mercator projection used for background
* <code style="background:lightgrey">maximumDistance</code> <double>	maximum distance to split on



# Converting file formats

## Nexus2Newick

Convert nexus tree file to Newick format
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified


## Newick2Nexus

Convert Newick tree file to NEXUS format
* <code style="background:lightgrey">trees</code> 	Newick file containing a tree set
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified


## Phy2Nexus

Convert phyml phy format to nexus alignment file
* <code style="background:lightgrey">phy</code> <filename>	Phyml phy file containing a sequence set
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified
* <code style="background:lightgrey">datatype</code> 


# Manipulating sets of trees

## AdjustTipHeight

Set tip heights of existing tree to match the heights from a list
* <code style="background:lightgrey">trees</code> 	Newick file containing a tree set
* <code style="background:lightgrey">cfg</code> <filename>	tab separated configuration file containing two columns: column 1: name of taxon, column 2: height (age) of taxon
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified
* <code style="background:lightgrey">year</code>2millenium 


## LeafSplitter

Relabels leafs of tree set, and splits leafs into random binary sub-tree with branch lengths exponentially distributed.Output as newick trees (not nexus). Metadata is not preserved.
* <code style="background:lightgrey">labelMap</code> <filename>	space delimited text file with list of source and target labels. For taxa that need splitting, specify a comma separated list of new taxon labels.
* <code style="background:lightgrey">meanLength</code> <double>	branch lengths are drawn from an exponential with average meanLength
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified


## TreeGrafter

Grafts nodes into a tree above the MRCA of a set of nodes. This can be useful in case there are taxa that are not part of the analysis, but for which we know from prior information where a taxon should be placed (e.g., South African language in an Indo-European langagae analysis).
* <code style="background:lightgrey">cfg</code> <filename>	tab separated configuration file containing three columns: column 1: name of taxon
column 2: height (age) of taxon
column 3: a comma separated list of taxa determining MRCA to graft above in source tree (if no constraints have been specified).
* <code style="background:lightgrey">constraints</code> 	newick tree file with constraints on where to insert leaf nodes
* <code style="background:lightgrey">src</code> 	source tree (set) file used as skeleton
* <code style="background:lightgrey">out</code> 	output file, or stdout if not specified


## TreeRelabeller

Relabels taxa in a tree file. Usfeful for instance when labels are iso codes and language names are required for visualisation
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">out</code> 	output file. Print to stdout if not specified
* <code style="background:lightgrey">labelMap</code> <filename>	tab delimited text file with list of source and target labels


## TreeTransitionMarker

Mark specific transitions of a meta-data attribute on a tree to make it easy to visualise these transitions, e.g. for an ancestral reconstruction analysis
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree (set)
* <code style="background:lightgrey">out</code> 	output file. Print to stdout if not specified
* <code style="background:lightgrey">tag</code> <string>	metadata tag to be marked
* <code style="background:lightgrey">from</code> <string>	metadata tag value at top of branch to be marked
* <code style="background:lightgrey">to</code> <string>	metadata tag value at bottom of branch to be marked
* <code style="background:lightgrey">markAll</code> 


## MakeUltraMetric

Converts a rooted tree (set) to an ultrametric tree (set), i.e., make all leafs have same distance to root by extending leaf branches so the height of all leaf nodes is zero.
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">out</code> 	output file. Print to stdout if not specified


## TreeEpochScaler

Scales trees so epochs have the same length. This is handy when you have deep branches, but most of the detail is from recent splits: say your tree has a very long branches to the root, and you want to display it well in FigTree then you can define two epochs: one from the root to the oldest child, and one from that child time till present.
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">out</code> 	output file. Print to stdout if not specified
* <code style="background:lightgrey">epochs</code> <string>	space delimited list of epoch boundaries: each epoch will be scaled to the same height as the first epoch


## FamilyFilter

Filters all leafs from specified taxon sets out of a tree file based on clade membership
* <code style="background:lightgrey">families</code> <filename>	NEXUS file containing taxon sets
* <code style="background:lightgrey">trees</code> 	NEXUS file containing a tree set
* <code style="background:lightgrey">subset</code> <filename>	text file with list of taxa to include
* <code style="background:lightgrey">out</code> 	output file. Print to stdout if not specified
* <code style="background:lightgrey">verbose</code> 

