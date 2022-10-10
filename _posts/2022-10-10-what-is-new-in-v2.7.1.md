---
layout: post
title: What is new in v2.7.1
tags: []
---

## Fix assorted launch issues on Windows, Mac and Linux

All apps should be able to launch from the command line, even from directories containing spaces, or when BEAST is installed in a directory containing spaces.
Don't echo commands in Windows batch files.

## DocMaker functional again

DocMaker is a utility for creating XML documentation like shown [here](https://www.beast2.org/xml/).
It is now accessible through the AppLauncher that comes with BEAST.

## AppLauncher: deal with superfluous icon not found messages 

On the command line, icons of apps could not always be found, leading to warning messages that cluttered the terminal output.
A fix has been put in place that should make finding the icon more robust, thus suppressing these messages.

## Logcombiner and EBSPanalyser converted to JavaFX

LogCombiner and EBSPanalyser were still using the swing library for its GUIs.
These have now been converted to JavaFX, making the look and feel more similar to BEAUti and BEAST.
Also, on high density screens, the scaling is now automatically adjusted, so they will be legible.

## BEAST: GUI version outputs log files in same directory as XML file 

BEAST, when started as GUI, put log and trees files in the wrong place: the place where BEAST was installed. Now, it uses the directory where the XML file is situated.

## BEAST: added option to specify version.xml files explicitly. 

For developers, classes that provide certain services (e.g. being available to the XML parser) need to be declared in version.xml files for packages.
It is convenient for testing not to have the package installed, but just point BEAST to the `version.xml` file to pick up new classes.
The new `-version_file` command line option for BEAST lets you specify such `version.xml` file.


## BEAUti:

A few BEAUti glitches were fixed, in particular:

* Fix splash screen for BEAUti, so at launch some feedback is available while waiting for templates to be processed
* Fix some BEAUti tooltip glitches, such as the tooltip for the tab showing up at the wrong place
* Use proper BEAUti icon in Windows instead of black box
* Remove AquaFX theme, which was only available for Mac, but locked up BEAUti.
* Add theming to Alert, so in dark mode message windows are now in dark mode as well.
* More responsive refresh forms, which did not always refresh in a timely manner.

## Clean up BEAST code and some BEAUti code

Making the code a bit nicer to work with for developers.

## DensiTree update to v3.0.1, fixing a display bug on Apple silicon

The combination of java 17 and Apple silicon caused DensiTree to layout labels of trees and mirror trees at the wrong scale.

## Update migration script for converting v2.6 packages to v2.7

This should help not only with migrating v2.6 packages to v2.7, but also XML files prepared with v2.6 that might need to be run under v2.7.




