--- 
layout: post 
title: The improvement of BEAUti for "sampled ancestor trees" package (SA) 
tags: []
---

<p style="color: gray;">March 2023 by Walter Xie</p>

In earlier versions of BEAUti, the user had to manually change all TipDatesRandomWalker operators 
into SampledNodeDateRandomWalker when using the "sampled ancestor trees" package (SA). 
This was because the former did not consider trees containing sampled-ancestor nodes. 
However, thanks to Remco for fixing this issue in BEAUti 
(see the [GitHub issue](https://github.com/CompEvol/BeastFX/issues/45)).

In BEAST 2.7.3 or higher, SA can be installed or updated to version 2.1.1 (or higher). 
To set up a "Sampled Ancestors MRCA prior" for a fossil, 
repeat the previous process in the "Priors" panel, but select the prior from the drop list. 

![](/images/SAMRCApriorDropList.png)

Apply a distribution (e.g., Uniform) to it and remember to select the "Tipsonly" checkbox.

![](/images/SAMRCAprior.png)

The availability of the SampledNodeDateRandomWalker operator can be checked in the "Operators" panel. 
If the panel is not visible, go to the "View" menu and select "Show Operators panel".

![](/images/SampledNodeDateRandomWalker.png)

Once all settings have been applied, save the work to the XML file. 
The created SampledNodeDateRandomWalker operator can be found in the file, 
as shown in the example below:

```xml
<operator id="tipDatesSampler.Agriarctos_spp" spec="sa.evolution.operators.SampledNodeDateRandomWalker" taxonset="@Agriarctos_spp" tree="@Tree.t:bearsTree" weight="1.0" windowSize="1.0"/>
```

Finally, thank you, ChatGPT, for your help in improving this post! :)
