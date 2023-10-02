---
layout: post
title: What will change in v2.7.0 for developers 
tags: []
---
<p style="color:gray">22 August 2022 by <a href='mailto:r.bouckaert@auckland.ac.nz'>Remco Bouckaert</a></p>

## Code is reorganised & split into BEAST.base and BEAST.app 

All java packages have been reorganised to follow a more logical organisation (IMHO) and without circular dependencies.
BEAST.base still lives in the [beast2](https://github.com/CompEvol/beast2/) repository.
BEAST.app is in the [BeastFX](https://github.com/CompEvol/BeastFX) repository, and contains code for BEAST, BEAUti, TreeAnnotator, LogCombiner, etc.

If you don't plan on adding BEAUti features, only the beast2 repository is needed.

## BEAST 2 uses java 17

BEAST now uses Java 17 instead of Java 8, which might give a small performance boost for some models and allows access to Java 17 features.

It is recommended to install the JDK + FX from Azul, since it allows painless integration of JavaFX. It can be downloaded from here: [https://www.azul.com/downloads/](https://www.azul.com/downloads/).

## BEAUti is now JavaFX based

BEAUti got a facelift and uses JavaFX instead of Swing.
The reason for the move to JavaFX is that is allows automatic user interface testing in Java 17. 
Automatic testing in the past has been crucial in identifying problems and without it it would not be responsible to move to Java 17 and we would be stuck with Java 8.

Packages that want to provide BEAUti input editors have to be aware that these are now JavaFX based, which may require a bit of code changes.

## Java libraries are updated

* commons-math => commons-math3-3.6.1.jar		
* antl => antlr-runtime-4.10.1.jar	
* fest.jar => assertj-core-3.20.2.jar, assertj-swing-junit-3.17.1.jar, assertj-swing-3.17.1.jar
* junit-4.8.2.jar => junit/junit-platform-console-standalone-1.8.2.jar

Previously, all libraries were loaded by the same class loader, which meant that if Package A uses library X version 1 and Package B uses library X version 2 it was not well defined which version of the library got loaded.
With v2.7.0, each package can load its own version of the library without clashing.

## BEAST packages need to provide services explicitly

BEAST packages have to explicitly declare which classes they want to be available for external access, e.g., for XML parsing.
The `PackageHealthChecker` application (see more below) will help list services.
The following services are recognised:

* `beast.base.core.BEASTInterface`: for classes you want to be available for XML files.
* `beast.base.evolution.datatype.DataType`: for non-standard data types.
* `beast.base.inference.ModelLogger`: for model logging (can probably be ignored).
* `beastfx.app.inputeditor.InputEditor`: for BEAUti to know which input editors to use.
* `beastfx.app.inputeditor.AlignmentImporter`: for importing data from files.
* `beastfx.app.beauti.BeautiHelpAction`: for adding entries to the BEAUti help menu.
* `beastfx.app.beauti.PriorProvider`: for adding additional priors to the prior panel in BEAUti.
* `beastfx.app.beauti.ThemeProvider`: for providing GUI themes (see more below).

## BEAST packages claim a namespace

When making a class available as service, the package name is automatically claimed as namespace for the package.
This means no two packages can have a java package with the same name.
In particular, any java package starting with `beast` should be renamed.

## Migrating packages from v2.6 to v2.7

The basic steps for migrating a BEAST package are

* run the [`migrate.pl`](https://github.com/CompEvol/beast2/blob/master/scripts/migrate.pl) script (in `/path/to/beast2/scripts/migrate.pl`) on the directories `src`, `examples` and `templates`. This should resolve the majority of class name changes. Also, rename `templates` to `fxtemplates` if you have any BEAUti templates.
* fix remaining import and other compilation issues. Then build the package and install by hand.
* run the `PackageHealthChecker` application that comes with `BEAST.app` on the package, and update the `version.xml` files, example XML files and other issues indicated by the package health checker.

There is a more [detailed description](https://github.com/CompEvol/beast2/blob/master/scripts/migrate.md) on migrating packages.

## "Help me choose" feature for BEAUti

Every input editor can show a link to the [help me choose](https://beast2-dev.github.io/hmc/) website.
By specifying the `hmc` attribute in a `subtemplate` you can let BEAUti know that a page exists.
The `hmc` attribute contains comma delimited list of `help me choose` pages available from the https://beast2-dev.github.io/hmc/ site. 
Pages can be redirected by adding an alias, e.g. `RateACPrior/index/=Priors/RateGTRPrior/` redirects the `hmc` button for the prior with id `RateACPrior` to the [`/Priors/RateGTRPrior`](https://beast2-dev.github.io/hmc/hmc/Priors/RateGTRPrior/) page, which covers priors on all rates of the GTR model.

## Automatic methods section generation

Automatic methods section generation is now included in BEAUti by default.
By including a `methods.csv` file inside the `fxtemplates` folder, method section phrases can be specified.
See [here](https://github.com/CompEvol/BeastFX/blob/master/fxtemplates/methods.csv) and [here](https://github.com/BEAST2-Dev/bModelTest/blob/master/fxtemplates/methods.csv) for examples.

## Themes

GUI Themes can be added by providing a service of type `beastfx.app.beauti.ThemeProvider`.
Theme providers should implement the `loadMyStyleSheet` method of `ThemeProvider`, which allows loading a CSS style sheet, or provide more complex user interface facilities.


