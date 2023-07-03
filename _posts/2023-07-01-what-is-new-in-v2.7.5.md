---
layout: post
title: What is new in v2.7.5
tags: []
---

# BEAST:

## Enable quiting BEAST on OS X with cmd-Q BeastFX

The GUI version of BEAST did not handle the `Quit` menu properly, so pressing the menu or using the Cmd-Q shortcut did not work. This is fixed now.

## Bounded transforms added

This is of use to package developers. Transforms are used in the AVMN operator, which can improve mixing considerably. Now, new transforms are available that upper, lower or interval bounded a parameter.

## Higher default heap sizes for Windows

This can prevent out-of-memory errors.


# BEAUti

## Make linking by combobox changes work in partition panel of BEAUti

A bug was fixed for linking partitions in the paritions panel by selecing a partition to link to from the combobox in the site model, clock model and tree columns

## Make drag/drop work in FileListInputEditor

Handy when selecting multiple files, such as for LogCombiner.

## Add warning when adding hyperpriors on parametric distribution parameters

Hyperpriors on parametric distribution parameters should almost certainly not be used, but are currently easily introduced by clicking the `estimate` box next to such parameters.
Unless the user knows very well what is going on, the estimate box should probably left unselected.
Now, when the user selects the box a warning pops up asking whether this is really what is intended.

## Fix MRCA prior editor using the wrong tree (useful for multi-species coalescent analyses)

The MRCA prior editor could select a gene tree instead of the species tree, which is what MRCA priors typically should be applied to in an MSC analysis.

## Make it possible for the MCMC analysis to be replaced by others

Switching between MCMC and other methods of analysis, such as coupled MCMC or nested sampling, required seperate templates. 
In this version, switching can be done in the MCMC panel by selecting such alternative analysis from a drop down box (provided the packages have the appropriate templates).

## Fixed command line argument parsing problem

When starting with `-version_file` and  multitple arguments BEAUti became confused, which is fixed now.

## Added command line options to import nucleotide and AA fasta files.

It was always possible to start BEAUti from the command line and specify an alignment in a NEXUS file to be loaded at start-up.
This can be done with fasta files as well now using the `-fasta_nucleotide` and `-fasta_aminoacid` command line options. 

## Fix prior panel parametric distribution display

Parametric distributions contain parameters that were only visible when the prior was expanded.
Now, parameters are displayed in the drop down box. 
For example a normal with mean 6 and standard deviation 0.5 is shown as `Normal[6.0,0.5]` in the drop down box of distributions to choose from.

## Make BEAUti show coalescent based tree prior inputs

Coalescent based tree priors can have options that were not always visible. This is fixed now.

## Show input dialog in center of BEAUti when drag/dropping files in partitions panel BeastFX

The dialog box sometimes disappeared in the background, leaving BEAUti otherwise unresponisve.
It is now shown centered in the BEAUti panel making it clear a partition type needs to be chosen.

## Fasta importer allows choosing datatype other than nucleotide and aminoacid BeastFX

Handy for importing alignments in fasta format for non standard data types.

## BEAUti package manager buttons and taxon set editor show correctly with bootstrap theme BeastFX

These buttons became minimised when using the bootstrap theme making the labels unreadable. 
The theme was fixed so that the labels are now clearly visible.

## X-axis of graph for parametric distributions in prior panel scales better BeastFX

It used to be upper bounded at 1, which made it very hard to see distributions that had most of its support between 0 and 1e-x where x a small integer.

## Improve tool tips for PriorInputEditor & Better error messages BeastFX

An ever continuing effort...

## Selected theme indicated in view menu BeastFX

There is now a tick in front of the selected theme, making it easier to know which theme is the current theme.


# LogCombiner

## GUI now selects appropriate file types in file selector by default

LogCombiner can combine trace or tree logs, typically stored in `.log` and `.trees` files respectively. When selecting a file to combine, the file chooser defaulted to `.log` files, even when combining tree logs. This version looks at the file type selection, and changes the appropiate default file type in the file chooser.



