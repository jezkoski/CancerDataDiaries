+++
title = '(Problems) installing devtools package in R'
date = 2024-12-22T10:53:37+02:00
draft = false
categories = ['installations']
tags = ['R','devtools']
+++

When you want to install R-packages from github, you need a package called devtools. 
Many times installing this directly in R with “install.packages(’devtools’)” 
throws an error “couldn’t find package devtools”. Some other error message 
following this installation effort might be: error installing packages gert and usethis. 

Here’s one way I have been able to solve this issue on my system.

# Install libgit2 to your system or conda environment

I often use R through Python and hence I install everything in my conda environment.
Install libgit2 with conda:

```bash{}
conda install --yes conda-forge::libgit2
```

You can also install this package to your system if you don’t use conda 
(eg. linux, refer to this [stack](https://stackoverflow.com/questions/65853618/installation-of-package-devtools-had-non-zero-exit-status-r-packge-or-r-stud) )

# Make environment variable to use system libgit2 you just installed and install devtools and two other packages git2r and gert.

```R{}
#in R

Sys.setenv(USE_SYSTEM_LIBGIT" = 1)

install.packages(c("git2r", "gert", "devtools"))
```

# Install R package from github with devtools

After the previous steps, you should be able to install R packages from github with:

```R{}
#in R

install_github("PACKAGE_NAME")
```




