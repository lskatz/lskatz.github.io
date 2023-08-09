---
title:  "Converting a set of VCFs to distances"
date:   2023-08-09 09:47:01 -0400
categories: file-conversion VCF MSA fasta distances distance-matrix
header:
   teaser: /assets/images/mergeVcf-Dall-e.png
---

I think that converting a bunch of VCF files to an alignment is still an artisinal thing.
I made this sort of task in the [Lyve-SET](https://github.com/lskatz/Lyve-SET) pipeline back in 2013 and it is just sort of buried in the mess of scripts.
I thought it could be helpful to outline how it goes.
I am assuming that you are using the stable version of Lyve-SET, v1.1.4f.

Let's say you have a set of VCF files describing SNPs against the same reference genome.
In Lyve-SET, you can run `set_test.pl --numcpus 4 lambda lambda` to create a sample Lyve-SET run including these VCFs.
Now we have a set of VCF files (and all the other files!).

```plaintext
$ tree lambda/vcf
lambda/vcf
├── NC001416.fasta.wgsim.fastq.gz-reference.vcf.gz
├── NC001416.fasta.wgsim.fastq.gz-reference.vcf.gz.tbi
├── sample1.fastq.gz-reference.vcf.gz
├── sample1.fastq.gz-reference.vcf.gz.tbi
├── sample2.fastq.gz-reference.vcf.gz
├── sample2.fastq.gz-reference.vcf.gz.tbi
├── sample3.fastq.gz-reference.vcf.gz
├── sample3.fastq.gz-reference.vcf.gz.tbi
├── sample4.fastq.gz-reference.vcf.gz
└── sample4.fastq.gz-reference.vcf.gz.tbi

0 directories, 10 files
```

So in our example, we have four VCF files from raw reads and a fifth file which is self vs self.

Next, we want to convert this to a multisample VCF file which I sometimes call a pooled VCF.
We can do that with the Lyve-SET wrapper script mergeVcf.sh (in later versions, it was renamed to set_mergeVcf.sh).

```plaintext
mergeVcf.sh: merges VCF files that are compressed with bgzip and indexed with tabix
Usage: mergeVcf.sh -o pooled.vcf.gz *.vcf.gz
  -o output.vcf.gz         The output compressed VCF file
  -t /tmp/mergeVcfXXXXXX   A temporary directory that will be completely removed upon exit
  -s                       Output SNPs only file in addition to the whole thing
```

Let's make a space and then merge all these VCFs.

```plaintext
$ mkdir lambda/MSA
$ mergeVcf.sh -o lambda/MSA/pooled.vcf.gz -s lambda/vcf/*.vcf.gz
mergeVcf.sh: Dividing the genome into regions, making it easier to multithread
makeRegions.pl: main: Reading lambda/vcf/NC001416.fasta.wgsim.fastq.gz-reference.vcf.gz with extension .vcf.gz
makeRegions.pl: main: Reading lambda/vcf/sample1.fastq.gz-reference.vcf.gz with extension .vcf.gz
makeRegions.pl: main: Reading lambda/vcf/sample2.fastq.gz-reference.vcf.gz with extension .vcf.gz
makeRegions.pl: main: Reading lambda/vcf/sample3.fastq.gz-reference.vcf.gz with extension .vcf.gz
makeRegions.pl: main: Reading lambda/vcf/sample4.fastq.gz-reference.vcf.gz with extension .vcf.gz
mergeVcf.sh: temporary directory is /tmp/mergeVcf.TogLcB
mergeVcf.sh: Running bcftools merge
mergeVcf.sh: merging SNPs in gi|9626243|ref|NC_001416.1|:1-48502
mergeVcf.sh: finished with region gi|9626243|ref|NC_001416.1|:1-48502
mergeVcf.sh: Concatenating vcf output
mergeVcf.sh: parsing /tmp/mergeVcf.TogLcB/concat.vcf for SNPs
‘/tmp/mergeVcf.TogLcB/hqPos.vcf.gz’ -> ‘lambda/MSA/pooled.snps.vcf.gz’
removed ‘/tmp/mergeVcf.TogLcB/hqPos.vcf.gz’
‘/tmp/mergeVcf.TogLcB/hqPos.vcf.gz.tbi’ -> ‘lambda/MSA/pooled.snps.vcf.gz.tbi’
removed ‘/tmp/mergeVcf.TogLcB/hqPos.vcf.gz.tbi’
mergeVcf.sh: SNPs-only file can be found in lambda/MSA/pooled.snps.vcf.gz
‘/tmp/mergeVcf.TogLcB/concat.vcf.gz’ -> ‘lambda/MSA/pooled.vcf.gz’
removed ‘/tmp/mergeVcf.TogLcB/concat.vcf.gz’
‘/tmp/mergeVcf.TogLcB/concat.vcf.gz.tbi’ -> ‘lambda/MSA/pooled.vcf.gz.tbi’
removed ‘/tmp/mergeVcf.TogLcB/concat.vcf.gz.tbi’
mergeVcf.sh: Output file can be found in lambda/MSA/pooled.vcf.gz
removed ‘/tmp/mergeVcf.TogLcB/regions.txt’
removed ‘/tmp/mergeVcf.TogLcB/merged.37965.vcf.gz.tbi’
removed ‘/tmp/mergeVcf.TogLcB/merged.37965.vcf.gz’
removed directory: ‘/tmp/mergeVcf.TogLcB’
```

What do we have now?

```plaintext
$ cd lambda/MSA/
[gzu2@monolith3 MSA]$ ls -lh
total 1.6M
-rw-------. 1 gzu2 users  73K Aug  9 10:05 pooled.snps.vcf.gz      # multisample SNPs-only vcf
-rw-------. 1 gzu2 users  160 Aug  9 10:05 pooled.snps.vcf.gz.tbi  # index
-rw-------. 1 gzu2 users 1.6M Aug  9 10:05 pooled.vcf.gz           # multisample vcf
-rw-------. 1 gzu2 users  163 Aug  9 10:05 pooled.vcf.gz.tbi       # index

[gzu2@monolith3 MSA]$ zgrep '#CHROM' pooled.vcf.gz
#CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO    FORMAT  NC001416.fasta.wgsim    sample1 sample2 sample3 sample4

```

So now we have a multisample VCF and we want to convert it to a fasta alignment.
First in Lyve-SET, I have it so that it converts to a TSV matrix of "called" SNPs.
We'll run `pooledToMatrix.sh` for that.
It uses `bcftools` behind the scenes.

```plaintext
[gzu2@monolith3 MSA]$ pooledToMatrix.sh
pooledToMatrix.sh: generates a SNP matrix using BCFtools and a pooled VCF file
USAGE: pooledToMatrix.sh -o bcfmatrix.tsv pooled.vcf.gz
[gzu2@monolith3 MSA]$ pooledToMatrix.sh -o bcfmatrix.tsv pooled.vcf.gz
pooledToMatrix.sh: bcftools query -i '%TYPE="snp"' -f '%CHROM\t%POS\t%REF\t[%TGT\t]\n' --print-header pooled.vcf.gz > bcfmatrix.tsv.unrefined.tmp
pooledToMatrix.sh: Post-processing
‘bcfmatrix.tsv.tmp’ -> ‘bcfmatrix.tsv’
removed ‘bcfmatrix.tsv.unrefined.tmp’
[gzu2@monolith3 MSA]$ head bcfmatrix.tsv
# [1]CHROM      [2]POS  [3]REF  [4]NC001416.fasta.wgsim:GT      [5]sample1:GT   [6]sample2:GT   [7]sample3:GT   [8]sample4:GT
gi|9626243|ref|NC_001416.1|     1       G       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     2       G       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     3       G       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     4       C       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     5       G       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     6       G       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     7       C       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     8       G       .       N       N       N       N
gi|9626243|ref|NC_001416.1|     9       A       N       N       N       N       N
```

Not much information is in the first ten lines but I hope you get it.
From there, we convert it to an actual fasta alignment with `matrixToAlignment.pl`.

```plaintext
[gzu2@monolith3 MSA]$ matrixToAlignment.pl -h
Multiple VCF format to alignment
  matrixToAlignment.pl: reads a tab-delimted file created by 'bcftools query'.
  Usage:
    matrixToAlignment.pl bcfmatrix.tsv > alignment.fasta
    matrixToAlignment.pl < bcfmatrix.tsv > alignment.fasta # read stdin
  --tempdir tmp       temporary directory

[gzu2@monolith3 MSA]$ matrixToAlignment.pl < bcfmatrix.tsv > alignment.fasta
matrixToAlignment.pl: Finding basecalls based on the genotype in the pooled VCF file
matrixToAlignment.pl: Found all positions; converting to fasta
```

I think from here, you can use `snp-sites` on the fasta alignment, but I will continue down the path I laid out with Lyve-SET.
In the Lyve-SET package, you can find distances and convert them to the "tall/skinny" format like so.

```plaintext
[gzu2@monolith3 MSA]$ pairwiseDistances.pl -h
Finds pairwise distances of entries in an alignment file
  Usage: /apps/x86_64/lyve-SET/lyve-SET-1.1.4f/scripts/pairwiseDistances.pl alignment.fasta > pairwise.tsv
  -n numcpus (Default: 1)
  -e Estimate the number of pairwise distances using random sampling. 1/4 of all pairwise bases will be analyzed instead of 100%.
  -s 0.25 (to be used with -e) The frequency at which to analyze positions for pairwise differences
[gzu2@monolith3 MSA]$ pairwiseDistances.pl alignment.fasta --numcpus 4 > pairwise.tsv
Loaded 5 sequences for comparison
Distances for sample4
Distances for sample2
Distances for sample3
Distances for NC001416.fasta.wgsim
Distances for sample1
No more left in the queue
[gzu2@monolith3 MSA]$ head pairwise.tsv | column -t
sample2               sample4  80
sample2               sample3  83
NC001416.fasta.wgsim  sample3  39
NC001416.fasta.wgsim  sample1  47
sample3               sample4  75
sample1               sample3  84
NC001416.fasta.wgsim  sample2  44
NC001416.fasta.wgsim  sample4  37
sample1               sample2  92
sample1               sample4  85
```

And now we have a format of distances with three columns: sample1, sample2, distance (in SNPs).

To convert to the 2d matrix, there is one last script, `pairwiseTo2d.pl`.

```plaintext
[gzu2@monolith3 MSA]$ pairwiseTo2d.pl -h
Turns a 3-column pairwise distance into a 2d matrix/spreadsheet
  Usage: pairwiseTo2d.pl < pairwise.tsv > table.tsv
[gzu2@monolith3 MSA]$ pairwiseTo2d.pl < pairwise.tsv > matrix.tsv
[gzu2@monolith3 MSA]$ column -t matrix.tsv
.                     NC001416.fasta.wgsim  sample1  sample2  sample3  sample4
NC001416.fasta.wgsim  -                     47       44       39       37
sample1               47                    -        92       84       85
sample2               44                    92       -        83       80
sample3               39                    84       83       -        75
sample4               37                    85       80       75       -
```

There you have it. If you run Lyve-SET directly, then all this is done behind the scenes.
However, I built it so that each script can be run separately, and I think it's paying off.
