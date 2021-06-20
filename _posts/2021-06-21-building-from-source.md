---
layout: post
title: Building packages from source
tags: []
---
<p style="color:gray">21 June 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

If you need the latest features that are already available in the source code, but are not released yet, you can still run an analysis in BEAST by building the package from the source code. Here is how:

* 0. install required software
* 1. get source code
* 2. build the package
* 3. install the package

This post assumes you are familiar with command line interface of Windows or terminal on OS X or Linux.

## Install required software

You need java JDK at least version 8, for example [AdoptOpenJDK](https://adoptopenjdk.net/). Currently, BEAST targets Java 8, and newer versions, so version 8 is recommended to prevent binary format issues.

Also required is ant, available from [Apache](https://ant.apache.org/bindownload.cgi). Make sure that `ant` is in your path.

## Get source code

Most source code resides in git repositories these days, and most packages are on github. We will use the [Babel](https://github.com/rbouckaert/Babel/) package as example in this post, which is indeed on github, and can be obtained by cloning:


```
git clone git@github.com:rbouckaert/Babel.git
```

This creates a Babel directory with all relevant code inside it. Babel is dependent on beast2 and BEASTLabs, which you can tell by the dependencies in the `versions.xml` file using `grep depends Babel/version.xml`, which produces

```
	<depends on='beast2' atleast='2.6.0'/>
	<depends on='BEASTLabs' atleast='1.9.6'/>
```

So, we want to clone these repos as well:

```
git clone git@github.com:CompEvol/beast2.git
git clone git@github.com:BEAST2-Dev/BEASTLabs.git
```

which creates the `beast2` and `BEASTLabs` directories with all sources inside. Some packages have self-contained build files that download dependencies when you build them, and it is not necessary to clone dependent packages.

## Build the package

You can build the individual packages using ant by switching directory, and run `ant` in there with the appropriate target. For building the Babel package, this will do:

```
cd beast2
ant
cd ..
cd BEASTLabs
ant addon
cd ..
cd Babel
ant addon
```

Note there is no target specified for beast2, so the default target will be used (which is `build_jar_all_BEAST`, which compiles code and runs tests), but using `ant compile-all` might safe a some time since it does not run tests.

Not every package has `addon` as target, and some build their package by default, so simply running `ant` without argument will do. To find out which targets there are, run `grep "<target" build.xml` in the package's directory, and `grep "<project" build.xml` usually reveals the default target.


## Install the package

You need to install the package by hand, as follows:

* change directory to the package directory.
	* for Windows in Users\<YourName>\BEAST\2.X\
	* for Mac in /Users/<YourName>\/Library/Application Support/BEAST/2.X/
	* for Linux /home/<YourName>/.beast/2.X/
  Here <YourName> is the username you use, and in “2.X” the X refers to the major version of BEAST, so 2.X=2.6 for version 2.6.3.
* create a directory for the package, and change directory
```
mkdir Babel
cd Babel
```
* when running `ant`, the screen output usually says where the package is zipped up. For example for Babel on my machine that would be
```
      [jar] Building jar: /Users/remco/workspace/Babel/build/dist/Babel.addon.v0.3.2.zip
```
so we want to unzip this, overwriting existing file if any, using `unzip -o package.zip` where `package.zip` the package file. In my case this would look like this:
```
unzip -o /Users/remco/workspace/Babel/build/dist/Babel.addon.v0.3.2.zip
```
* NB <b>If this is the first time</b> the package is installed, you have to reset the BEAST class path, which is stored in the `beauti.properties` file in the package directory (the `/2.X/` folder indicated above for each operating system). Either delete the line starting with `package.path=`, or run BEAUti and select the menu `File/Clear class path`

You should now be able to use the newly build package.
