---
layout: post
title: How to run older versions of packages
tags: []
---
<p style="color:gray">1 June 2025 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

Replicability of analyses is a major consideration in the design of BEAST2.
Here, we will have a look at how to reproduce an analysis performed by someone else, assuming you have the BEAST2 XML available.

BEAUti registers which versions of packages were used when creating the XML.
This information is stored in the `required` attribute at the top level `beast` element in the XML, and can look something like

```
<beast ... required="BEAST.base v2.7.7:ORC v1.1.0:BEASTLabs v2.0.2">
```

when using an optimised relaxed clock.
This allows replicating the analyses with these exact version of packages, which may change a little from later version of packages.

To do this, first these older packages should be installed, then BEAST must be told to use these older versions.

## Installing (multiple) older versions of packages

Various version of the same package can be installed at the same time.
Be aware that by default, the latest version will be used.

### From the package maneger GUI

Start the package manger via the `File => Manage packages` menu in BEAUti.
* Select the package you want to install, say ORC.
* Uncheck the `Latest` check box (bottom left corner)
* Click the `Install` button. 
* A dialog pops up where you can select the version. Click `OK` and the package with that particular version will be installed.


[installing packages](/images/usingOldPackages.png)

### From the command line

For installing many package versions, the command line may be easier. Assuming the `packagemanager` that comes with BEAST2 is in the path:

`packagemanager -add <PackageName> -version <PackageVersion>`

will install package with name `PackageName` and version `PackageVersion`.
For example:

```
~> packagemanager -add ORC -version 1.1.0
Packages user path : /Users/remco/Library/Application Support/BEAST/2.7
Access URL : https://raw.githubusercontent.com/CompEvol/CBAN/master/packages2.7.xml
Getting list of packages ...Done!

Determine packages to install
Start installation
Package ORC is installed in /Users/remco/Library/Application Support/BEAST/2.7/archive/ORC/1.1.0.
```

## Running older versions of packages

If you have v2.7.0 installed, but updated the BEAST package to v2.7.7, it will run everything with v2.7.7.

The crude but easy option to run a different version of BEAST, say v2.7.6, is to remove the BEAST package directory, and start BEAST from the v2.7.6 release (for Windows in `Users\<YourName>\BEAST\2.7\BEAST`, for Mac in `/Users/<YourName>\/Library/Application Support/BEAST/2.7/BEAST`, for Linux `/home/<YourName>/.beast/2.7/BEAST`).

Be aware that when you run a newer version of BEAST, the BEAST package is automatically updated to the newer version: so, if you run v2.7.0 the first time, that is the version of the BEAST package that will be installed, unless there is already a newer version installed. Running v2.7.0, then v2.7.7, then v2.7.0 results in v2.7.7 being used for the last run. The reason for this is that updating to newer versions only require the package to be updated, not the whole BEAST installation (which, since it includes a java runtime, takes up much more space).

A more elegant solution is to install the v2.7.7 package inside the `archive` directory inside the BEAST package directory, specify BEAST v2.7.6 in the XML in the 'required' attribute of the `beast` element, and start BEAST with the `strictversions` option. This should allow you to run different versions of the package.

Older releases are available from https://github.com/CompEvol/beast2/releases.

Another option is to set the `BEAST_USER_PACKAGE_DIR` environment variable to different values when running the different versions of BEAST.
For example 

```
set BEAST_USER_PACKAGE_DIR=/Users/rbou019/beast2.7.0/ when running BEAST v2.7.0
```

and 

```
set BEAST_USER_PACKAGE_DIR=/Users/rbou019/beast2.7.5/ when running BEAST v2.7.5 
```

will ensure when running BEAST v2.7.0 packages will be stored in different locations then when running v2.7.7. 
Therefore, packages will not be overwritten.

