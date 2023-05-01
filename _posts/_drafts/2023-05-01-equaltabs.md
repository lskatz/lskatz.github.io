---
title:  "We can make this beautiful and searchable"
date:   2023-05-01 08:59:11 -0400
categories: jekyll update
header:
   teaser: /assets/images/silicon-valley-tabs.jpg
---

You might have seen this message on GitHub for a tab separated file.
It's warning you that you don't have an equal number of tabs, and then it offers you more information without telling yout to fix it!  Ugh.

> ![screenshot of github saying "we can make this file beautiful and searchable](/assets/images/beautiful-and-searchable.png)

# Process

## Step by step

Here is how I solved it with perl.

```
TSV=sars-cov-2-coronahit-routine-a.tsv
cat $TSV | perl -F'\t' -lane 'print(scalar(@F))' | sort | uniq 
0
11
12
2
3
4
```

Ok so the most columns I have is 12 and I'll use this next one liner to add in columns until I get to 12 on each row.

```
cat $TSV | \
  perl -F'\t' -lane '
    # while there are fewer than 12 elements, add an empty string element
    while(@F < 12){
      push(@F,"");
    }
    # print the elements separated by \t
    print join("\t", @F);
  ' > tmp.tsv && \
  mv -v tmp.tsv $TSV
```

* `-F'\t'` indicates that it will separate the fields by tab
* `-lane` 
   * `-l` indicates that newlines will be printed after each print statement
   * `-a` read the input line by line
   * `-n` separate the fields into `@F`
   * `-e` execute perl code
* `> tmp.tsv && mv -v tmp.tsv $TSV` only replace the file if the code executed successfully, using `mv`.

## batch

I wanted to do this with the whole folder and so here is my bash loop with perl

```
for i in *.tsv; do tabs=$(cat $i | perl -F'\t' -lane 'print(scalar(@F))' | sort | uniq | sort -nr | head -n 1); cat $i | perl -F'\t' -lane 'while(@F < '$tabs'){push(@F,"");} print join("\t", @F);' > tmp.tsv && mv -v tmp.tsv $i; done;

```

