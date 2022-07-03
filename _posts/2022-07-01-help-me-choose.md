---
layout: post
title: BEAST help me choose site
tags: []
---
<p style="color:gray">1 July 2022 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

When setting up an analysis in BEAUti, by the time you finished over a hundred decisions have been made, or, more likely, have been made for you in the form of default settings such as lower bounds for rate parameters, which parameters to estimate, and default settings of priors.
Many of these settings, in particular detailed options of operators (e.g. whether to automatically optimise operator settings) usually have no great impact on the resulting MCMC run.
However, to be able to make an informed decision on what to choose and/or decide whether default settings are reasonable, you might want to have more information.

BEAUti already has some help built in in the form of tooltips that show op when hovering the mouse over most fields.
There are even some help buttons that pop up dialogs with for example formatting information of input files. 
However, this help facility is most of the time very limited.
Comparing differences in priors, or pointing out better/newer alternatives for existing models is not possible through (mostly single line) tool tips.

Therefore, there is now the "help me choose" website, currently located [here](https://beast2-dev.github.io/hmc/).
It will be linked in with BEAUti v2.7.0, and directly links tabs and other places of interest to the site.
The website allows for more elaborate explanation and comparison of settings and models.

Also, be aware it is opinionated in some areas, which is hardly surprising since there are a wide range of opinions about priors in Bayesian analyses.
If you do not agree, there is always the possibility to raise an issue where alternative views can be stated -- these issues will be linked in with the corresponding web page, so it will be easy to find.

It is currently in development, but please do not hesitate to contribute.
All help is very welcome!

