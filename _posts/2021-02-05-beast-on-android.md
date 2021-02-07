---
layout: post
title: Running BEAST on Android
tags: []
---
<p style="color:gray">5 February 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>


This is not something serious to consider, but perhaps some fun thing to do to benchmark your phone, and since it can be done, someone had to do it... These are some notes on how I got to get BEAST v2.6.3 to run on Android, on a <a href="https://www.ubergizmo.com/products/lang/en_us/devices/huawei-y9-prime-2019/">Huawei Y9 prime (2019)</a> phone. Your milage may vary. Note that only the command line tools run, so for example BEAUti won't run (yet). We go through the following three steps:

* install Termux
* install java
* install BEAST v2.6.3




## Install Termux

<a href="https://play.google.com/store/apps/details?id=com.termux&hl=en">Termux</a> is a linux emulator for Android that can be obtained through the play store. The latest version can also be obtained through <a href="https://f-droid.org/en/packages/com.termux/">F-Droid</a> for which you need the F-Droid client installed.

We are going to use `wget` for downloading the other parts, so this need to be installed using

```
apt update
apt upgrade
apt install wget
```

For some reason, I had to run `apt update` and `apt upgrade` twice before `wget` became available to `apt`. Also, I set up access to storage using

```
termux-setup-storage
```




## Install java

There is no java package available for `apt`, but there is a handy script by MasterDevX that you can obtain using

```
wget https://raw.githubusercontent.com/MasterDevX/java/master/installjava
```

This downloads the script that you can run using

```
bash installjava
```

The script checks your phone's architecture (aarch64, arm, etc.) and downloads and installs the appropriate java jdk version 1.8 (in `/data/data/com.termux/files/usr/bin`).  


By the way, there is an uninstall script available as well [here](https://raw.githubusercontent.com/MasterDevX/java/master/uninstall_java.sh).


Check that java works by using

```
java -version
```

At first, I got an error saying <a href="https://github.com/MasterDevX/Termux-Java/issues/39">"Bad system call"</a>, which can be solved using

```
termux-chroot
```

After this, `java -version` returned `Java version "1.8.0_152"` plus some other information.





## Install BEAST v2.6.3

The standard Linux version of the BEAST release (without JRE) now just works without any further problems. To get it, download using `wget` like so:


```
wget https://github.com/CompEvol/beast2/releases/download/v2.6.3/BEAST.v2.6.3.Linux.tgz
```

then untar using

```
tar fxz BEAST.v2.6.3.Linux.tgz
```

and run BEAST using

```
./beast/bin/beast ./beast/examples/testHKY.xml
```

which gives the following output on my phone:

<img alt="StartBeast" width="45%" src="/images/android2.png"/>
<img alt="RunBeast" width="45%" src="/images/android1.png"/>

It runs at a respectable 11 seconds per million samples, which is what my desktop computer from less than a decade ago used to run like. If only there was a BEAGLE version for Android to speed things up!


## Installing BEAST packages

Unfortunately, the package manager that comes with BEAST has trouble accessing the package repository files (any idea how to fix this? please let me know), so packages need to be <a href="http://www.beast2.org/managing-packages/#Install_by_hand">installed by hand</a>.

First, we create a package directory in `/home/`

```
mkdir /home/.beast
```

We also have to let BEAST know about this directory, since the default location on Linux (`~/.beast/2.6`) do not appear to be recognised. To do this, we need to pass the `beast.user.package.dir` property to java, which can be done (awkwardly) from the command line like so:

```
java -Dbeast.user.package.dir=/home/.beast -cp /home/beast/lib/launcher.jar beast.app.beastapp.BeastLauncher beast.xml
```

or by editing the beast script in `/home/beast/bin/beast`, and add `-Dbeast.user.package.dir=/home/.beast` to the java commands (2 instances in the last three lines of the script).

Then, packages can be downloaded using `wget`, unzipped in a subdirectory of `/home/.beast`, and should be available once `/home/.beast/beauti.properties` has been deleted.
