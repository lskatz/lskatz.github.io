---
title: 'Sampling the taxonomy database'
date: 2017-12-26T13:38:00.001-05:00
draft: false
url: /2017/12/sampling-taxonomy-database.html
---

I was a little frustrated that every time I wanted to try out my new Bio::DB::Taxonomy\-based script, it would take a few minutes to run.... and then I would find the bug in my script, fix it, and run it again.  I couldn't find a small example database to run my script on, and so I created one.  This is the NCBI taxonomy, spliced for just _[Firmicutes](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?mode=Info&id=1239&lvl=1&lin=f&keep=1&srchmode=1&unlock)_ (taxid: 1239)  Enjoy!  
  
\# Filter the nodes file.  
\# I used a recursive function printChildren to  
\# print the taxonomy lines.  
perl -F'\\t\\|\\t' -MData::Dumper -lane '  
  BEGIN{  
    sub printChildren{  
      my $parent=shift;  
      return if(!$child{$parent});  
      for my $child(values($child{$parent})){  
        print $nodes{$child};  
        printChildren($child);  
      }  
    }  
  }  
  push(@{$child{$F\[1\]}},$F\[0\]);  
  $nodes{$F\[0\]}.=$\_;  
  END{printChildren(1239);  
}' < nodes.dmp > exampleNodes.dmp  
  
\# Backtrack and filter the names file.  
perl -F'\\t\\|\\t' -Mautodie -lane '  
  BEGIN{  
    open($fh, "names.dmp");  
    while(<$fh>){  
      my($nodeID)=split(/\\t\\|\\t/);  
      $names{$nodeID}.=$\_;  
    }  
    close $fh;  
    print STDERR "Indexed!  Searching and printing.";  
  }  
  chomp $names{$F\[0\]};  
  print $names{$F\[0\]};  
' < exampleNodes.dmp > exampleNames.dmp  
  

Show that the taxonomy has been significantly filtered to one or two orders of magnitude.

$ wc -l \*.dmp  
   107700 exampleNames.dmp  
    79466 exampleNodes.dmp  
  2401017 names.dmp  
  1614627 nodes.dmp  
  4202810 total  
  
  
Then, loading the database in BioPerl:  
use Bio::DB::Taxonomy;  
$db = Bio::DB::Taxonomy->new(-source=>"flatfile", -nodesfile=>"exampleNodes.dmp", -namesfile=>"exampleNames.dmp");