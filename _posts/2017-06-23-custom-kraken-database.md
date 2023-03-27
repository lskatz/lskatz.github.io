---
title: 'Custom Kraken database'
date: 2017-06-23T09:43:00.000-04:00
draft: false
url: /2017/06/custom-kraken-database.html
---

Like many labs around the world, we use Kraken for contamination detection.  This isn't its intended purpose however because it is supposed to be a taxonomic classifier for metagenomics samples.  
  
Despite that, it has been incredibly useful.  We just want to make it _more_ useful.  Many weird species, especially those without genome assemblies on NCBI, show up really weird in Kraken.  So when look for contamination in a weird species, we get random hits from random species or even a majority of unclassified reads.  On the other hand, if we run Kraken on a standard _Escherichia coli_ genome, we get near-perfect results.  
  
Working with our subject matter experts (SMEs), I was able to piece together a preliminary set of completed genome assemblies where the risk of their being contaminated was virtually none.  Then, I tried making a custom Kraken database.  The instructions are straightforward... until they're not.  There were some unsaid things such as the need to erase the description on sequence headers.  Below is my workflow for creaking a totally new Kraken database and not simply adding onto a pre-existing one.  
  
\# First, make a directory of fasta files   
\# whose extensions are .fasta. The specific  
\# extension doesn't matter, but it helps with  
\# the find command later.  
  
\# Display the assemblies directory with tree.  
\# This is a for-example, not necessarily what I have.  
\# Each subfolder should be formatted Genus\_species.  
$ tree genomeAssemblies/\*   
Escherichia\_coli  
├──Escherichia\_coli\_44.fasta  
└──Escherichia\_coli\_45.fasta  
Salmonella\_bongori  
├── NCTC-12419.fasta  
└── N268-08.fastaSalmonella\_enterica  
└── Salmonella\_enterica\_90.fasta  
  
\# Download the taxonomy and initialize the Kraken  
\# database.  
$ kraken-build --download-taxonomy --db custom-kraken  
  
\# Alter the Shigella species so that they are subspecies  
\# to E. coli  
cd custom-kraken/taxonomy  
mv nodes.dmp nodes.bak  
\# Anything with Genus Shigella (taxid: 620),   
\# change to parent E. coli (taxid: 562)  
perl -Mautodie -e 'open($fh, "nodes.bak"); while(<$fh>){@F=split /\\t\\|\\t/; if($F\[1\]==620){ $F\[1\]=562; $\_=join("\\t|\\t", @F); } print; } close $fh;' > nodes.dmp  
cd -  
  
\# Next format the fasta files using a custom script  
\# which can be found in my misc github repo  
\# [https://github.com/lskatz/lskScripts/blob/master/formatFastaForKraken.pl ](https://github.com/lskatz/lskScripts/blob/master/formatFastaForKraken.pl)  
\# This script adds a kraken tag and removes the   
\# fasta description. Modifications are in-place.  
$ ls genomeAssemblies | xargs -P 12 -n 1 bash -c '  
  genusSpecies=$(sed "s/\_/ /g" <<< $0);   
  taxid=$(grep "|"$'\\t'"$genusSpecies"$'\\t'"|" custom-kraken/taxonomy/names.dmp | cut -f 1 | head -n 1)  
  formatFastaForKraken.pl --taxid=$taxid $0/\*.fasta  
'  
  
\# This automation with find/xargs only works down to   
\# the species level. For any deeper diving, you can   
\# use the custom script to replace the taxid with   
\# something more specific.  
$ formatFastaForKraken.pl --taxid=41520 genomeAssemblies/Salmonella\_enterica/Salmonella\_enterica\_90.fasta  
  
\# Add genome assemblies to the custom database  
find . -name '\*.fasta' | \\  
  xargs -P 12 -n 1 kraken-build --db custom-kraken --add-to-library  
  
\# Build the database  
kraken-build --build --threads 12 custom-kraken