---
layout: post
title: What is new in v2.7.6
tags: []
---

## TreeAnnotator allows packages to define new methods for creating summary trees 

By default, TreeAnnotator uses the maximum clade credibility (MCC) tree.
Recent investigation showed that the MCC tree may not be a good tree and we are looking into alternatives (e.g. [matrix based ones](https://www.biorxiv.org/content/10.1101/2023.10.19.563180v1)) appear to outperform MCC on metrics like product of clade posteriors and expected Robinson Foulds distance to the true tree.

For this reason, two interfaces are added to TreeAnnotator: TopologySettingService and NodeHeightSettingService and these can be implemented to specify the target topology to annotate and setting the internal node heights of the topology respectively.
See the [CubeVB](https://github.com/rbouckaert/cubevb) package for an example that implements the `TopologySettingService` and declares it as a service in the `version.xml` file.

## BEAST

### Enable close button in top bar of BeastMain dialog 

This was not working on OS X and the only way to quite the BEAST GUI was through the menu. Now clicking the close button in the top bar of the dialog as well as the CMD-Q shortcut should quit BEAST.

### Allow trees to be exported correctly up to 999999 taxa

Trees are saved in NEXUS format with node numbers in alphabetical order. 
But trees with more than 99999 taxa have nodes numbered 100000 upwards, and were not given enough space, so sorting node numbers resulted in incorrect ordering of numbers and messing up the tree.
This is fixed now, allowing trees up to a million nodes.


### Some improvements for developers

* parameters restored correctly after dimension change 
See [#1130](https://github.com/CompEvol/beast2/issues/1130)
* Robustify AdaptableOperatorSampler
See [#1129](https://github.com/CompEvol/beast2/issues/1129)
* Fix element wise update in BactrianUpDownOperator
See [#1126](https://github.com/CompEvol/beast2/issues/1126)
* Robustify Input when setting String values
See [#1125](https://github.com/CompEvol/beast2/issues/1125)


## BEAUti

### TipDatesRandomWalker picks up correct tree

It was sometimes mixing up gene and species trees, which should be fixed now.

### Indicate current theme in menu

Since v2.7, the appearance of GUIs can be changed by selecting a theme under the `View` menu in BEAUti. 
There was no way of telling which is the current theme, but now a tick appears next to the them that is active.

### some minor improvements:

* Make sure there are no numbers in tree taxon label block
See [#1125](https://github.com/CompEvol/beast2/issues/1112)
* Improved error messages
See [#1121](https://github.com/CompEvol/beast2/issues/1121)
* PackageManager resizes properly
See [BeastFX#73](https://github.com/CompEvol/BeastFX/issues/73)
* Applauncher improved layout
See [BeastFX#77](https://github.com/CompEvol/BeastFX/issues/77)

##  Logcombiner fix default type when selecting files 

In the last release, default file types for the file dialog distinguished between log and tree files in the GUI version of LogCombiner. 
Unfortunately, it was set up exactly the wrong way around, so log files were selected when tree files needed to be combined and vice versa.
This should be fixed in this release.
