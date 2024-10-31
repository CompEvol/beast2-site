---
layout: post
title: Incorporating root state prior information
tags: []
---
<p style="color:gray">1 November 2024 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

Be default, the stationary frequencies that come with a substitution model are used to represent the probability a root state is observed.
Felsenstein's peeling algorithm uses these frequencies by multiplying the partials at the root with the stationary frequencies, and sum over the states to obtain the site probability.
However, it is possible there is prior information available for the root states that differs from the stationary frequencies.
Here, we consider a few scenarios where this can happen.

## Root frequencies differ from stationary frequencies

If root frequencies are known to differ, but are the same for every site, the `rootFrequencies` of the `(Threaded)TreeLikelihood` can be set.
To edit the XML, add a `Frequencies` object in the `rootFrequencies` element of the `(Threaded)TreeLiklihood`, for example like so

```xml
 <distribution id="treeLikelihood.dna" spec="ThreadedTreeLikelihood" data="@dna" tree="@Tree.t:dna">
 
 	<rootFrequencies id="rootFreqs" spec="Frequencies" frequencies="0.1 0.2 0.3 0.4"/>
	
        <siteModel id="SiteModel.s:dna" spec="SiteModel">
        ...                
```

In the example above, the root frequencies are fixed at `0.1 0.2 0.3 0.4`, so edit as appropriate.

If the frequencies need to be estimated, a bit more is required: a `RealParameter` needs to be added to the state, a prior and operator added and potentially a logger as well. 
Then the parameter needs to be referred to from the `frequencies` attribute of the `rootFrequencies` element.



## Root frequencies differ between rate categories

If every rate category has its own frequencies and frequencies are shared among all sites, the 5rf model (Waddell & Bouckaert, 2024) can be used. 
The [5rf](https://github.com/rbouckaert/5rf/) package needs to be installed (see [here](https://github.com/rbouckaert/5rf/) for installation instructions).

The XML needs to be edited to make it work: see the [annotated example](https://github.com/rbouckaert/5rf/blob/main/examples/test5rf.xml) for details.



## Root sequence is known exactly

If the root state is know exactly and differs at each site, this implies a root frequencies distribution where for each site one of the states has probability 1, while all others have probability 0.
The [rootfreqs](https://github.com/rbouckaert/rootfreqs) package can be used for this.

To edit an XML

1. Replace `spec="TreeLikelihood"` with `spec="rootfreqs.TreeLikelihood"`, or if you have the threaded version, replace `spec="ThreadedTreeLikelihood"` with `spec="rootfreqs.ThreadedTreeLikelihood"`
2. Add a sequence representing the root state inside the tree likelihood as a `rootfreqseq` element, so it looks something like this:

```xml
<distribution id="treeLikelihood.dna" spec="rootfreqs.TreeLikelihood" data="@dna" tree="@Tree.t:dna">
                
	<!-- specify root frequencies that differ from substitution model frequencies -->
    <rootfreqseq id="seq_Root" spec="Sequence" taxon="Root"
	    totalcount="4" value="TTTTTTTTTTTTTTTTTTTTT..."/>

...
```

Here the sequence is all `T`, but you need to set it to the appropriate value for your analysis.
Make sure to use the same sequence length as the alignment.



## Root sequence is know with uncertainty

If the root state is know with some uncertainty and differs at each site, this implies a root frequencies distribution where for each site there is a different state probability.
The [rootfreqs](https://github.com/rbouckaert/rootfreqs) package can be used for this situation as well.
However, instead of using a fixed sequence, an uncertain sequence needs to be used.
Uncertain sequences represent a distribution for each site by a semi-colon separated list of distributions, where each distribution is a comma separated list of probabilities.

To make a sequence uncertain, set the `uncertain` attribute to `true`.
The example above modified for uncertain root sequence can look something like this:

```xml
<distribution id="treeLikelihood.dna" spec="rootfreqs.TreeLikelihood" data="@dna" tree="@Tree.t:dna">
                
	<!-- specify root frequencies that differ from substitution model frequencies -->
    <rootfreqseq id="seq_Root" spec="Sequence" taxon="Root" uncertain="true"
	    totalcount="4" value="0.0025,0.00071,0.032,2e-04; 0.0021,0.0021,0.027,0.00059; 0.028,0.00062,0.00017,4.8e-05; ..."/>

...
```

Here, the root frequencies for the first site are `0.0025,0.00071,0.032,2e-04;`, for the second site `0.0021,0.0021,0.027,0.00059;` and for the third site `0.028,0.00062,0.00017,4.8e-05;`.
Note that each of these frequencies add up to 1.0, so make a proper distribution.
Edit as appropriate, and make sure there are as many root frequency distributions as there are sites in the alignment.



## References

Peter J. Waddell, Remco Bouckaert.
An independent base composition of each rate class for improved likelihood-based phylogeny estimation; the 5rf model
bioRxiv [2024.09.03.610719](https://doi.org/10.1101/2024.09.03.610719)

