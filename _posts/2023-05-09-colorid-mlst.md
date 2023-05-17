---
title:  "MLST with colorid"
date:   2023-05-16 22:20:11 -0400
categories: perl rust colorid MLST

---

I am pleasantly surprised by how well ColorID works with MLST.
For some background, please see my previous post on [cgMLST]({% post_url 2023-04-09-wgMLST %}).

Basically, you can query your MLST database against your reads or assembly. First, your format your bigsi database which is your reads or assembly; then, query the MLST database against the bigsi.
The major downside is that you cannot discover new alleles. Either there is an allele match or there isn't. The upside is that it is super fast. Even faster in batch.

## Example data

### Benchmark dataset: Campylobacter raw milk outbreak

To get a few samples typed, you can download some benchmark datasets from our
[datasets repo](https://github.com/ncezid-biome/datasets).

```plaintext
cd ~/src
git clone https://github.com/ncezid-biome/datasets datasets-biome
cd ~/src/datasets-biome
# Set up a benchmark folder
mkdir Campy
cd Campy
cp ../scripts/Makefile.template ./Makefile
cp ../datasets/Campylobacter_jejuni_0810PADBR-1.tsv ./in.tsv
# Download and verify all the data
make all
# Compress the fastq files like a good citizen
# Gzipping will take a while.
gzip -v *.fastq
# Checksums no longer needed
rm *.sha256
```

And then a quick check in with what is here.

```plaintext
gzu2@L355901:~/src/datasets-biome/Campy$ ls
2014D-0067_1.fastq.gz  D7316_1.fastq.gz  D7323_1.fastq.gz  D7330_1.fastq.gz  PNUSA000194_1.fastq.gz
2014D-0067_2.fastq.gz  D7316_2.fastq.gz  D7323_2.fastq.gz  D7330_2.fastq.gz  PNUSA000194_2.fastq.gz
2014D-0068_1.fastq.gz  D7319_1.fastq.gz  D7324_1.fastq.gz  D7331_1.fastq.gz  PNUSA000195_1.fastq.gz
2014D-0068_2.fastq.gz  D7319_2.fastq.gz  D7324_2.fastq.gz  D7331_2.fastq.gz  PNUSA000195_2.fastq.gz
2014D-0070_1.fastq.gz  D7320_1.fastq.gz  D7327_1.fastq.gz  D7333_1.fastq.gz  PNUSA000196_1.fastq.gz
2014D-0070_2.fastq.gz  D7320_2.fastq.gz  D7327_2.fastq.gz  D7333_2.fastq.gz  PNUSA000196_2.fastq.gz
2014D-0189_1.fastq.gz  D7321_1.fastq.gz  D7328_1.fastq.gz  D7334_1.fastq.gz  in.tsv
2014D-0189_2.fastq.gz  D7321_2.fastq.gz  D7328_2.fastq.gz  D7334_2.fastq.gz  prefetch.done
D5663_1.fastq.gz       D7322_1.fastq.gz  D7329_1.fastq.gz  MANIFEST          sha256sum.log
D5663_2.fastq.gz       D7322_2.fastq.gz  D7329_2.fastq.gz  Makefile
gzu2@L355901:~/src/datasets-biome/Campy$ du -sh
9.7G    .
```

### Campylobacter MLST database

Save the MLST database into a specific folder as shown here.
It is a folder of fasta files.

```bash
mkdir -pv ~/db/wgMLST
cd ~/db/wgMLST
mkdir Campy.chewbbaca
cd Campy.chewbbaca
wget -O Campylobacter_jejuni_INNUENDO_wgMLST_2021-05-30T22_06_50.902917.zip "https://chewbbaca.online/api/NS/api/download/compressed_schemas/4/1/2021-05-30T22 :06:50.902917"
unzip Campylobacter_jejuni_INNUENDO_wgMLST_2021-05-30T22_06_50.902917.zip
cd ..
```

## Run ColorID

### Installation

This is a copy from the cgMLST post

```plaintext
pushd $HOME/bin
git clone https://github.com/hcdenbakker/colorid.git
cd colorid
cargo build --release
export PATH=$PATH:~/bin/colorid/target/release:~/bin/colorid/scripts
popd
```

Additionally, you'll have to get my custom script to run the whole pipeline of building the database and querying it.

```bash
pushd ~/src
git clone https://github.com/lskatz/lskScripts
export PATH=$PATH:~/src/lskScripts/scripts
popd
```

```plaintext
colorid.mlst.pl: runs colorid/mlst on samples and produces a profiles.tsv in stdout
  Usage: colorid.mlst.pl [options] MLST.db/ *.fasta *.fastq.gz > profiles.tsv
  MLST.db     The MLST database of fasta files, one fasta per locus
  *.fasta *.fastq.gz can be any number of sequence files

  -k          kmer length [default: 39]
  --tempdir   Alternative location for temporary directory
  --numcpus   Default: 1
  --paired    Assume paired end reads for each sample.
              This internally pairs R1 and R2 for each sample
              for colorid's samples.tsv.
  --help      This useful help menu
```

### Running ColorID

I basically made a perl script that runs the ColorID MLST workflow
with as much multithreading as I could do.

```bash
time colorid.mlst.pl ~/db/wgMLST/Campy.chewbbaca *.fastq.gz --numcpus 4 --paired > profiles.tsv
```

* Campy.chewbbaca - the database
* *.fastq.gz - all R1/R2 fastq files
* --numcpus 4 - four threads
* --paired - to indicate that two reads at a time belong to a sample
* \> profiles.tsv - the output is in profiles.tsv

This script:

1. Builds the bigsi database from `*.fastq.gz`
2. Queries the MLST database against the bigsi database
3. Parses the exact hits to create a profiles file.
This file is a tsv which has first column as the sample name and subsequent columns as allele calls.

In my case with 4 threads and 22 samples, it took all of 38 minutes. Assembly-free.
