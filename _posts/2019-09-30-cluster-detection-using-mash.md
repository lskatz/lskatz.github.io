---
title: 'Cluster detection using Mash'
date: 2019-09-30T18:39:00.000-04:00
draft: false
url: /2019/09/cluster-detection-using-mash.html
---

I have been racking my brain on what the command line version of a rapid cluster detection should be.  As in, how can I rapidly figure out if two random genomes on NCBI are related or not?  
  
My workflow depends largely on the min-hash algorithm as implemented by [Mash](http://mash.readthedocs.io/) and the assemblies that NCBI is generating behind the scenes.  Then at the end, you get a large mash file consisting of sketches for all genomes.  
  
1) Write down the bioprojects to monitor.  I recorded all genomes in the PDP contributors page. [https://www.ncbi.nlm.nih.gov/pathogens/contributors/](https://www.ncbi.nlm.nih.gov/pathogens/contributors/)  
  
2) Save a spreadsheet of two columns, SRA and SRS identifiers for all genomes from these bioprojects.  I chose to do that with Edirect using this kind of query, and piping the results to SRR.tsv:  
  
esearch -db bioproject -query $bioproject | \\  
      elink -target sra | \\  
      esummary |\\  
      xtract -pattern DocumentSummary -element Run@acc -element Sample@acc > SRR.tsv  
  
3) Optionally, get the assembly name so that you can get out of NCBI identifiers and back to names that have meaning to you.  I did that with this kind of code:  
  
esearch -db sra -query $SRR | elink -target biosample | esummary | xtract -pattern DocumentSummary -group Attributes -element Attribute\\@attribute\_name Attribute  
  
4a) Download the assemblies using this trick [https://github.com/ncbi/SKESA/issues/12#issuecomment-431503915](https://github.com/ncbi/SKESA/issues/12#issuecomment-431503915).  Here is my perl subroutine that mimics the shell code.  
  
sub downloadSkesaAsm{  
  my($SRR, $outfile) = @\_;  
  
  my $URL="";  
  open(my $fh, "srapath -f names -r $SRR.realign | ") or die "ERROR: could not run srapath -f names -r $SRR.realign: $!";  
  while(<$fh>){  
    chomp;  
    my $eighthField = (split(/\\|/,$\_))\[7\] || "";  
    if($eighthField =~ m@(/traces/|^http)@ ){  
      $URL = $eighthField;  
    }  
  }  
  close $fh;  
  if(!$URL){  
    # print STDERR "ERROR: could not find URL for $SRR\\n";  
    return "";  
  }  
  
  system("dump-ref-fasta $URL > $outfile.tmp");  
  if($?){  
    logmsg "ERROR with dump-ref-fasta $URL ($SRR): $!";  
    return "";  
  }  
  
  mv("$outfile.tmp", $outfile);  
  
  return $outfile;  
}  
4b) If the assembly does not exist, then download the fastq file.  To keep consistency and to get an assembly, run Skesa on the file before moving onto the next step.  
  
5) Mash sketch the assembly and optionally remove the assembly.  
  
6) Mash paste on all the sketches to produce one mega-mash file.  Optionally, remove the old mash sketches.  Personally, I keep these sketches because it helps reduce redundancy on future runs.  However, there are better ways to keep records.  
  
7) Cluster detection!  Run mash dist on the mega file vs itself and record matches under a certain threshold.