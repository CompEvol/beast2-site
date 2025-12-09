---
layout: post
title: Protein structural phylogenetics in BEAST 2 with 3Di characters
tags: []
---
<p style="color:gray">1 December 2025 by <a href='mailto:jordan.douglas@auckland.ac.nz'>Jordan Douglas</a></p>

The three dimensional structure of a protein retains evolutionary information much longer than its sequence. This property enables structure to rescue phylogenetic signal when sequences are highly divergent, as recently reviewed (Puente-Lelievre 2025). 

Advances in artificial intelligence have enabled protein structures to be represented as sequences of 3Di characters (Van Kempen et al. 2025). The 3Di alphabet consists of twenty structural characters, which are conveniently named after the twenty canonical amino acids (A, C, D, ..., W, Y) but otherwise share no resemblance. Some of these characters are associated with helices, some are strands, and others are turns or loops.  Using Foldseek, a protein structure with L sites can be discretised into a sequence of 3Di characters, also of length L. Then, phylogenetic trees can be inferred from 3Di sequences in the same fashion as as amino acids, enabling further insight into highly divergent protein alignments (Puente-Lelievre et al. 2023, Moi et al. 2025).

3Di phylogenetics can be performed using the FoldBeast package in BEAST 2. Please refer to the [FoldBeast](https://github.com/jordandouglas/FoldBeast/tree/main)  repository page for installation and user instructions. 

The FoldBeast package currently comes with four 3Di substitution matrices:  the original Foldseek 3Di matrix (van Kempfen et al. 2023), the GH AlphaFold and LLM matrices (Garg and Hochberg 2025), and a null model where all exchangeability rates are equal. The FoldBeast 3Di model averaging framework will automatically estimate which of these matrices should be used,  whether there should be gamma site rate heterogeneity or invariant sites, and whether empirical or estimated 3Di frequencies should be used. Be careful not to upload an amino acid alignment instead of 3Di, or vice versa. They may look very similar. 

![FoldBeast site model](https://raw.githubusercontent.com/jordandouglas/FoldBeast/refs/heads/main/fig/siteModel.png)


This model averaging is done in the same style as the [OBAMA](https://www.beast2.org/2020/11/25/OBAMA.html) package for amino acid models, except here we have a different list of empirical matrices to choose from. One notable difference between amino acids and 3Di characters comes from the diminished 3Di alphabet size found in many proteins. For example, a helical protein structure without any beta strands might contain just ten of the twenty possible 3Di characters. This will have major implications on the estimated frequencies.

It is recommended to compare 3Di phylogenies with trees inferred from amino acids alone, and trees inferred from amino acids and 3Di characters together in two partitions. In most cases, the amino acid trees will be taller (in substitutions per site), and therefore it is important to assign relative mutation rates to the partitions.

One major limitation of 3Di phylogenetics comes from the standard assumption of sites evolving independently; an assumption broken by the very definition of 3Di characters, which are based on their shared geometries the nearest position in the protein structure. The severity of this model violation remains an open question.




## References


Puente-Lelievre, C., Malik, A., & Douglas, J. (2025). Protein Structural Phylogenetics. Genome Biology and Evolution, 17(8), evaf139.

Puente-Lelievre, C., Malik, A. J., Douglas, J., Ascher, D., Baker, M., Allison, J., ... & Matzke, N. (2023). Tertiary-interaction characters enable fast, model-based structural phylogenetics beyond the twilight zone. _bioRxiv_, 2023-12.

Moi, D., Bernard, C., Steinegger, M., Nevers, Y., Langleib, M., & Dessimoz, C. (2025). Structural phylogenetics unravels the evolutionary diversification of communication systems in gram-positive bacteria and their viruses. _Nature Structural & Molecular Biology_, 1-11.

Van Kempen, M., Kim, S. S., Tumescheit, C., Mirdita, M., Lee, J., Gilchrist, C. L., ... & Steinegger, M. (2024). Fast and accurate protein structure search with Foldseek. Nature biotechnology, 42(2), 243-246.

Garg, S. G., & Hochberg, G. K. (2025). A general substitution matrix for structural phylogenetics. Molecular Biology and Evolution, 42(6), msaf124.