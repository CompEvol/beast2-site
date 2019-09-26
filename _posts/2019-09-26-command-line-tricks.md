---
layout: post
title: BEAST v2.6 Command line tricks
tags: []
---
<p style="color:gray">26 September 2019 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>


Running BEAST or any of the other programs from the command line can be more convenient (for example when repeatedly calling `BModelAnalyser` on different log files), and (especially for Windows) can speed things up considerably. So, it may be worth learning to run programs from a terminal. But first note this:

## <span style="color:red">*Do not use `java -jar beast.jar`*</span>

In older releases it was possible to run BEAST through `java -jar beast.jar` or `java -cp beast.jar beast.app.beastapp.BeastMain`, but due to changes in class loading in BEAST v2.6, this will lead to problems when you have packages installed, and produce errors it cannot find some classes and `java.lang.VerifyError` messages. The replacement command is `java -jar launcher.jar`, but different operating systems behave a bit differently, so here are the specifics for Windows, Mac and Linux. 

## Windows

To run BEAST from the command prompt, open the program `cmd` from the Windows start menu. I will assume you have BEAST installed on your desktop and user name is `BEASTUser`, so there will be a folder `c:\Users\BEASTUser\Desktop\BEAST` with `BEAST.exe`, `BEAUti.exe` etc. in there. If you have it in a different place, you should replace `c:\Users\BEASTUser\Desktop\` with where the BEAST folder is on your computer. Just calling the exe-files does start these programs, but does not allow visual feedback for BEAST or AppLauncher, which makes it hard to determine whether a program has finished, or diagnose errors.

First, make sure you have the file `launcher.jar` in the `lib` directory (`c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar)`. If the file does not exist, you can download it from [here](https://github.com/CompEvol/beast2/releases/download/v2.6.0/launcher.jar).

If you have BEAST without java installed, you must have java installed separately, and can use

```
java -jar c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar -threads 4 beast.xml
```

to start BEAST on `beast.xml` with 4 threads. To run the other programs,


`java -cp c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar beast.app.tools.AppLauncherLauncher`
`java -cp c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar beast.app.beauti.BeautiLauncher`
`java -cp c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar beast.util.LogAnalyser`
`java -cp c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar beast.app.tools.LogCombinerLauncher`
`java -cp c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar beast.app.treeannotator.TreeAnnotatorLauncher`

You may add command line arguments where appropriate (e.g. `java -cp c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar beast.app.tools.AppLauncherLauncher BModelAnalyser -file beast.log` to run BModelAnalyser on file `beast.log`).


### Windows BEAST v2.6.2 and better

From the next release (probably v2.6.2) onward, there will be a folder `BEAST/bat` that contains batch files to start each of these programs, and you can just call

```
c:\Users\BEASTUser\Desktop\BEAST\bat\beast.bat -threads 4 beast.xml
```

to start BEAST on `beast.xml` with 4 threads. Output of BEAST will be shown in the cmd terminal, so no other windows will open, and you will notice the difference in speed. Another example is to run BModelAnalyser on file `beast.log` like so:

```
c:\Users\BEASTUser\Desktop\BEAST\bat\applauncher.bat BModelAnalyser -file beast.log
```


For BEAST v2.6.0 and v2.6.1, you can create the `bat` folder inside the `BEAST` folder, and dowload these batch files from here: [applauncher](https://raw.githubusercontent.com/CompEvol/beast2/master/release/Windows/bat/applauncher.bat), [beast](https://raw.githubusercontent.com/CompEvol/beast2/master/release/Windows/bat/beast.bat), [beauti](https://raw.githubusercontent.com/CompEvol/beast2/master/release/Windows/bat/beauti.bat), 
[loganalyser](https://raw.githubusercontent.com/CompEvol/beast2/master/release/Windows/bat/loganalyser.bat), [logcombiner](https://raw.githubusercontent.com/CompEvol/beast2/master/release/Windows/bat/logcombiner.bat), and/or [treeannotator](https://raw.githubusercontent.com/CompEvol/beast2/master/release/Windows/treeannotator/beauti.bat), and put the in the `c:\Users\BEASTUser\Desktop\BEAST\bat` directory. 

<span style="color:red">Make sure you have launcher.jar in the lib directory</span>


If not, download [launcher](https://github.com/CompEvol/beast2/releases/download/v2.6.0/launcher.jar) and put in the lib directory.


### Increasing memory after a `java.lang.OutOfMemoryError` on Windows

To increase memory available to BEAST or any of the other program you can use the `-Xmx` directive for java. For example, to allocate 16GB of memory for BEAST, use

```
java -Xmx16g -jar c:\Users\BEASTUser\Desktop\BEAST\lib\launcher.jar -threads 4 beast.xml
```

If you have batch files installed, you can edit the default settings. Note, there will be two places to update (actually one, but the script is for both with and without java included so make sure to edit the right one).

## Mac

Assuming BEAST is installed in the Application folder, you can call BEAST programs from a terminal using

```
/Applications/BEAST\ 2.6.0/bin/beast -threads 4 beast.xml
```

to start BEAST on `beast.xml` with 4 threads. If you have BEAST installed in a different location, you need to replace `/Applications/BEAST\ 2.6.0` with the location where `BEAST\ 2.6.0` can be found. All programs (applauncher, beast, beauti, densitree, loganalyser, logcombiner, packagemanager, and treeannotator) are in the `bin` folder, and can be started likewise, for example 

```
/Applications/BEAST\ 2.6.0/bin/applauncher BModelAnalyser -file beast.log
```

to run BModelAnalyser on file `beast.log`.


### Increasing memory after a `java.lang.OutOfMemoryError` on Mac

To increase memory available to BEAST or any of the other program you can use the `-Xmx` directive for java. For example, to allocate 16GB of memory for BEAST, use

```
java -Xmx16g -jar /Applications/BEAST\ 2.6.0/lib/launcher.jar -threads 4 beast.xml
```

You can also edit the scripts in the `bin` directory and call the script instead. Note, there will be two places to update.


## Linux

Assuming BEAST is installed in your home directory (as `~/beast`), you can call BEAST programs from a terminal using

```
~/beast/bin/beast -threads 4 beast.xml
```

to start BEAST on `beast.xml` with 4 threads. If you have BEAST installed in a different location, you need to replace `~/beast` with the location where `beast` can be found. All programs (applauncher, beast, beauti, densitree, loganalyser, logcombiner, packagemanager, and treeannotator) are in the `bin` folder, and can be started likewise, for example 

```
~/beast/bin/applauncher BModelAnalyser -file beast.log
```

to run BModelAnalyser on file `beast.log`, or

```
~/beast/bin/packagemanager -add bModelTest
```

to install the bModelTest package.


### Increasing memory after a `java.lang.OutOfMemoryError` on Linux

To increase memory available to BEAST or any of the other program you can use the `-Xmx` directive for java. For example, to allocate 16GB of memory for BEAST, use

```
java -Xmx16g -jar ~/beast/lib/launcher.jar -threads 4 beast.xml
```

You can also edit the scripts in the `bin` directory and call the script instead. Note, there will be two places to update.


# For developers testing your packages

For developing packages, there are a few peculiarities to keep in mind due to the way packages are loaded.

### In an IDE

Setting up a class path to include relevant classes, and calling `BeastMain` directly can work in an IDE, provided every class used is in the class path. Classes loaded from package will not be accessible and lead to `VerifyError`s. If you give the java directive `-Dbeast.user.package.dir=NONE`, no packages will be loaded (unless you have packages installed in the directory `NONE`).

### From a terminal

The most robust way is to build your package (by running `ant addon`, or just `ant` for most `build.xml` files), and [install your packages by hand](http://www.beast2.org/managing-packages/#Install_by_hand). In summary,

* create folder ~/.beast/2.6/MyPackage for a package called MyPackage (change as appropriate)
* cd ~/.beast/2.6/MyPackage
* unzip `/Users/username/workspace/starbeast3/build/dist/MyPackage.addon.v0.0.1.zip`
   (replace `username` with your user name).
* <span style="color:red">Before running BEAST, make sure you cleared the class path in `~/.beast/2.6/beauti.properties`</span> for instance, by deleting `~/.beast/2.6/beauti.properties`

Once the package is installed, you can run BEAST from a terminal (as outlined above for your operating system). Once you installed the package and cleared the class path, BEAST will restore it and every time you rebuild and install your package again, you do not need to clear the class path again (though it does not hurt if you do).




