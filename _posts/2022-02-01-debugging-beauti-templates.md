---
layout: post
title: BEAUti template debugging tips
tags: []
---
<p style="color:gray">1 February 2022 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

BEAUti templates can seem to behave mysteriously. Here, we have a look at a few things that can help making them behave the way you want and examine a few common issues and ways to solve them. One of the most useful things to look at is the terminal output when starting BEAUti from the command line, but if you start it by double clicking the BEAUti icon these messages can be found under the Help/Messages menu. Often, there are error messages that should give a clue to what is going wrong.


## BEAUti does not start/hangs when loading

### Reason: subtemplate has XML formatting errors

If there are errors in the XML because the XML is invalid, an exception is thrown. 
Here is what is shown when adding the invalid XML fragment `<x` randomly in the OBAMA template right after a stack trace:

````
validate and intialize error: Element type "x" must be followed by either attribute specifications, ">" or "/>".

Error detected about here:
  <beast>
      <beauticonfig spec='BeautiConfig'>
          <subtemplate id='OBAMA Bayesian Aminoacid Model Averaging'>

```

### Solution

The error message should give a clue to what should be fixed in the template.

## Template does not show up

### Reason: template is not loaded

Check that the template is actually loaded. When BEAUti starts, it tells which packages are loaded and which template XML files are processed. This is shown in the messages and should look something like this:

```
Investigating /Users/remco/Library/Application Support/BEAST/2.6/OBAMA
Processing /Users/remco/Library/Application Support/BEAST/2.6/OBAMA/templates/OBAMA.xml
```

If your template is not there, it is possibly in the wrong place: BEAUti templates are expected to be located in the package subdirectory called `templates` in an XML file.

### Solution

If the template is in the right place, you might need to reset the class path by using the `File/Clear Class Path` menu in BEAUti, and restarting BEAUti.


### Reason: subtemplate is not merged at right place

Possibly, there is a typo in the mergepoint, or the main template being loaded does not have an appropriate merge point defined. An error message is shown indicating the template is ignored. When adding X to the mergepoint in the OBAMA template, it looks like this:

```
Cannot find merge point named aux-sitemodel-panelsX from OBAMA.xml in template. MergeWith ignored.
```

### Solution

Fix typo in the mergewith attribute.


### Reason: substitution model subtemplate has not implemented `SubsitutionModel.canHandleDataType` method

Substitution models are only shown when it is compatible with the datatype of the alignment. This is determined by BEAUti by inferring the datatype of the partition, and calling the `canHandleDataType` method with the datatype that was found. If the `canHandleDataType` is not (correctly) implemented, BEAUti assumes that the substitution model is not compatible and therefore should not be shown in the drop down box.

### Solution

Implement the `SubsitutionModel.canHandleDataType` method.

## The template shows up but cannot be selected

### Reason: error in XML specifying the model

This is probably due an error in the XML in the CDATA block that defines the objects in a model. This is one of the most common problems, because the XML in the CDATA block is treated as plain text in most editors. At the time of switching between templates, an exception is thrown indicating where the problem is, which you can find in the messages output of BEAUti.

For example if the XML contains a reference to an object that do not exists (usually because of a typo), we get a message similar to this:

```
beast.util.XMLParserException: 
Error 170 parsing the xml input file

Could not find object associated with idref OBAMA_ModelIndicator.s:protein

Error detected about here:
  <beast>
      <siteModel id='OBAMA.s:protein' spec='beast.evolution.sitemodel.OBAMAModelTestSiteModel'>
          <substModel id='OBAMA_substmodel.s:protein' spec='OBAMAModel'>
              <modelIndicator>
```

### Solution

The message should give some clue to what needs to be fixed in the XML.

## Operators or other objects do not appear

### Reason: typo in ID of connector rules

Connector rules rely on exactly matching IDs, and are ignored when they cannot find matching objects. However, by default, no warnings are shown. By starting BEAUti with the `-Dbeast.debug=true` directive to java, it shows a lot more messages about which connectors are connecting objects and which cause disconnections. To start BEAUti from the terminal with these extra messages, edit the `beauti` script (`/Applications/BEAST\ 2.6.6/bin/beauti` on OS X, `~/beast/bin/beauti` on Linux) and on the last line add `-Dbeast.debug=true` like so:

```
"$JAVA" -Dbeast.debug=true -Dlauncher.wait.for.exit=true -Xms256m -Xmx8g -Djava.library.path="$BEAST_LIB" -Duser.language=en -cp "$BEAST_LIB/launcher.jar" beast.app.beauti.BeautiLauncher -capture $*
```

If we introduce a typo in a connector rule in the OBAMA template, we get a warning message like this:

```
connect: @OBAMA_freqsPriorX.s:protein -> @prior/distribution
Could not find beastObject with id OBAMA_freqsPriorX.s:protein. Typo in template perhaps?
```

### Solution

Fix the typo in the ID of the connect element.

### Reason: duplicate IDs

IDs of objects should be globally unique. If they are not, there may be a connector rule for which the condition is not satisfied, so a rule from another template may disconnect some objects in the model graph. If this happens, it shows up in the messages BEAUti produces as

```
connect: @MyOpertor.t:dna -> @mcmc/operator
```

followed further on in the messages as

```
DISconnect: @MyOpertor.t:dna -> @mcmc/operator
```

### Solution

You can make sure your objects have unique IDs by prefixing them with the BEAST package name.


