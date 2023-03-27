---
title: 'Tree To Reads'
date: 2016-10-24T11:25:00.001-04:00
draft: false
url: /2016/10/tree-to-reads.html
---

It looks like Tree To Reads is online in a preliminary draft!  I encourage everyone to try it out!  
  
http://biorxiv.org/content/early/2016/01/22/037655  
  
Using genome-wide SNP-based methods for tracking pathogens has become standard practice in academia and public health agencies. There are multiple computational approaches available that perform a similar task: call variants by mapping short read data against a reference genome, quality filter these variants, then concatenate the variants into a sequence matrix for downstream phylogenetic analysis. However, there are no existing methods to validate the accuracy of these approaches despite the fact that we know there are parameters that can affect whether a SNP is called, or the correct tree is recovered. We present a simulation approach (TreeToReads) to generate raw read data from mutated genomes simulated under a known phylogeny. The user can vary parameters of interest at each step in the simulation (e.g., topology, model of sequence evolution, and read coverage) to assess the robustness of a given result, which is critical within both research and applied settings. Source code, examples, and a tutorial are available at \\url{https://github.com/snacktavish/TreeToReads}.