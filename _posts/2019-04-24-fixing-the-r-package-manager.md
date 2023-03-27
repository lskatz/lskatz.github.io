---
title: 'Fixing the R package manager'
date: 2019-04-24T08:48:00.001-04:00
draft: false
url: /2019/04/fixing-r-package-manager.html
---

R's package manager is broken.  I present to you my solution for it.  
  
$HOME is your home directory.  
  
1) mkdir -pv $HOME/R/tmp  
  
2) Edit $HOME/.Renviron and add these contents to ensure that your temporary directory is local, universally found in your environment, and not bound by any tempdir restrictions (e.g., noexec).  The tradeoff is that your home directory might not be the fastest drive (but that's not why you're using R anyway is it?).  
  
TMP=$HOME/R/tmp  
TMPDIR=$HOME/R/tmp  
TEMP=$HOME/R/tmp  
  
3) Edit $HOME/.Rprofile and add these contents so that each set of libraries is specific to the version of R.  
  
major <- R.Version()$major  
minor <- R.Version()$minor  
  
majorDir <- paste("/scicomp/home/gzu2/R/",major,sep="")  
minorDir <- paste("/scicomp/home/gzu2/R/",major,"/",minor,sep="")  
if(!file.exists(majorDir)){  
  dir.create(majorDir)  
}  
if(!file.exists(minorDir)){  
  dir.create(minorDir)  
}  
  
.libPaths(c(minorDir, .libPaths()))