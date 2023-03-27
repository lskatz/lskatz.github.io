---
title: 'Perl writing style'
date: 2016-02-03T12:27:00.001-05:00
draft: false
url: /2016/02/perl-writing-style.html
---

I'm taking a moment to reflect on a post that Torsten Seemann wrote a couple of years ago called "[Minimum standards for bioinformatics command line tools](http://thegenomefactory.blogspot.com/2013/08/minimum-standards-for-bioinformatics.html)."  In it, he explains many basic conventions that I believe in.  I wanted to expand on this list by showing how I start off my own scripts in Perl.  I learned a lot of these tricks by experience and by viewing the scripts I inherited in CG-Pipeline from Andrey Kislyuk. Additionally, some of the tips in the [Doom 3 code review](http://kotaku.com/5975610/the-exceptional-beauty-of-doom-3s-source-code) resonated with me.  
  
Briefly, these are the ten tips that Torsten outlines plus a hidden one, but then I have new Perl-specific rules.  My rules start with #12.  Or maybe #11.  
  

1.  **Print something if no parameters are supplied**
2.  **Always have a "-h" or "--help" switch**
3.  **Have a "-v" or "--version" switch**
4.  **Use stderr for messages and errors**
5.  **Validate your parameters**
6.  **Don't hard-code any paths**
7.  **Don't pollute the command-line name space**
8.  **Don't distribute bare JAR files**
9.  **Check that your dependencies are installed**
10.  **Be strict if you are still a Perl tragic like me**
11.  **I'll shut up now** I know that this wasn't labeled as a tip but this is in fact a hidden tip in my opinion!  I'll explain later.

And now for my additions:  
  
**12\. Make a logmsg subroutine so that you can direct all messages to the right place (which is STDERR!,  corollary of rule #4)**  This is my logmsg subroutine which I modified from Andrey's code.  I change it often according to my needs.  
  
sub logmsg {  
  # Get this script's filename and the   
  # subroutine that called logmsg()  
  local $0=basename $0;  
  # $parentSub is the subroutine that called this one.  
  my $parentSub=(caller(1))\[3\] || (caller(0))\[3\];  
  # Remove the annoying 'main::' prefix  
  $parentSub=~s/^main:://;  
  
  my $msg="$0: $parentSub$tid: @\_\\n";  
  
  print STDERR $msg;  
}  
  
**13\. Use as few global variables as possible. Use a main subroutine to avoid globals.**  
Perl makes all variables global by default which really hurts the idea of closure with subroutines.  A variable in your subroutine could be evaluated from the global space or the local space and it's best to avoid this situation.  Imagine for example if you have a variable $x in global space  and $X in your local space.  If you accidentally called $x in your subroutine then strict would not complain and you would get the wrong variable.  This is a very tough situation to find and debug.  
  
Therefore, use a subroutine main(). Furthermore, you can use it to return the correct exit code of your program.  
  
\# my $VARIABLE="SOMETHING"; # Not in global space! Bad!   
\# exit code is going to be main's return value.  
exit(main());  
\# Define variables inside of main() to keep them local  
sub main{  
  my $variable="something";  
  
  return 0;  
}  
  
  
**14\. There are some modules and pragmas you should just include every time.  Corollary to rule #10.**  
  
When I start writing a script, I always start off with these basic shebang, pragmas, and modules. It actually doesn't matter if you are a Perl tragic or a Perl genius... these are safeguards that are necessary in virtually any Perl program and should be used.  I disagree with using the pragma autodie in favor of using my own error messages, but it can be used and this is just a decision between more control vs more ease.  
  
#!/bin/env perl  
use strict;  
use warnings;  
\# use autodie;    # I personally don't use this, but   
                  # I believe Perl tragics should.  
use Getopt::Long; # for parsing command line options (rule #5)  
use Data::Dumper; # for debugging  
use File::Temp;   # Temporary directory space  
  
\# Path parsing  
use File::Basename qw/basename fileparse dirname/;   
  
**14\. Use Getopt::Long (the Perl answer to rule #5)**  
This is exactly what you need to parse flags on the command line.  Stay consistent between your scripts on what parameters you accept.  I like to have these standard parameters: numcpus, help, tempdir.  Have default options for each one.  
  
use Getopt::Long  
sub main{  
  my $settings={};  
 GetOptions($settings,qw(output=s help numcpus=i tempdir=s));   
  
  
  # Set default values for all parameters.  
  # ||= means "equals to this if not false"  
  # Better to use the operator //= however  
  # which means "equals to this if not defined".  
  # However, the //= operator does not work in   
  # perl 5.8 and so you should be careful.  
  $$settings{output} ||="out.txt";  
  
  $$settings{numcpus}//=1;  
  # A default temporary directory is a little more nuanced  
  
  $$settings{tempdir}//=tempdir(  
        myscriptnameXXXXXX,  # choose a template name  
        CLEANUP=>1,          # This dir will be deleted at end  
        TMPDIR=>1,           # Use system /tmp  
  );  
  
  return 0;  
}   
  
**15\. Have a usage statement function (same as rules #1-3). Keep it simple.**  
I prefer to have the usage subroutine return a string, but it is up to the developer.  To answer rule #3, have a version() subroutine or a $version variable.  This subroutine should run whenever --help is supplied or if the user did not specify the options correctly.  
  
For example:  
sub usage{  
  local $0=basename $0;  
  # in perl, the subroutine returns whatever was evaluated last.  
  # Therefore this string does not require a return statement.  
  "$0: runs a complete process.  
  Usage:  $0 input.fasta -o output.fasta  
  --numcpus 1  
  --tempdir /tmp"  
}  
  
**16\. Keep your main() uncluttered.  Keep it simple.**  
Have as few steps as possible; keep a top-down approach such that each step is its own subroutine.  
  
\# inside of main(), after default options are declared  
my $fasta=**processOneThing**($settings);  
my $bam=**processAnotherThing**($fasta,$settings);  
my ($one,$two,$three)=**getBamResult**($bam,$settings);  
print join("\\t",$one,$two,$three))."\\n";  
return 0; # 0 is the exit code if all goes well  
  
die usage() if($$settings{help});  
die "ERROR: numcpus has to be >0".usage() if($$settings{numcpus} < 1);  
  
**17\. Keep with standard file formats as much as possible.**  
This isn't about Perl coding as much as it is about bioinformatics.  I've seen too often that people invent their own format just because they feel like it.  Well... DON'T!  
  
**18\. When your program ends, it should end.  Corollary to rule #11**  
There shouldn't be any lingering process.  Don't have forked processes that just keep going after it looks like your program ended.  Just shut up already.  On the other hand, don't do too little.  There shouldn't be an additional step.  If you output sam format, go the extra mile and output a sorted bam too.  And index it!  
  
Another way you should shut up: just run the program.  If you have prompts, keep them to a minimum.  Keep your log messages to a minimum unless you have a --verbose flag.  
  
**19\. Include Perl modules in the directory structure (corollary to rule #9)**  
So your program does something really cool but it requires a weird perl module.  Well... install it!  In my newest Makefile installers I have something like the following lines, and you should too.  If you don't use Make, then use some other installer, but the Perl libraries should be the developer's responsibility and not the user.  
  
lib/lib/perl5/Config/Simple.pm:  
        perl scripts/cpanm --self-contained -L lib Config::Simple  
  
Additionally in your script, you should include the path to the libraries.  This is how I do it with FindBin.  Adjust the lib path according to how you install the Perl modules.  
  
use FindBin;  
use lib "$FindBin::RealBin/../lib";  
  
Well, this turned out to be a small novel.  Hopefully instead it can become a reference for the future for the readers of this post.