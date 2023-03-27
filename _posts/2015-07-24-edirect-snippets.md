---
title: 'Edirect snippets'
date: 2015-07-24T14:38:00.000-04:00
draft: false
url: 
---

I have been really excited by the new-ish edirect utilities.  I thought I'd put out my snippets and encourage anyone to show theirs too.  I haven't found a lot of great snippets for what I exactly need (although it makes sense because there are so many uses for it).   
  
Given a biosample access, find the SRR number  

    esearch -db sra -query $SAMN | efetch | xtract -pattern IDENTIFIERS -element PRIMARY\_ID  | grep SRR  
  
Using sratools to get an SRR.  This one doesn't really belong since it's not edirect, but it's important to note that this is the correct way to deal with SRA, once you know the identifier that you want to download.  

    fastq-dump --split-files -O $NAME $SRR
