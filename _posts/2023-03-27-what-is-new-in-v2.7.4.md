---
layout: post
title: What is new in v2.7.4
tags: []
---

# BEAST:

##  Add commmand line option for setting package user directory

The `-packagedir` option allows specifying the user package directory. This was always possible using the `-Dbeast.package.user.dir=/path/to/user/dir` option as Java directive, but the command line option to BEAST makes this more convenient.

##  More robust `-version_file` handling

The `version_file` option allows specifying `version.xml` files, which, among other things, declares which java classes are available. This option is useful for developers when debugging.

Typical usage is like so:
```beast -version_file /path/to/version.xml beast.xml```

If multiple files need to be loaded, a second entry can be given, like so:
```beast -version_file /path1/to/version.xml -version_file /path2/to/version.xml  beast.xml```

Since this can become quite a long line when there are many package dependencies, the `version.xml` part is not optional, so this will be equivalent to the above:
```beast -version_file /path1/to/ -version_file /path2/to/  beast.xml```


Especially, when the path ends with an asterisk, all subdirectories will be inspected for `version.xml` files, and all of them will be loaded:

```beast -version_file /path/* beast.xml```
will look in all direct subdirectories of `/path/` and load `version.xml` if available.

##  Make beast script find BEAGLE easier on arm CPU

When BEAST is started from the command line, it only set up the BEAGLE library path on Intel based CPUs for Mac and Linux. The script has been fixed to also set up a default path on arm CPUs.

##  Allow BEAGLE usage for irreversible models, and make BEAGLE rootFrequencies input aware

BEAGLE efficiently handles substitution models that have real Eigen values and vectors. Irreversible models can have imaginary Eigen values and vectors, which BEAGLE currently does not seem to be able to handle. However, passing through the transition probability matrix is still possible. The tree likelihood handling code previously gave up using BEAGLE or produced incorrect likelihoods when there were imaginary components of the Eigen system. 

This release allows the transition probability matrix to be calculated in Java and transferred to BEAGLE, which then handles the likelihood calculation.

##  More robust AVMN operator, uncertain alignment 

The AVMN operator could produce proposals that resulted in `NaN` Hastings ratios, which seems to occur more often with uncertain alignments. This resulted in the MCMC failing, but this has been fixed with this release.


# BEAUti:

Appart from some glitches in the GUI, the following items have been fixed:

##  Fix bug that prevented BEAUti from saving files if directory was removed

This particularly annoying bug featured predominantly on OS X, but has been fixed now.

##  Make sure tip date operator is added when tips-only for MRCA prior is set

This worked in v2.6, but a bug was introduced making the operator addition fail.

##  Add functionality for auto-configuring tip date

Tip dates spanning the millenium boundary often are encoded in the taxon name as two digits, e.g. taxon1x95 for a taxon from 1995 and taxon2x14 from 2014. A facility to add 100 but only if the value is below 23 in the auto-configure tip dates panel was re-activated.

##  Allow 'Cancel' to stop BEAUti from closing

Previously, BEAUti could close, even when the `cancel` button was pressed.

##  Make short-cut keys on OS-X use command key instead of control

This makes BEAUT behave more like a native OS X application.

# Package manager 

##  Selects CBAN clone if the default is not available

Some users can not access Github (reliably). CBAN, the comprehensive BEAST archive network, is hosted on Github, and now has a clone on Bitbucket that gets updated once a day. If the package manager cannot access the Github version, it tries the bitbucket version, and only if that also fails it shows an error message.

##  Add `-dir` option to switch user package directory

Using the `-dir /path` option to the command line version of the package manager installs/uninstall package in directory `/path/`. This overrides the `-useAppDir` option if specified.


