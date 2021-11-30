---
layout: post
title: Should you use the proportion invariable sites model?
tags: []
---
<p style="color:gray">1 December 2021 by <a href="mailto:r.bouckaert@auckland.ac.nz">Remco Bouckaert</a></p>


It is essential to capture different rates among sites in an alignment in order to accurately estimate branch lengths and even the topology (Steel et al, 2000). Two popular models are the gamma rate heterogeneity model (Yang, 1994), usually employed with 4 categories. Another it the site model that assumes a proportion is invariable (Churchill et al, 1992). The gamma model and invariable site model can be combined into a model with 5 categories: 4 for the gamma model and one with rate 0 for the invariable site model. However, the invariable site model does not always have a good reputation. Here we look at a few arguments against and in favour of the model.


## Argument against 1: Gamma+I is unidentifiable

The gamma rate heterogeneity model is considered to be unidentifiable in combination with the proportion invariable site model in a maximum likelihood setting (p. 120, Yang (2014)). 
This is because for small value of the shape parameter of the gamma model the slowest category gets a rate that is very close to zero to the point that numerically it becomes impossible to distinguish tree likelihoods.
This was also observed in a Bayesian setting (Bouckaert & Drummond, 2017).

However, in a Bayesian setting, we can put a prior on the shape parameter that discourages low values of the shape parameter, and this unidentifiability vanishes, as shown in Bouckaert, 2020. Small gamma shape values do not make sense anyway, since they lead to extremely low rates for the slowest categories of the gamma model, suggesting effectively no mutation. But, when already modelled by the invariable site model, such categories become unnecessary, so it makes sense to discourage shape values close to zero.

Waddell (1995) points out that the pure (non-discretised) gamma+I model is identifiable, but forms a very flat likelihood landscape and there is a strong negative correlation between the proportion invariable and components of the gamma site heterogeneity model. Once approximations like those of Yang (1994) or Steel and Waddell (1999) are considered this does no longer hold in practice in a maximum likelihood setting.  Using a Bayesian methods and choosing appropriate priors can fix this though.


## Argument against 2: Invariability is unreasonable, use gamma heterogeneity instead

Another argument against the invariable site model is that it is unreasonable to assume that there is no evolution at all: the site is invariable, and as the name says, is not allows to mutate, in direct contradiction to the fact that we attempt to infer trees based on information that comes from assuming there are mutations. The argument goes that using the gamma model instead allows rates to become small enough to represent very slow mutation rate, making it all but impossible to flip a site.

Let's look at some numbers: with a shape of 0.1 and four categories, the categories have the following rates: 1.2E-8, 6.9E-4, 0.11, 3.9, so if the tree is in units of years, the lowest rate suggest that on average once every 83 million years a mutation can be expected. If the tree is in units of centuries, this becomes once every 8.3 billion years, which is longer than the earth exists, and about twice as long as life is know to exist on earth. If the tree is in even older units of time, like millenia, or millions of years, the numbers get more incredulous. Shape value can go below 0.1, and then the above number become even smaller: for shape=0.05, rates are 4.8E-17, 1.7E-7, 0.0045, 3.99 and not only the slowest rate is close to zero (expecting less than one mutation since long before the beginning of the universe), but the second rate becomes rather small as well.

So, practically, it does not make a difference whether are rate is zero or some number very close to it: the tree likelihood is not going to be affected by it. However, if no variable site model is used, but only the gamma site model with four categories is used, then the shape parameter will become small when there are many invariable sites, and the slower 2 or even 3 rates will become very close to zero, as we have seen with the numeric samples above. This leaves very little rate heterogeneity to be captured by the model, and only by increasing the number of categories -- and thus the computational cost -- can any remaining heterogeneity be modelled (see more at benefit 2 below).



## Argument in favour 1: Biologically plausible in some cases

It can be argued that for the time period of the analysis, some of the sites will remain invariable because any mutation in those sites will be highly likely to be deleterious and will be rapidly removed from the population by natural selection. Therefore, a model that explicitly makes the assumption that such sites do not mutate comes close to modelling the underlying biological process.

It has been argued (Tuffley & Steel 1998, Fitch 1971) covarion models -- models that turn allow substitution models to turn "on" and "off" -- are more representative of evolution than models of rate heterogeneity over sites. So, a covarion + I would be a more natural combination of models than gamma + I.

## Argument in favour 2: Invariable sites have negligible computational cost

For any tree, the invariable site model multiplies all branch lengths with a rate of zero, effectively reducing the tree to a zero height tree, that is a tree that does not allow any evolution. Therefore, all of the sites that are variable have zero probability, and all the sites that are invariable have probability equal to the frequency of the site's character at the root of the tree (some care need to be taken when ambiguous characters are allowed). Note that this is true for any tree topology and any combination of branch lengths (Drummond & Bouckaert, 2015).

We can use this observation to pre-calculate the contribution of invariable sites, before even starting the MCMC, and use the cached results every time the tree likelihood needs to be recalculated. So, unlike adding more categories to the gamma site heterogeneity model, which comes at a cost linear in the number of categories, the invariable site model adds a computational cost that is vanishingly small compared to the rest of the tree likelihood calculation.


## Argument in favour 3: Invariable sites allow the gamma model with 4 categories to cover most of the space

If the invariable site model is not used, but there are many invariable sites in the alignment, the shape parameter of the gamma model is encouraged to be so small during the MCMC that when using 4 categories, the slowest categories will become very close to zero. In fact, three of the four categories can become close to zero, and there is effectively no heterogeneity of rates other than the almost zero rates vs the fastest rate at about 4 (to ensure the average rates over categories is 1). A remedy for this is to introduce more categories: if the shape parameter is small (less than 0.1) double the number of categories so that the slowest 3 still capture the invariable sites, but the fastest categories cover some heterogeneity among the variable sites. This comes at a computational cost which is linear in the number of categories (parallelisation may alleviate some of this, but it still is bound to produce double the amount of electricity consumption).

When a category of invariable sites is allowed, the shape parameter (assuming an appropriate prior) will not become as small, and the 4 categories of the gamma model will be able to represent the heterogeneity in rates among variable sites, without having to resort to more categories, thus saving on computation (and electricity bill).


## Argument in favour 4: The data can tell whether to use the invariable model

You do not have to feel forced to choose the invariable site model, but can use Bayesian model averaging that switches between including invariable sites or not. This is implemented for nucleotide alignments in the bModelTest site model (Bouckaert & Drummond, 2017), and for amino acid alignments in the OBAMA site model (Bouckaert, 2020). By using Bayesian model averaging, the data can tell whether the invariable site model and/or the gamma model is appropriate for the data at hand.



## Acknowledgements

Thanks to Peter Waddell for discussing the model and pointing out many arguments for and against the model.


## References

Remco Bouckaert. OBAMA: OBAMA for Bayesian amino-acid model averaging. PeerJ 8, 2020 [e9460](https://doi.org/10.7717/peerj.9460)

Bouckaert, Remco R., and Alexei J. Drummond. bModelTest: Bayesian phylogenetic site model averaging and model comparison. BMC evolutionary biology 17.1 (2017): 42. [paper](https://doi.org/10.1186/s12862-017-0890-6)

Churchill GA, von Haeseler A, Navidi WC. Sample size for a phylogenetic inference. Molecular biology and evolution. 1992 Jul 1;9(4):753-69. [paper](https://doi.org/10.1093/oxfordjournals.molbev.a040757)

Drummond AJ, Bouckaert RR. Bayesian evolutionary analysis with BEAST. Cambridge University Press; 2015.

Fitch WM. Rate of change of concomitantly variable codons. Journal of Molecular Evolution. 1971 Mar;1(1):84-96.

Steel M, Huson D, Lockhart PJ. Invariable sites models and their use in phylogeny reconstruction. Systematic Biology. 2000 Jun 1;49(2):225-32.

Steel, M. and Waddell, P.J., Approximating likelihoods under low but variable rates across sites. Applied mathematics letters, 1999. 12(6), pp.13-19.

Tuffley C, Steel M. Modeling the covarion hypothesis of nucleotide substitution. Mathematical biosciences. 1998 Jan 1;147(1):63-91.

Waddell, P.J., Statistical methods of phylogenetic analysis: including Hadamard conjugations, LogDet transforms and maximum likelihood: a thesis presented in partial fulfilment of the requirements for the degree of Ph. D. in Biology at Massey University (Doctoral dissertation, Massey University). 1995. 

Yang Z. Maximum likelihood phylogenetic estimation from DNA sequences with variable rates over sites: approximate methods. Journal of Molecular evolution. 1994 Sep;39(3):306-14.

Yang Z. Molecular evolution: a statistical approach. Oxford: Oxford University Press. 2014.
