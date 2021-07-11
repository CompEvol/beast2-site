---
layout: post
title: Ascertainment correction/Sampling bias
tags: []
---

<p style="color:gray">18 July 2019 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>

Sampling bias can happen when data for the alignment is collected in such a way that some of the sites have lower probability than other sites. For example SNP data is typically selected from among the sites that have some variation among the taxa that you want to analyses, but there will be a very large set of constant sites, which never end up in your sample. Though this probably does not affect the topology of the tree very much, it can have substantial effect on the timing, so you may want to correct for sampling bias through ascertainment correction.

Say, we leave out all constant sites then where we usually calculate `P(D|T)` for alignment data `D` and tree `T` (leaving out other parameters to not clutter the notation too much), what we actually calculate is `P(D,where D not constant | T)`. Through application of Bayes rule, this can easily be shown to be equal to

```
    P(D | T)
---------------
1-P(D constant)
```

So, we have `P(D|T)` in the numerator, which we calculate as usual, but the term `P(D constant)` in the denominator is what we need to specify.

## Ascertainment correction in XML

The way this is implemented in BEAST is by adding sites for constant characters to the alignment, and identifying these columns in the XML. Say, we have this (very short) alignment obtained by selecting non-constant sites:

{% highlight xml %}
<data id="alignment" dataType="nucleotide">
    <sequence taxon="human">    AGAAATATGTCT</sequence>
    <sequence taxon="chimp">    TAGAAATATGTC</sequence>
    <sequence taxon="bonobo">   CTAGAAATATGT</sequence>
    <sequence taxon="gorilla">  GTCTAGAAATAT</sequence>
    <sequence taxon="orangutan">AGAAATATGTCT</sequence>
    <sequence taxon="siamang">  AGAAATATGTCT</sequence>
</data>
{% endhighlight %}

If you do *not* know how many constant sites you have, follow the steps below. If you *do* know how many sites you have, go to the section below these steps.


## Step 1: add constant sites

There is currently no BEAUti support for changing ascertainment correction, so it requires some XML editing. The basics are quite simple: add constant sites to the alignments, and set up a few attributes to identify these sites.

It is most convenient to add constant sites at the start of the alignment. Since there are four possible nucleotide characters, we add four columns, and obtain this alignment
{% highlight xml %}
<data id="alignment" dataType="nucleotide">
    <sequence taxon="human">    ACGT AGAAATATGTCT</sequence>
    <sequence taxon="chimp">    ACGT TAGAAATATGTC</sequence>
    <sequence taxon="bonobo">   ACGT CTAGAAATATGT</sequence>
    <sequence taxon="gorilla">  ACGT GTCTAGAAATAT</sequence>
    <sequence taxon="orangutan">ACGT AGAAATATGTCT</sequence>
    <sequence taxon="siamang">  ACGT AGAAATATGTCT</sequence>
</data>
{% endhighlight %}


## Step 2: specify sites to exclude from `P(D|T)`

The `Alignment` has three attributes to specify the set of columns to take in account for ascertainment correction:

* excludefrom (Integer): first site to condition on, default 0 (optional, default: 0)
* excludeto (Integer): last site to condition on (but excluding this site), default 0 (optional, default: 0)
* excludeevery (Integer): interval between sites to condition on (default 1) (optional, default: 1)

Since we want to exclude the first four columns, we specify `excludefrom="0"` and `excludeto="4"`. We could specify `excludeevery="1"`, but since that is the default value, we can just leave it out, giving:

{% highlight xml %}
<data id="alignment" dataType="nucleotide" excludefrom="0" excludeto="4">
    <sequence taxon="human">    ACGT AGAAATATGTCT</sequence>
    <sequence taxon="chimp">    ACGT TAGAAATATGTC</sequence>
    <sequence taxon="bonobo">   ACGT CTAGAAATATGT</sequence>
    <sequence taxon="gorilla">  ACGT GTCTAGAAATAT</sequence>
    <sequence taxon="orangutan">ACGT AGAAATATGTCT</sequence>
    <sequence taxon="siamang">  ACGT AGAAATATGTCT</sequence>
</data>
{% endhighlight %}

## Step 3: Setting the `ascertained` flag

For the treelikelihood to use the correction, the `ascertained` flag has to be set to `true` on the alignment, like so:

{% highlight xml %}
<data id="alignment" dataType="nucleotide" excludefrom="0" excludeto="4" ascertained="true">
    <sequence taxon="human">    ACGT AGAAATATGTCT</sequence>
    <sequence taxon="chimp">    ACGT TAGAAATATGTC</sequence>
    <sequence taxon="bonobo">   ACGT CTAGAAATATGT</sequence>
    <sequence taxon="gorilla">  ACGT GTCTAGAAATAT</sequence>
    <sequence taxon="orangutan">ACGT AGAAATATGTCT</sequence>
    <sequence taxon="siamang">  ACGT AGAAATATGTCT</sequence>
</data>
{% endhighlight %}



## If you know the number of constants sites

In case you know how many constant sites were ignored, you can add a filtered alignment to specify how many constants sites with As, Cs, Gs and Ts respectively you have.

* rename the original alignment to say `original-alignment` by editing the `id` attribute
* below the alignment, add a filtered alignment with the id of the old alignment, and set the number of constant sites.

With 500 As, 200 Cs, 300 Gs and 100 Ts you can achieve this as follows:

{% highlight xml %}
<data id="original-alignment" dataType="nucleotide">
    <sequence taxon="human">    AGAAATATGTCT</sequence>
    <sequence taxon="chimp">    TAGAAATATGTC</sequence>
    <sequence taxon="bonobo">   CTAGAAATATGT</sequence>
    <sequence taxon="gorilla">  GTCTAGAAATAT</sequence>
    <sequence taxon="orangutan">AGAAATATGTCT</sequence>
    <sequence taxon="siamang">  AGAAATATGTCT</sequence>
</data>

<data id="alignment" spec="FilteredAlignment" filter="-" data="@original-alignment" constantSiteWeights="500 200 300 100"/> 
{% endhighlight %}

# Ascertainment tips and tricks

## SNAPP analyses

SNAPP has its own ascertainment correction scheme. The above only applies to cases where the `TreeLikelihood` are used. The `ThreadedTreeLiklihood` does not support ascertainment correction (see more <a href="#threading">below</a>).


## Cognate data for languages

For language cognate data, it is common that the cognates have been selected in such a way that at least one of the languages has the cognate present. In this case you would ascertain on at least one `1` in a cognate column, e.g., like so:

{% highlight xml %}
<data id="alignment" dataType="binary" excludefrom="0" excludeto="1" ascertained="true">
    <sequence taxon="Dutch">    0 100001 </sequence>
    <sequence taxon="English">  0 100001 </sequence>
    <sequence taxon="German">   0 100011 </sequence>
    <sequence taxon="Spanish">  0 011110</sequence>
</data>
{% endhighlight %}


## Missing data

Especially for cognate data for language alignments, it is possible there is data missing for a language for a particular cognate class. Then it cannot be determined whether a site is constant or not, since there is no conclusive way to say what value the missing taxon would have. In this case, you should not ascertain on the language having a `0` but on it being unknown, i.e., has a `?` in the ascertainment column, like so:

{% highlight xml %}
<data id="alignment" dataType="binary" excludefrom="0" excludeto="1" ascertained="true">
    <sequence taxon="Dutch">    0 100001 </sequence>
    <sequence taxon="English">  0 100001 </sequence>
    <sequence taxon="German">   0 100011 </sequence>
    <sequence taxon="Spanish">  0 011110</sequence>
    <sequence taxon="Hittite">  ? ??????</sequence>
</data>
{% endhighlight %}


## More missing data

A more complex situation is where data was obtained only if there is variation between two (known) clades, but not when there is only variation inside a clade. This means, for cognate data there is at least one `1` in clade A (say, Germanic including Dutch, English and German), and at least one `1` in clade B (say Italic, including Spanish and French). In other words, clade A is never all `0` and neither is `B`, so we could ascertain like so:

{% highlight xml %}
<data id="alignment" dataType="binary" excludefrom="0" excludeto="2" ascertained="true">
    <sequence taxon="Dutch">    0? 100001 </sequence>
    <sequence taxon="English">  0? 100001 </sequence>
    <sequence taxon="German">   0? 100011 </sequence>
    <sequence taxon="Spanish">  ?0 011110</sequence>
    <sequence taxon="French">   ?0 011110</sequence>
</data>
{% endhighlight %}

but this is wrong, since both columns contain the all `0` site. So, we should subtract the probability of that column once (but more if there are more clades). To do this, there are three extra attributes to specify such columns:

* includefrom (Integer): first site to condition on, default 0 (optional, default: 0)
* includeto (Integer): last site to condition on, default 0 (optional, default: 0)
* includeevery (Integer): interval between sites to condition on (default 1) (optional, default: 1)

Adding an all zero column and `includefrom` and `inlcudeto` attributes gives:

{% highlight xml %}
<data id="alignment" dataType="binary" excludefrom="0" excludeto="2" includefrom="2" includeto="3" ascertained="true">
    <sequence taxon="Dutch">    0? 0 100001 </sequence>
    <sequence taxon="English">  0? 0 100001 </sequence>
    <sequence taxon="German">   0? 0 100011 </sequence>
    <sequence taxon="Spanish">  ?0 0 011110</sequence>
    <sequence taxon="French">   ?0 0 011110</sequence>
</data>
{% endhighlight %}

This calculates
```
          P(D | T)
-------------------------------
1-P(D excluded) + P(D included)
```
were `D excluded` is specified by the `exclude*` attributes and `D included` by the `include*` attributes. A more complex example can be found in (<a href="https://academic.oup.com/jole/article/3/2/145/5067185">Robbeets & Bouckaert, 2018</a>).

## Sub sets of alignments

It is possible to split up alignments into sub-alignments, especially when there are blocks of missing data as is common with language data. This can be done with the `FilteredAlignment`. Any `FilteredAlignment` takes an `Alignment` as `data` attribute and a filter specification that  specifies which of the sites in the input alignment should be selected. First site is 1. Filter specs are comma separated, either a singleton, a range [from]-[to] or iteration [from]:[to]:[step]; 1-100 defines a range, 1-100 or 1:100:3 defines every third in range 1-100, 1::3,2::3 removes every third site. Default for range [1]-[last site], default for iterator [1]:[last site]:[1].

For an alignment with `id="pny2016"` we can select the cognates for the word `and` as follows:

{% highlight xml %}
<data id="and" spec="FilteredAlignment" data="@pny2016" filter="236-296"/>
{% endhighlight %}


## Note:
```
The first site in filters is 1, but the ascertainment correction uses 0 for first site. We are aware this is confusing, but also hard to change while keeping backward compatibility of XML files, so it is what it is.
```

It would be possible, but confusing to specify the ascertainment correction attributes directly on this filtered alignment. Less error prone is to just assume the first column is ascertained on and wrap the filtered alignment in another like so:

{% highlight xml %}
<data id="orgdata.and" spec="FilteredAlignment" ascertained="true" excludeto="1" filter="-">
    <data id="and" spec="FilteredAlignment" data="@pny2016" filter="236-296"/>
</data>
{% endhighlight %}

You can find a more elaborate example from (Bouckaert et al, 2018) <a href="https://static-content.springer.com/esm/art%3A10.1038%2Fs41559-018-0489-3/MediaObjects/41559_2018_489_MOESM3_ESM.xml">here</a>.

<h2 id="threading"> Threading </h2>

As mentioned above, the `ThreadedTreeLiklihood` does not support ascertainment correction (and warns so with the message `Note, can only use single thread per alignment because the alignment is ascertained` before crashing with a `NullPointerException` if you try it with multiple threads). One way around is to split up the alignment into multiple sections using the `FilteredAlignment` as outlined in the previous partitions, and having one `TreeLiklihood` per partitions, each with its own ascertainment correction on the same columns as the single threaded version would use.

Make sure to set `useThreads="true"` on the `CompoundDistribution` containing the `TreeLiklihood`s -- usually, this is the element with `id="likelihood"`.



## Reference

<a href="https://www.nature.com/articles/s41559-018-0489-3">Bouckaert RR, Bowern C, Atkinson QD. The origin and expansion of Pama–Nyungan languages across Australia. Nature ecology & evolution. 2018 Apr;2(4):741.</a>


<a href="https://academic.oup.com/jole/article/3/2/145/5067185">Robbeets M, Bouckaert R. Bayesian phylolinguistics reveals the internal structure of the Transeurasian family. Journal of Language Evolution. 2018 Aug 6;3(2):145-62.</a>


