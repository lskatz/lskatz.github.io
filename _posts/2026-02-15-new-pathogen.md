---
title:  "So you want to work on a new pathogen: a self-onboarding checklist"
date:   2026-02-15 10:00:10 -0400
categories: bioinformatics pathogens
last_modified_at: 2026-02-17 18:06:10 -0400
---

Imagine, you are a bioinformatician but you go into a new lab or go into a new project.
You know your basics: genome assembly, genome annotation, SNPs,  MLST, the usual toolkits.
So you're mostly good in this department.

You might have been trained on _E. coli_. Or maybe you were trained on _Neisseria meningitidis_.
So you're good in these departments too!.

But now you have to learn about a new organism you have never worked on before, _Cryptococcus razy_ [^1].
If you're lucky, a few people are in the lab and they can teach you a bit about it.
However, many times, you might not have a lot of people helping you.
So what are the basics for when you have to learn _C. razy_?
Here are some ideas I have cobbled together for what I think you need to learn on a new organism.

## The whole genome

Know your basic stats.
Look up and memorize the range of genome sizes.
Is it 5 to 6 megabases?
What is the GC content? 37%? 51%?
Usually the GC content is stable to the ones or the decimal digit.
These numbers will help you eyeball anything wrong like contamination.

Look for structural features.
Most bacterial genomes have one chromosome and maybe some plasmids.
_Vibrio cholerae_ has two chromosomes of sizes 3M and 1M.
Maybe _C. razy_ has one chromosome but it is linear.
Maybe it has a plasmid.
Know these basic genomic characteristics: these features help shape everything from genome assembly to phylogeny.

## Genes of interest

Every pathogen is studied for a reason. Identify why people study it.
Is there a toxin? Single-gene toxin? Multi-gene toxin?
Does it always get delivered on a phage or another mobile element? E.g., Is the toxin on a plasmid instead?
Can it be turned on and off? Maybe through slipped strand mispairing?

Even seemingly simple phenotypes that it might be known for: Is it also interesting because it turns blue?
It has haemolytic activity?
There are genetic determinants for these things and so you can get familiar with them.
Knowing these genes of interest or genotypes might give you some immediate interpretation when studying these genomes.

For example, when I was working on _Vibrio_ but suddenly was asked to work on _E. coli_, it was very helpful to understand the toxin genes.
It turns out that _E. coli_ can be describe in different pathotypes, each with their own clinical outcomes.

## Taxonomy

Your pathogen doesn't live in a vacuum. Zoom out to the domain and then go inward.
Are you looking at Bacteria, Archaea, or Eukaryota?
I once spent part of a graduate project assuming I was working on bacteria before realizing the organism was the nematode _Caenorhabditis elegans_ (totally different domains!). 
That mistake is still embarrassing!

Zoom in a bit. What family is it in?
Zoom in once more. It is in the _Cryptococcus_ genus.
Keep going.
Is your species divided into subspecies?
Lineages? Biotypes? Serogroups? Pathotypes?
First, know the terminology. _Listeria monocytogenes_ is divided into lineages,
but _Salmonella enterica_ is divided into subspecies.
Find out what the tiers are in the taxonomy for _C. razy_.

Finally, look laterally.
What species is closest to your species?
This species makes a good outgroup in some phylogenies.
Sometimes this closely related species makes a good comparison.
For example, maybe _C. razy_ is haemolytic but _C. lose_ isn't.
A comparison between _C. razy_ and _C. lose_ might reveal the genomic basis behind haemolysis in this species.
Good outgroups and sister species often reveal what is unique about your pathogen.
This was very helpful to me when we only had a handful of _N. meningitidis_ genomes and wanted to research why some strains are hypervirulent but others weren't.
I was able to compare to a sister species _N. lactamica_ which is not hypervirulent.

## Phenotype

Don't neglect phenotype: Know what you have.
You should be able to describe your species to a non-bioinformatics colleague.

Is it a bacterium? A nematode?
What is its morphology? Gram stain? Motility? Haemolytic activity?

Does it cause disease? What is the mechanism for how it makes people sick?
What about some of the epidemiology?
Who gets sick from this? 
How serious is each case (case fatality ratio, sequelae)?

This will help guide the real use scenarios for your research and will help you communicate it to the broader public.

## Genomic epidemiology

Now that you know your organism a bit, you should know how people in your field normally compare in routine surveillance or in an outbreak.

Do they run MLST? If so, which type? The types you might see are classic MLST, ribosomal MLST (rMLST), core genome MLST (cgMLST), or whole genome MLST (wgMLST).
MLST is great for faster evolutionary rates or for those that have more homologous recombination.
Do they run SNPs? Is there a normal workflow for SNPs? SNPs are great for species with lower homologous recombination or lower mutation rates.
Is there a particular gene to start a phylogeny with such as 16S or _rpoB_? Sometimes, labs will use a specific gene to get an initial genotype or to place the genome in an initial context.

Do the people in your field have a favorite way to get a fast tree?
Sometimes that is the single gene like _rpoB_ followed by a phylogeny program, e.g., RAxML.
Sometimes it is a fast tree pipeline like Mashtree, KSNP, or SKA.

Just knowing these facts will help you know which tool sets to start using and how to view it in some kind of context.

## Conclusion

Learning to rapidly profile a new pathogen is a professional skill. It lets you ask better questions, communicate clearly with collaborators, and catch mistakes early.

The goal isn’t to become a domain expert overnight. It’s to build a mental scaffold — genome architecture, key genes, taxonomy, phenotype — that lets everything else attach in the right place.

[^1]: To be clear, _C. razy_ and _C. lose_ are fictional organisms.