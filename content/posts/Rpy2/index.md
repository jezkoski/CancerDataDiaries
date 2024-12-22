+++
title = 'Using R in python with rpy2'
date = 2024-12-22T10:17:07+02:00
draft = false
categories = ['coding', 'tutorial']
tags = ['Python','R', 'rpy2']
+++


For many Python users, it can be a painful moment when you realize you need to run some analyses
in R because many statistical packages are still offered only through R.
I often prefer not switching away from my jupyter notebooks or VScode and thus, I 
prefer using rpy2 and cell magic with %%R to do the needed analyses in R.  
(This is when I do light enough analyses I run only in the notebook)

# Installing rpy2

To get started, you need a conda environment into which you'll install the needed packages. 
For rpy2, you currently need an R version 4 or newer so it's good to install R and essentials to the environment.


```bash {class="my-class" id="my-codeblock" lineNos=inline height="600"}
# If you're creating your own environment:
conda create --yes -n rpy2_env r r-essentials

# You can also just (re-)install R in an environment you already have created
conda activate rpy2_env
conda install --yes -c conda-forge r r-essentials

```
I have found the installation of rpy2 to be buggy through pip, which is why
I install rpy2 also with conda.

```bash {class="my-class" id="my-codeblock" lineNos=inline height="600"}
conda install --yes rpy2
```

# Using R in jupyter notebook

After finishing the installation, you can start using R in your jupyter notebook 
with cell magic.

First activate rpy2 and R cell magic by running the following in a notebook cell:

```Python {class="my-class" id="my-codeblock" lineNos=inline height="600"}
%load_ext rpy2.ipython
```

Running this will enable the %%R cell magic and you can start using R in a cell.
You do this by adding '%%R' in the first line of a cell which turns the whole cell into R code cell.

```Python {class="my-class" id="my-codeblock" lineNos=inline height="600"}
%%R
library(Seurat)
```

# Share variables between R and Python 


The main point of using rpy2 in python is to be able to send variables back and forth 
Python and R without writing and reading them and running separate scripts.
To do this, you need to declare input and output variables to share the variables
between R and Python cells.

You can send variables from Python to R cell with parameter -i:

```Python {class="my-class" id="my-codeblock" lineNos=inline height="600"}
# In python cell
i = 6

# ----- #
# In R cell

%%R -i i
print(i)

```

Similarly, you can send variables from R cell to Python with parameter -o:


```Python {class="my-class" id="my-codeblock" lineNos=inline height="600"}
# In R cell
%%R -o rvar

rvar <- c(1,2,3,4)

# ----- #
# In Python cell
print(rvar)
```

You can also share dataframes and possibly other objects if you make sure they are 
prosessable in both. 
