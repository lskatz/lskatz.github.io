---
title: 'Krona snippet'
date: 2015-10-14T11:32:00.000-04:00
draft: false
url: /2015/10/krona-snippet.html
---

I couldn't find any kind of conversion Kraken and Krona recently and so I wrote up a little pipeline.  Full script is available at lskScripts: [https://github.com/lskatz/lskScripts/blob/master/qsub/launch\_kraken.sh](https://github.com/lskatz/lskScripts/blob/master/qsub/launch_kraken.sh)  
  
The main points of the script are shown below.  
```
run $KRAKENDIR/kraken --fastq-input --paired --db=$KRAKEN\_DEFAULT\_DB --preload --gzip-compressed --quick --threads $NSLOTS --output $KRAKENOUT $READS  
  
run kraken-translate --db $KRAKEN\_DEFAULT\_DB $KRAKENOUT | cut -f 2- | sort | uniq -c |\\  
  perl -lane '  
              s/^ +//;   # remove leading spaces  
              s/ +/\\t/;  # change first set of spaces from uniq -c to a tab  
              s/;/\\t/g;  # change the semicolon-delimited taxonomy to tab-delimited  
              print;  
             ' |\\  
  sort -k1,1nr > $KRAKENTAXONOMY  
  
  
run $KRONADIR/ktImportText -o $HTML $KRAKENTAXONOMY
```