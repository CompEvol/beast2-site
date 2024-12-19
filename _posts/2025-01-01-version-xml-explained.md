---
layout: post
title: `version.xml` explained
tags: []
---
<p style="color:gray">1 January 2025 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

Each BEAST2 package has a `version.xml` file that has several purposes:

* declaring the name and version of the package
* defining dependencies of the package
* listing services that can be used by other packages
* listing apps that can be launched by the `applauncher`
 
## Package name and version 

The `version.xml` file has the `package` element at top level with attributes `name` and `version`.

``` xml
<package name="MyPackage" version="1.0.1">
   ...
</package>
```

## Package dependencies

Inside the `package` element, dependencies to other packages can be defined together with their versions. 
Usually, `BEAST.base` and `BEAST.app` are dependencies. The `atleast` and `atmost` attributes put bounds on which versions are acceptable. 
If not `atmost` is defined, only `atleast` version is checked when BEAST2 starts.
A typical set of dependencies looks like so:

``` xml
    <depends on="BEAST.base" atleast="2.7.5"/>
    <depends on="BEAST.app" atleast="2.7.5"/>
    <depends on="OtherPacakge" atleast="1.0.0" atmost="1.9.0"/>
```

## Package services

The `version.xml` file allows you to specify which services are available for external packages, the XML parser, etc.. A service is specified by adding a `service` element *inside* the `package` element, e.g. like so:

``` xml
    <service type="beast.base.core.BEASTInterface">
        <provider classname="mypackage.base.SomeBEASTObject"/>
        <provider classname="mypackage.base.AnotherBEASTObject"/>
        <provider classname="mypackage.tools.SomeTool"/>
    </service>      
```

A service has a `type` and contains one or more `providers` which indicate which classes provide the service. The following services are recognised by `beast.base`:


* `beast.base.core.BEASTInterface` should be used for any BEAST object that can be created through XML files.
* `beast.base.evolution.datatype.DataType` to be used when new data types are made available in a package.
* `beast.app.inputeditor.InputEditor` for any input editor providing GUI components for BEAUti. **Note**, packages should not put input editors in `beast.app.beauti` or `beast.app.draw` any more.
* `beast.app.inputeditor.AlignmentImporter` for classes that help with importing alignments from files.
* `beast.app.beauti.BeautiHelpAction` for actions that extend the Help menu in BEAUti.
* `beast.app.beauti.PriorProvider` for actions that extend the `Add priors` button in the prior panel in BEAUti.


## Package apps

Applications inside a package should be classes with a `main` method, so they can be started with `applauncher` or via BEAUti using the `File => Launch Apps` menu.

``` xml
	<packageapp description="ModelAnalyser"
              class="mypackage.tools.ModelAnalyser"
              args=""
              icon="mypackage/data/icon.png"
            />
```

* `description` is the name displayed in BEAUti's App Launcher dialog
* `class` defines the class for which the `main` method is called
* `args` are default extra arguments passed to `main`
* `icon` specifies the location in the jar file that comes with the package to be displayed in App Launcher


## The `-version_file` argument

When debugging inside an IDE, it can be useful to specify one or more `version.xml` files to be used for packages that are not installed -- BEAST, BEAUti and applauncher have a `-version_file` argument that allows you to do this. 
When `-version_file dir/*` is used, all `version.xml` files in sub-directories of `dir` are included, which is usually all you need when all packages under development are inside the `dir` directory.

