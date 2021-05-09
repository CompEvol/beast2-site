---
layout: post
title: What is new in v2.6.4
tags: []
---

This release has a few minor bug fixes as well as API improvements for package.

## AppLauncher picks up BEAGLE

Some utilities that can be launched through AppLauncher run faster with the BEAGLE library, but unlike the `beast` script, the `applauncher` script was not set up to pick up the BEAGLE library automatically. This is fixed now.

## Less clutter on screen output

Trait values are printed to screen by default, which may result in a large amount of text when there are a large number of taxa. This prevents potentially important warnings or other messages to be overlooked, as well as slow down launching of BEAST when using the console. The new behaviour is that only the first 10 values are printed. By setting loglevel to `debug` (start `beast` with `-loglevel debug`) all trait values will be shown.

Another potential producer of copious amount of messages is the filtered alignment when the filter consists of a list of individual sites. The filter is shown, but only the first 200 characters. Again, more will be shown when log level is set at debug.

## Bayesian skyline plot has a proper prior alternative for the first element of the popSizes parameter

Currently, the options are to use a Jeffrey's (1/X) prior on the first population size, which is an improper prior, or a uniform prior on 0 to infinity, which is also not proper. An extra input is available to specify an alternive prior.

## Fix DensiTree launcher for Windows

Double clicking the DensiTree.exe file in Windows failed to launch DensiTree, which should be fixed now.

## LogCombiner can now write to stdout

When the `-o` option is not specified, it was running into a bug causing no output to be produced at all. Now, it outputs logs to stdout.

## FilteredAlignment can deal with missing and ambiguous values

The `FilteredAlignment` class attempted to convert datatype when provided with a user data type. Unfortunately, ambiguous codes were not converted correctly, which is now fixed.

## Node now recognises age trait

It already recognised `date`, `date-forward` and `date-backward` to which `age` is added as indication of a trait for time (as synonym for `date-backward`).

## Allow packages to be released in github release area again

Due to a change in github, the files in the release area were treated somewhat different causing problems for the package manager to download files. The package manager can now deal with this, but developers may be wise to put package files somewhere else in order for older versions of BEAST to be able to pick them up.

## Add differentRandomIndex to UpDownOperator

A `differentRandomIndex` flag is added to UpDownOperator to indicate if a different random index should be chosen for each parameter (default false); only applicable if `elementWise` is set to true

## Checksum support for developers chasing incorrectly calculated likelihoods

The dreaded incorrectly calculated likelihood bug encountered by developers may now be easier to chase by implementation of a checksum of the internal states of `CalculationNode`s. You have to implement `getChecksum()` method for your CalculationNode to have effect, and it is only invoked when `-Dbeast.debug=true` is set, so does not interfere with the normal running of BEAST MCMC.

## New packages

Check out newly added packages like [PIQMEE](http://www.beast2.org/2020/10/06/PIQMEE.html) for efficiently dealing with alignments that have many duplicates, the [optimised relaxed clock (ORC)](http://www.beast2.org/2020/12/15/ORC.html) and [OBAMA](http://www.beast2.org/2020/11/25/OBAMA.html) for model averaging withi amino acid data.