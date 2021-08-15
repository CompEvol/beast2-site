---
layout: post
title: What is new in v2.6.6
tags: []
---


## Fix so that when not using ambiguities, the nucleotide 'U' base pair is correctly interpreted

A bug caused the `U` character for RNA sequences to be interpreted as ambiguous characters. When tree likelihoods have `useAmbiguities` set to false (which is the default) `U`s were treated the same as missing data (with `useAmbiguities=true` there was no such issue). This is fixed in this release, and `U`s should be treated the same as `T`s for nucleotide sequences.


## Fix tree annotator for lower bound on common ancestor heights

A bug in Tree annotator caused zeros to be included in the range for heights, which resulted in common ancestor height intervals often containing zeros. This is fixed, so common ancestor height intervals should look more reasonable now.


## Make the -prefix option work when not using a directory

When using the `-prefix` option for BEAST, the state file had a directory separator included. If the prefix is not a directory, this resulted in the state file not being able to be written. The prefix can still refer to a directory by putting a directory separator ("\" for Windows, "/" for OS X and Linux) at the end of the prefix.

## Allow '=' signs in options passsed with -D

Command line beast allows specifying parameters using the `-D` option, so you can for example set the chaing length by specifying `chainLength="$(cl)"` in the XML and start beast with `-D cl=100000`. However, if the value contains equal signs (=) the parser assumed that the variable name included everything till the last equal sign. This is fixed, so it is now possible to specify `-D trait=value1=A,value2=B,value3=C` and any occurrance of '$(trait)' will be replaced by 'value1=A,value2=B,value3=C'.


## Fix so DocMaker works again

Some complex Inputs caused the DocMaker to fall over. Catching these cases more carefully allows the DocMaker to function again.

## Limit size of Trie to prevent memory leaks for some models

The Trie is a data structure in the state that caches paths in the calculation graph, which can save considerable time. However, it relies on there be a finite number of paths, which relies on operators only performing proposals on a limited set of state nodes in order to keep memory usage limited.

However, some models use proposals that can affect a large number of state node combinations, for example StarBeast2 can more any subset of the set of gene trees. Previously, this was solved by marking all state nodes dirty, resulting in a fixed path. A more elegant solution is to limit the size of the Trie, which is now set at 1000.

## Miscellaneous

* Add filter input to SwapOperator
* Update for R script to create EBSP plots
* More robust Nexus parser
* Improved error messages


## New packages

The [lphybeast](https://github.com/LinguaPhylo/LPhyBeast) and [Recombination](https://github.com/nicfel/Recombination) packages were added recently. Also keep an eye out on new packages in the [package-extra.xml](https://github.com/CompEvol/CBAN/blob/master/packages-extra.xml) repository where [BICEPS](https://github.com/rbouckaert/biceps/) and [Phylonco](https://github.com/kche309/beast-phylonco) packages were added since the last BEAST release.

