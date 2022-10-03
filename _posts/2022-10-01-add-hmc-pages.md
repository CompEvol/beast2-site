---
layout: post
title: Adding `Help me choose` info to BEAUti
tags: []
---
<p style="color:gray">1 October 2022 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

The [Help me choose](https://beast2-dev.github.io/hmc/) site for BEAST 2 contains helpful information on how to choose settings in BEAUti, as outlined [here](https://www.beast2.org/2022/07/01/help-me-choose.html).
This information is linked to input editors and tabs in BEAUti. 
This post explains how to add `Help me choose` information for new BEAST packages.

Essentially, you need to 

* create a link in `hmc` attributes in your BEAUti template
* write new page or find suitable page to add to `hmc` web


<img alt='BEAUti v2.7.0 default' src='/images/BEAUti.v2.7.0.default.png' width="75%"/>

## Create link for input

The links are encoded in BEAUti templates, where the `BeautiConfig` and `BeautiSubTemplate` items have a `hmc` attribute. 
Use `BeautiConfig/hmc` for main templates, and `BeautiSubTemplate/hmc` for sub templates.
The `hmc` attribute contains a comma delimited list of `help me choose` entries.
Each entry is of the form `BEAST object ID/input name`, for example

```
hmc='SiteModel/proportionInvariant/,
SiteModel/mutationRate/,
SiteModel/gammaCategoryCount/,
SiteModel/shape/,
SiteModel/substModel/'
```

Each of these five entries refers to a page on the HMC site, for example the first entry (`SiteModel/proportionInvariant/`) links to [https://beast2-dev.github.io/hmc/hmc/SiteModel/proportionInvariant/](https://beast2-dev.github.io/hmc/hmc/SiteModel/proportionInvariant/).
To keep things less unwieldy, in the remainer we write `{hmc-site}` to mean `https://beast2-dev.github.io/hmc`.

If you want to refer to the object, not one of its input, use the format `BEAST object id/index`. For instance,

```
hmc='YuleModel/index'
```

refers to [{hmc-site}/hmc/YuleModel/index/](https://beast2-dev.github.io/hmc/hmc/YuleModel/index/), and when the Yule model is chosen in the drop down box for tree priors (as shows in the image above), the link brings you to the Yule model page.

## Create link for tab

Tabs in BEAUti can be linked to the HMC site by adding entries of the form `package name/tab name`.
However, make sure the `tab name` have space replaced by underscores. For example for the `Standard` template, we have

```
hmc='Standard/Priors/,
Standard/Clock_Model/
'
```

linking tabs to the [{hmc-site}/hmc/Standard/Priors/](https://beast2-dev.github.io/hmc/hmc/Standard/Priors/) and [{hmc-site}/hmc/Standard/Clock_Model/](https://beast2-dev.github.io/hmc/hmc/Standard/Clock_Model/) pages.



## Redirect links 

If a suitable page is already written, or the location in the hmc-hierarchy does not fit the `BEAST object id/input name` format, you need to redirect the hmc entry to the correct place.
Pages can be redirected by adding an alias by adding the target after a `=`, for example 

```
hmc='GammaShapePrior/index/=Priors/GammaShapePrior/'
```

link so [{hmc-site}/hmc/Priors/GammaShapePrior/](https://beast2-dev.github.io/hmc/hmc/Priors/GammaShapePrior/), not `{hmc-site}/hmc/GammaShapePrior/index/`.


## Add page to the HMC site

This information is linked to input editors and tabs in BEAUti. 
To add a link for your BEAST package, first check whether there is not already a page written. 
Pages are maintained on the [hmc github repository](https://github.com/BEAST2-Dev/hmc).

If no suitable page is available, clone the [hmc github repository](https://github.com/BEAST2-Dev/hmc) and write your own page.
Many page names are of the form `BEAST object ID/input name`, but some are grouped to keep the structure of the site more sensible.
Once you are happy with the page, commit/create a pull request on github.



