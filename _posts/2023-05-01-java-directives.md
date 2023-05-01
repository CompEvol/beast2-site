---
layout: post
title: Java directives
tags: []
---
<p style="color:gray">1 May 2023 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

This post lists a few settings that can be passed to BEAST, BEAUti, etc. and change the behaviour of these programmes.
They are mostly for developers. 
The way to use them is a java directives that can be passed to java using the `-D` argument, for example, use

```
$java -Dbeast.debug=true -cp launcher.jar beast.pkgmgmt.launcher.BeastLauncher beast.xml
```

to run BEAST with the `beast.debug` flag set to true.

In programs, these values can be obtained using 

```
String property = System.getProperty(propertyName);
```


# BEAST related

## "no.beast.popup"

When BEAST starts, packages are loaded and package dependencies checked.
If a dependency is missing, a warning is printed on screen notifying that the analysis may not run as expected.

## "prefix"
Determines the prefix in the headers for LogAnalyser

## "beast.install.dir"

Where the package manager should install packages.

## "beast.system.package.dir"

Where system-wide packages can be found (defaults to `\Program Files\BEAST2\2.7` on Windows, `/Library/Application Support/BEAST2/2.7` on OS X and `/usr/local/share/beast2/2.7` on Linux).

## "beast.user.package.dir"

Where user installed packages can be found (defaults to `\Users\<user name>\BEAST2\2.7` on Windows, `~/Library/Application Support/BEAST2/2.7` on OS X and `~/.beast/2.7` on Linux).

For developers, it can be useful to change this to a non-existing director to prevent any installed packaged to be loaded, so there is no interference from installed version of the package you are debugging.

## "BEAST_PACKAGE_PATH"

The package path where the package manager first looks for packages. Can also be set as environment variable `BEAST_PACKAGE_PATH`.

## "beast.debug"

If set to true, more debug information is produced, and more checks are done in the code. Can run less efficient because of these extra checks.

## "beast.instance.count"

Number of instances that divide site patterns amongst number of threads used in threaded treelikelihoods. Can easier be set using the `-instances` option to BEAST.

## "beast.is.junit.testing"

Flag to indicate a test is being run. This allows behaviour in test code to differ from regular running of the code.

## "beast.log.level"

Determines the amount of logging and can have values error,warning,info,debug, and trace.
Can easier be set using the `-loglevel` option to BEAST.

## "beast.resume"

Flag to indicate we are resuming a BEAST run.

## "beast.useWindow"

Flag to indicate BEAST is using a console window instead of terminal output.

## "file.name.prefix"

Determines the prefix for the names of log files: will be pre-pended to each filename specified in Loggers.
Can easier be set using the `-prefix` option to BEAST.


## "state.file.name"

Determines the name of the state file to use.
Can easier be set using the `-statefile` option to BEAST.

## "user.dir":	beast/base/core/ProgramStatus.java

Directory of last opened file. 
BEAST and BEAUti defaults to this directory when opening a file.
Its value is stored in the `beauti.properties` file.



# BEAGLE related 

## "java.only"
Do not use BEAGLE, but use the built-in java treelikelihood.

## "beagle.resource.order"

Sets order of available BEAGLE resources to be used. 

This is more conveniently set by the `-beagle_order` flag to BEAST.

## "beagle.preferred.flags"
Flag to indicate which resource to use: CPU, SSE, GPU, etc.
Also, which precision to use: single or double.

This is more conveniently set by the `-beagle_CPU`, `-beagle_SSE`, `-beagle_GPU` flag to BEAST as well as the `-beagle_double` and `-beagle_single` flags.


## "beagle.required.flags"

Flag indicating requirements for BEAGLE, including whether the substitution model can return complex Eigen values and vectors.


## "beagle.scaling"

Flag to set BEAGLE scaling: auto 1 << 6, none 1 << 7, always 1 << 8 or dynamic 1 << 19 

Easier set via the `-beagle_scaling` flag for BEAST.

## "beagle.rescale"

Frequency at which BEAGLE rescales when using dynamic scaling (10000 default).



# Other directives 

## "java.library.path"

Library path used for loading system dependent libraries, like BEAGLE.
Should be set together with the `LD_LIBRARY_PATH` environment variable to have effect.
Setting one or the other may result in libraries not being loaded.

## "java.class.path"

Path used for loading java libraries (jar files). 
Easier set through the `-cp` argument to `java`.

## "java.version"

Java version.

## "os.name"

Operating system name: Windows, Mac, Linux.

## "file.separator"

Character used to separate files  ("\" or "/").

## "path.separator"

Character used to separate paths (":" or ";").

## "line.separator"

Character used to separate lines.

## "user.home"

User's home directory -- depends on operating system.
