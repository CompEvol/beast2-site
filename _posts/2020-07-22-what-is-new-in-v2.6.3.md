---
layout: post
title: What is new in v2.6.3
tags: []
---

## BEAST new `-DF` and `-DFout` options

With the `-D name=value` option, attribute values of the XML containing `$(name)` will be replaced by value, which is quite handy to parameterise the XML. However, command lines can become a bit unwieldy when there are many parameters, or values are quite large. Also, it is not possible to replace large sections, like all sequences.

Now, BEAST has a `-DF` option that specifies a file in say JSON format that defines name/value pairs, which is what a JSON dictionary provides quite naturally, and allows for multiple lines for values. For example, like so:

```
{
"sequences":"
<sequence taxon='D4Brazi82'>            ATGCGATGCG        </sequence>
<sequence taxon='D4ElSal83'>            ATGCGATGCG        </sequence>
...
<sequence taxon='D4Thai84'>             GTGCGATGCG        </sequence>
";
"datetrait":"D4Brazi82  = 1982,
                D4ElSal83  = 1983,
...
                D4Thai84   = 1984"
}
```

where `...` means many more of the same. This allows for using the same analysis with multiple data sets (or multiple analyses with the same data set), which can be handy for well calibrated simulation studies or situations where the data set rapidly evolves.

The resulting XML, where user defined parameters are replaced by the information from the JSON file, is by default written to a file with the same name as input XML file, but with `.out` added before `.xml` (so input `beast.xml` becomes output `beast.out.xml`). The output file can be specified using the `-DFout` option, e.g.

```
/path/to/beast/bin/beast -DF definitions.json -DFout result.xml beast.xml
```

If no output is desired, you can output to `/dev/null` using `-DFout /dev/null` on OS X and Linux, or `-DFout NUL` on Windows.




## Default values for user defined values

BEAST has the `-D` option to pass values for user defined parameters. For example, specifying `chainLength='$(chainLength)'` in the XML allows you to run BEAST with

```
/path/to/beast/bin/beast -D chainLength=1000000 beast.xml
```

and in the XML `chainLength='$(chainLength)'` is interpreted as `chainLength='1000000'`.


By specifying `chainLength='$(chainLength=1000000)'` in `beast.xml`, the value 1000000 is assumed to be default, so no `-D` option is required. However, it can be specified if desired.



## LogAnalyser new `-tag` option

Having a command line option for LogAnalyser to only show and calculate a subset of items from a log file can be useful when monitoring a number of running chains where one is not interested in the majority of the items in a log file. Currently, you have to wait through the calculation of all entries in the log file, which with large (or many) log files can take quite a bit of time.

The `-tag` option takes a comma separated list of items in the log file, so it could look something like this:

```
/path/to/beast/bin/loganalyser -tag treeHeight,posterior,prior beast.log
```

which then only outputs the three height, posterior, and prior entries. It works with the `-oneline` option as well.

## Let `Input.setValue()` ignore empty lists (instead of throwing exception) 

This helps the `applauncher` deal with lists of files (as in for example the CladeSetComparator tool from Babel).

## User data type lengths are not required to be equal any more

This helps running the BEASTvntr package for microsatellite data.


## RandomTree deals with calibrations in date-forward mode

A bug is fixed for generating random starting trees when tip dates are in date-forward, and an MRCA prior with hard bounds are used. A similar fix was applied to SimpleRandomTree in the [BEAST-labs](https://github.com/BEAST2-Dev/BEASTLabs) package.

## Robustify RPN calculator

RPN calculator is a class for on the fly calculations of simple arithmatic formulas in reverse Polish notation, which can be handy for logging and putting priors on combinations of parameters. An input `argnames` was added so that expressions can formulated in term of names argnames. Previously, argument names were derived from the IDs of parameters, which wrecked havock if these IDs contained spaces.

## Robustify resume

When logging trees and trace logs with different frequency, resuming was not possible when the end states of some of the logs differed. The way to solve this was to edit the tree or trace log and remove lines in a text editor in order to ensure equal end states. This was error prone and tedious. This release automatically removes lines at the end of logs so that the end state is the same. 

Furthermore, it detects whether the last line of the trace or tree log was corrupted. This can happen when BEAST was interrupted during the writing of the log files (which is not uncommon when running jobs on servers under a time limit). BEAST now automatically removes these corrupt lines, and adjust other logs to ensure all logs resume from the same state.

## The usual improvements:

Some error messages are made a bit clearer.

API access for developers has improved further by relaxing some access (e.g. from private to protected) or adding access methods.

## Installation

To install v2.6.3, there are individual binaries for the different operating systems available via links on the [main page](/), but when you have v2.6.0, v2.6.1 or v2.6.2 installed it is easy to upgrade. There are the following options:

* start BEAUti, and you will be prompted to install BEAST v2.6.3.
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

