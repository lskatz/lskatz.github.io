---
title: 'Minimizer perl module'
date: 2019-10-10T15:11:00.000-04:00
draft: false
url: /2019/10/minimizer-perl-module.html
---

I looked all over cpan but did not find a module to make Minimizers.  Therefore I went ahead and made it in Bio::Minimizer.

  

Minimizers are _l-_mers of _k-_mers, where _k_ is the number of nucleotides in a substring of DNA.  A minimizer is a substring of a _k-_mer with length _l_.  The minimizer is lexicographically the smallest _l_\-mer in the _k-_mer.  They were described in a 2004 paper: https://academic.oup.com/bioinformatics/article/20/18/3363/202143

  

They seem simple but they have some interesting properties, copied and pasted from the 2004 paper in bold.  In the paper, _w_ is equivalent to the notation _l_.  Not all properties are discussed here in this post.  My comments are inline.

1.  **If two strings have a signiﬁcant exact match, then at least one of the minimizers chosen from one will also be chosen from the other.**  This means that in two strings of DNA, if there is an exact match represented by a _k-_mer, then there will be an exact match in an _l_\-mer.
2.  **If two strings have a substring of length _w_+_k_\-1 in common, then they have a (_w_,_k_)-minimizer in common.**  This property basically takes the previous property and applies mathematical variables to it.
3.  **A new minimizer occurs about once every half-window width**.  If you are thinking about breaking down a genome to a sliding window of _k_\-mers, then consider this.  If you use minimizers, that genome is relatively well indexed with minimizers instead.
4.  A minimizer represents a set of multiple _k-_mers.  Ok, so if you have something in genome A that might have a match in B, one fast thing you can do is find all minimizers in genome A and genome B.  Find where exact matches are between the genomes, and even some inexact matches, you could find places that have identical minimizers, and extend the alignment from there.

Anyway, please have fun with the module and if it gets more useful over time, I will develop it more.

  

https://metacpan.org/pod/Bio::Minimizer

https://github.com/lskatz/bio--minimizer