---
layout: post
title: What will change in v2.6.0 for developers 
tags: []
---
<p style="color:gray">June 2019 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

# Must know changes!



## Signature changes

Some methods have become less restricted, which means that derived classes may need to have the same restriction loosened for overriding methods. This is very easy to spot, since the java compiler will refuse compiling with a message saying that it cannot reduce visibility of methods.

There should not have been any other signature changes.



## Class loading

The main change follows from the fact that we use a custom class loader ([`beast.core.BEASTClassLoader`](https://github.com/CompEvol/beast2/blob/master/src/beast/util/BEASTClassLoader.java]) to dynamically load packages. This impacts code that uses reflection to create objects. If you use `Class.forName()` in a package, you should replace it with [`BEASTClassLoader.forName()`](https://github.com/CompEvol/beast2/blob/9386ba5775e4168c7b60d970e6cb7a35b535f1bb/src/beast/util/BEASTClassLoader.java#L61). In general, any reference to class loaders should probably be needed to change to [`BEASTClassLoader`](https://github.com/CompEvol/beast2/blob/master/src/beast/util/BEASTClassLoader.java]).




# Nice to know changes

## Launching BEAST

It used to be possible to launch BEAST with `java -jar beast.jar ...`, but since this clashes with the way packages are loaded, this very quickly can result in `java.lang.VerifyError`s accompanied by a scary bytecode dump. The new way to start BEAST is through `BEASTLauncher`, which is the default in `launcher.jar`:

`java -jar /path/to/launcher.jar ...`

There is no need to use the `-Dload.beast.user.packages=true` directive any more (this is in fact ignored now). In case you do not want to load installed packages, you can still use `-Dbeast.user.package.dir=/path/to/non-default/package/dir`.



## Installing packages by hand

The process of installation of packages by hand has not changed from v2.5.x. Be aware to reset the class path after installing the package, since only those packages listed in the `beauti.properties` file will be loaded.


## Automatic methods section generation

If you install the [Beasy](https://github.com/rbouckaert/beasy) package, there will be an extra menu in BEAUti to `Help/Methods section` that attempts to automatically generate a methods section, e.g. like so:

	
	There are 3 partitions (RSV2_1, RSV2_2 and RSV2_3) with 209, 210 and 210 sites respectively.
	
	All partitions individually have a gamma site model and HKY substitution model (Hasegawa et al. (1985)) with kappa log-normally distributed (mean-log=1.0 and stdev-log=1.25) and empirical frequencies.
	
	All partitions share a strict clock with clock rate log-normally distributed (mean-log=-5.0 and stdev-log=1.25).
	
	There is a single tree with dated tips (in dates not ages) using constant coalescent tree prior with population size 1/X distributed.
Relative substitution rates among all partitions are estimated.

	This analysis is for BEAST (Bouckaert et al. (2014)).
	
	References:
	Hasegawa, M., Kishino, H., & Yano, T. (1985). Dating of the human-ape splitting by a molecular clock of mitochondrial DNA. Journal of Molecular Evolution, 22(2), 160–174. doi:10.1007/bf02101694
	Bouckaert, R., Heled, J., Kühnert, D., Vaughan, T., Wu, C.-H., Xie, D., … Drummond, A. J. (2014). BEAST 2: A Software Platform for Bayesian Evolutionary Analysis. PLoS Computational Biology, 10(4), e1003537. doi:10.1371/journal.pcbi.1003537

The default is text output, but Latex and markdown is also supported.

The text is based on template texts, and Beasy goes through all template directories to collect [`methods.csv`](https://github.com/rbouckaert/beasy/blob/master/templates/methods.csv) files, comma separated files with comments starting with # characters. The main thing to add for your packages are probably lines with object IDs as found in BEAUti templates (or complete class names). Each line should have a comma separated list of phrases associated with them, that allow you to specify the text being produced. If you want to expand inputs, use input name as item between commas, or @-sign before input name, and for citations add a cite command, e.g., `cite(ExponentialRelaxedClock.c:$(n))`.

Because this is a comma separated file, to get a comma in the text, you need the comma-entity: &amp;comma;.

If you need something more sophisticated you can derive from [`methods.MethodsText`](https://github.com/rbouckaert/beasy/blob/master/src/methods/MethodsText.java) and implement the [`getModelDescription`](https://github.com/rbouckaert/beasy/blob/7c2beac4693b30f15f2419126cb3bc63035d6d86/src/methods/MethodsText.java#L49) method to generate a list of phrases making up a sentence. For example, for the site model text is generated that distinguishes between with/without rate heterogeneity and with/without proportion invariable sites (see [SiteModelMethodsText](https://github.com/rbouckaert/beasy/blob/master/src/methods/implementation/SiteModelMethodsText.java)).



