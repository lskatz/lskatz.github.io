---
title: 'Downloading the breadth of SARS-CoV-2'
date: 2020-07-14T13:49:00.000-04:00
draft: false
url: /2020/07/downloading-breadth-of-sars-cov-2.html
---

I was trying to figure out how to download the breadth of all of SARS-CoV-2 genomes and so I started out with the two major repositories: NCBI and GISAID.  
  

NCBI
----

I think that for this task, all that is generally needed is the edirect package and the SRA Toolkit.  The first tool will give us a spreadsheet of metadata.  The second tool will give us the genomic data.

### Edirect

The basic strategy here is to query the taxonomy ID of SARS-CoV-2 only, link it to SRA, then get summary documents and parse them.

  

esearch -db taxonomy -query "$taxid\[uid\]" | \\

  elink -target sra | \\

  esummary | \\

  xtract -pattern DocumentSummary -group Runs -element Run@acc -element Run@total\_bases -group ExpXml -element Biosample -element Platform -element Statistics@total\_bases -block Library\_descriptor -element LIBRARY\_NAME -element LIBRARY\_STRATEGY -element LIBRARY\_SOURCE -element LIBRARY\_SELECTION -element LIBRARY\_LAYOUT \\

  > ncbi.tsv 2> ncbi.log &

  

### SRA Toolkit

Keeping with the chain of data, I used the resulting spreadsheet from Edirect to get a listing of all SRA Run IDs and download them.  I used this script, given to me by Taylor Griswold which I modified a little.

  

https://github.com/lskatz/lskScripts/blob/master/qsub/array/launch\_fastq-dump\_split.sh

  

bash ~/src/qsub/array/launch\_fastq-dump\_split.sh out <(cut -f1 ncbi.tsv)

GISAID
------

These genomes were downloaded as fasta files: click on EpiCov, then download, then nextfasta.