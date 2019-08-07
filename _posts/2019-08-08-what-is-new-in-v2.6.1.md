---
layout: post
title: What is new in v2.6.1
tags: []
---

## Changes

This is a small patch release, mainly to fix a bug in BEAUti so that template of "last" package is recognised. This problem showed up as some packages not being available in BEAUti, even though they were installed.

Some other small fixes include LogAnalyser, which now can deal with spacees in trace log names (and thus fixes a problem with NSLogAnalyser for the nested sampling package). Further, LogAnalyser now adds a sample number and file name column when using the `-oneline` argument.

Finally, there are a few fixes for the random local clock to make sure the rates are scaled correctly when the scaling option is used.


## Installation

To install v2.6.1, there are no individual binaries for the different operating systems, but when you just install v2.6.0 it is easy to upgrade. There are the following options:

* start BEAUti, and you will be prompted to install BEAST v2.6.0.
* if you switched off automatic package update checks earlier, start BEAUti and go to the package manager (menu `File/Manage packages`) and select the BEAST package, then click the `install/update` button.
* if you are on a headless machine, you can use the package manager
```
/path/to/beast/bin/packagemanager -update
```
if you want to be prompted to confirm, or
```
/path/to/beast/bin/packagemanager -updatenow
```
to install without confirmation. Here `/path/to/beast` is the path to where you installed BEAST.

