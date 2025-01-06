---
title:  "Adapter trimming"
date:   2025-01-06 09:40:01 -0400
categories: bioinformatics adapters
header:
   teaser: /assets/images/read-inserts.jpg
---

After sequencing, one of the first steps is to remove adapter sequences.
These are most commonly the sequences that are either used to initiate sequencing like a universal primer,
or are barcoding sequences for multiplexing.

Since these are artificial sequences, we try to remove them using software like
[trimmomatic](https://github.com/usadellab/Trimmomatic)
or [cutadapt](https://github.com/marcelm/cutadapt/).
However, when I looked at the software for either of these, I was mildly shocked that the adapter sequences were either hard coded, in the software repo but not standalone, or just not present at all.
I decided to make a place to store just the adapter sequences, so that future versions of these packages or future software can just access the sequences.

I could not find a single repo with all sequences and so I just made it easy on myself in making it:

1) I took all sequences found in the Trimmomatic repo and copied them over with the Illumina copywrite.
2) I parsed the Porechop repo, or rather [a fork of it from Sam Wilkinson](https://github.com/BioWilko/Porechop), and saved it as a fasta file [^1].
3) I added an open source license so that it is actually usable by anybody.
4) I added unit tests to the repo to make sure that other software can use the adapters.

I also took the extra step in adding a simple adapter trimming method in [`fasten_trim`](https://lskatz.github.io/fasten/fasten_trim/).

[^1]: Showing my work: `cat adapters.py | perl -lane 'if(/(start_sequence|end_sequence)=\((\S+),\s+(\S+?)\)+/){$id=$2; $dna=$3; print ">$id\n$dna";} ' | perl -plane "s/'//g" > Porechop.fa`