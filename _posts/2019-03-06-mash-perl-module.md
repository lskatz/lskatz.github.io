---
title: 'Mash perl module'
date: 2019-03-06T14:12:00.001-05:00
draft: false
url: /2019/03/mash-perl-module.html
---

Hey y'all, I made a new perl module to read and write [Mash](http://mash.readthedocs.io/) files.  I made it a complete package, adding it to CPAN with documentation and adding unit testing with continuous integration on Travis-CI.  Most of the back end relies on the binary, but I re-implemented the mash distance formula in Perl.  
  
Why?  I am the developer for Mashtree and it is becoming increasingly difficult to parse Mash files, and it became evident that I needed some object-oriented programming.  Because it might be helpful to the community, I made it an actual perl module.  
  
One difficult part of the process was not being able to read the cap'n proto formatted mash files.  This is due to no relevant perl parser that was available for it.  Therefore, I looked for a different way to serialize mash sketches.  Ultimately because mash info displays JSON, I went with JSON and gzipped JSON.  
  
Here is a quick synopsis:  
`use` `strict;``  
use` `warnings;``  
use` `Mash;``  
  
# Sketch all fastq files into one mash file.``  
system``(``"mash sketch *.fastq.gz > all.msh"``);``  
die` `if` `$?;``  
# Read the mash file.``  
my` `$msh` `= Mash->new(``"all.msh"``);`  
`# All-vs-all distances`  
`my` `$distHash` `=` `$msh``->dist(``$msh``);`  
`# Or, one liner``perl -MMash -e '$msh1 = Mash->new("1.msh"); $msh2 =` `Mash->new("2.msh"); $dist = $msh1->dist($msh2);`  
  
I ran a preliminary all-vs-all test of 303 genomes.  To make the file, I used Mash v2.  To run all-vs-all distances, I ran  mash dist vs $msh->dist().  In this test, the binary was 15x faster (1.9s vs 28s).  One more benchmark shows that the resulting json.gz files are approximately the same size as the original msh files.  
Let me know what you think!  As of the time of this post, the version is 0.5.  
[https://metacpan.org/pod/Mash](https://metacpan.org/pod/Mash)  
[https://github.com/lskatz/perl-mash](https://github.com/lskatz/perl-mash)  
If you would like to contribute, my wish list includes  

*   Speed optimization
*   Mash paste
*   Native reading of mash files
*   Native writing of mash files