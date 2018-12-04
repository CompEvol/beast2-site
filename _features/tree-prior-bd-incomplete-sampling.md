---
layout: feature
title: Birth-death incomplete sampling (no ψ)
url_beast2_imp: https://doi.org/10.1016/j.jtbi.2009.07.018
label_beast2_imp: stadler2009
pr_beast2_imp: false
url_beast1_imp: http://arxiv.org/abs/0803.0153
label_beast1_imp: gernhard2008
pr_beast1_imp: true
url_theory: ['http://www.ncbi.nlm.nih.gov/pubmed/19631666', 'http://arxiv.org/abs/0803.0153']
label_theory: [stadler2009, gernhard2008]
url_source: http://github.com/CompEvol/beast2
label_source: core
url_example_xml: 
category: Birth-death tree priors
---

The Birth-death incomplete sampling [stadler2009](https://doi.org/10.1016/j.jtbi.2009.07.018) 
includes random sampling in the birth–death process model. 
There are two scenarios: 
either (i) each individual is sampled in the complete birth–death tree with probability conditioning 
that the tree after sampling has n leaves (birth–death process), 
or (ii) given a tree on m individuals, n of these individuals are sampled uniformly at random (birth–death process).
