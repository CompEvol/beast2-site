---
layout: post
title: What is new in v2.6.0
tags: []
---

## Robustify application launching and package upgrading 

Package loading changed between Java 8 and 9, which was dealt with in v2.5.x by having a launcher process find all packages and kick off a new process for BEAST, BEAUti, etc, with the correct class path for Java. This caused a number of hard to resolve problems with security software. Release v2.6.0 runs everything in a single process (just like v2.4.x and before), which should be more robust. This is especially useful for Windows, where upgrading packages did not always work when Java (versions > 8) was installed.

One implication is that `java -jar beast.jar` should not be used any more (in fact, it does not start BEAST any more), but instead use

`java -jar /path/to/lib/launcher.jar ...`

where `/path/to/` is the path to the file `lib/launcher.jar`, which is part of the BEAST distribution. There is no need to use the `-Dload.beast.user.packages=true` directive any more (this is in fact ignored now).

## Better BEAUti performance with large number of partitions

When large numbers of partitions were imported, this could take a long time due to messages being produced for the message log (which you can find under the `Help/Messages` menu in BEAUti). Reducing the verbosity of these messages can have a great impact on the time it takes to import a large number of partitions, as well as switching between tabs.

## The <a href="https://doi.org/10.1371/journal.pcbi.1006650">BEAST 2.5</a> paper is out!

So, we encourage you to use the following citation:

Remco Bouckaert, Timothy G Vaughan, Joëlle Barido-Sottani, Sebastián Duchêne, Mathieu Fourment, Alexandra Gavryushkina, Joseph Heled, Graham Jones, Denise Kühnert, Nicola De Maio, Michael Matschiner, Fábio K Mendes, Nicola F Müller, Huw A Ogilvie, Louis Du Plessis, Alex Popinga, Andrew Rambaut, David Rasmussen, Igor Siveroni, Marc A Suchard, Chieh-Hsi Wu, Dong Xie, Chi Zhang, Tanja Stadler, Alexei J Drummond

BEAST 2.5: An advanced software platform for Bayesian evolutionary analysis. PLoS computational biology. 2019 Apr 8;15(4):e1006650.


## Make `SequenceSimulator` less sensitive to taxa ordering

When no taxon set was explicitly specified for both tree and sequence simulator, the simulator could get confused in the ordering of taxa, which is solved now.

## XML being produced is more standardised

Where there were a number of key-words for element that were automatically mapped to classes (e.g., and `parameter` element was interpreted as `RealParameter` object) these now explicitly get a `spec` attribute. The use of these keywords is suppressed, resulting in more standardised XML. XML from v2.5.x and before will still be accepted by the XML parser.

## Improved error messages...

... an ever lasting effort.


## New version of DensiTree

This version allows closing the Type and Style tabs in the side bar so that even on screens with low resolution it is still possible access tabs further down in the side bar.

## Help menu in BEAUti is made extendible 

This allows packages to add their own menu items. An example is the <a href="https://github.com/rbouckaert/beasy">Beasy</a> package, which contains a prototype that allows automatic generation of text for a methods section for the model that is specified in BEAUti. 

## Saves last accessed directory between sessions

When accessing directories, BEAUti now remembers the last directory accessed -- even between sessions -- so, directories that are often accessed should require less navigation of the file system.


## Default bounds on BSP pop sizes and random local clock rates updated

The default upper bound for BSP was specified low enough that if these were not changed by the user the analysis could be severely impacted. The upper bound is now set to +Infinity, and users must change this in order to be able to run model selection analyses (like stepping stone and nested sampling analyses).


## Let GuessPatternDialog handle empty fields

With taxon labels of the form "t1&#124;field2&#124;field3" and at least one entry of the form "tN&#124;&#124;field3", splitting on "&#124;" did not pick up "field3", which is fixed now.

## Adding MRCAPriors more robust when multiple alignments share a tree

This situation could cause problems before, where sometimes the wrong tree was picked up.


## Nucleotide model frequencies prior

An implicit uniform[0,1] prior was assumed for frequencies conditioned under the sum of frequencies summing to 1. This is made explicit in BEAUti now.

## More complete Nexus parser ...

... another ever lasting effort.

# For developers

Things that change for developers are outlined in a <a href="/2019/06/24/what-will-change-in-v2-6-0-for-developers.html">separate post</a>, but the main things are:

* there is the possibility to generate text describing methods used in an analysis through the <a href="https://github.com/rbouckaert/beasy">Beasy</a> package.
* More flexible options for constructing BEAST objects.
* More access methods to core classes.
* Added `BEASTInterface::notClonable`.
