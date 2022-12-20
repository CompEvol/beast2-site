---
layout: post
title: What is new in v2.7.3
tags: []
---

## Allow all packages to be installed, not just a small subset

Due to an issue with the class loader, BEAST and BEAUti failed to start when more than 24 packages were installed.
This is fixed in this release, so that all available packages can be installed.

## Add support for various packages

The migration of packages from v2.6 to v2.7 required some access changes for various packages.
With this release, more packages become availble for the v2.7 version of BEAST.

## Add `-packagedir` and `-version_file` CLI options to BEAST, BEAUti

These command line options are mainly useful for package developers.

The `-packagedir` option sets the `beast.user.package.dir` property, and thus the BEAST user directory where packages are loaded from, and packages in the default user package directory are ignore

The `-version_file` options allows specifying one or more `version.xml` files to be loaded. 
Since these files containing information about which classes are made available by a package, this can be useful when debugging packages from an IDE.

## Extend XML variable substitution to text nodes

BEAST has the `-D` command line option for defining variables.
For example, when running BEAST with `-D chainLength=1000000` every occurance of `$(chainLength)` in attributes in the XML is replacecd by `1000000`.
This is now extended to inline text as well, such as for example trait set definitions.


## BEAUti: fix bugs in templates

A few bugs were created when migrating the BEAUti templates from v2.6 to v2.7, which causes a superfluous error messages to appear in BEAUti messages output.
These have been fixed now.

## BEAUti more robust when reloading XML into BEAUti

XML files generated with BEAUti should be able to be loaded back into BEAUti. This did not work sometimes in v2.7.2 due to some errors in BEAUti templates, which have been fixed now, so reloading XML should work again.



## Update migration script for converting v2.6 packages to v2.7

This should help not only with migrating v2.6 packages to v2.7, but also XML files prepared with v2.6 that might need to be run under v2.7.


## What was new in v2.7.2?

BEAST v2.7.2 was a minor release with a few small changes to support migration of packages from v2.6 to v2.7.



