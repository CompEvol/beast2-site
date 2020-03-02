---
layout: post
title: What is new in v2.6.2
tags: []
---

##	Allow operator schedule changes in BEAUti

The [BEASTLabs](https://github.com/BEAST2-Dev/BEASTLabs/) has various operators implemented that use Bactrian kernels (Yang &amp; Rodríguez, 2013), which in genereal are slightly more efficient than the standard BEAST operators. The [BactrianOperatorSchedule](https://github.com/BEAST2-Dev/BEASTLabs/blob/master/src/beast/evolution/operators/BactrianOperatorSchedule.java) automatically replaces standard BEAST operators with Bactrian versions. You could already replace the standard operator schedule with this new one by editting the XML, but in v2.6.2, you can do this in BEAUti.

The operator schedule can be changed in the operators panel. By default, this panel is not shown, but it can be made visible by selecting the `View/Show operators panel` menu.

##	Add batch file for Windows command line usage

The Windows executables (BEAST.exe, BEAUti.exe, etc.) do not recognise command line options. Since running BEAST in the cmd-prompt tends to be faster than running it inside the console that comes with BEAST.exe, batch files are added to run BEAST under the command prompt. The batch files are in the `BEAST\bat` directory, so you can run it as follows:

```
\path\to\BEAST\bat\beast.bat -threads 2 beast.xml
```

where `\path\to` the path to where BEAST is installed.

Another benefit is that error messages are printed in the cmd window, which should make it easier to track some bugs. Further, you can now run the `loganalyser`, `logcombiner`, `treeannotator` utilities from the cmd prompt, which can be quite handy in particular when dealing with many files.

This BEAST release for Windows was built with the latest version of [launch4j](http://launch4j.sourceforge.net/), since a previous versions caused starting issues with Windows 10.

##	Fix quantile parameterisation of relaxed clocks

There were some issues with the UCRelaxedClockModel under other parameterisations than the categories parameterisation (which is the standard parameterisation). Zhang &amp; Drummond, 2020 have been using the rates parameterisation, which then highlighted issues with the quantiles parameterisation. This is fixed now, so all three parameterisations for relaxed clocks work now.

##	The usual improvements:

Some error messages are made a bit clearer.

API access for developers has improved further by relaxing some access (e.g. from private to protected) or adding access methods.

## Installation

To install v2.6.2, there are individual binaries for the different operating systems available via links on the [main page](/), but when you have v2.6.0 or v2.6.1 installed it is easy to upgrade. There are the following options:

* start BEAUti, and you will be prompted to install BEAST v2.6.2.
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


### References

Yang Z, Rodríguez CE. Searching for efficient Markov chain Monte Carlo proposal kernels. Proceedings of the National Academy of Sciences. 2013 Nov 26;110(48):19307-12.

Zhang R, Drummond A. Improving the performance of Bayesian phylogenetic inference under relaxed clock models. Under Review. 2020
