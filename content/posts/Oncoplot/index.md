+++
title = 'Oncoplot'
date = 2024-08-26T13:43:50+09:00
draft = false
categories = ['tutorial']
series = ['Bio Visualisation Tools']
tags = ['oncoplot','maftools']
+++

# Introduction

Oncoplots are are visualisation tool used in genomics to present genetic mutations in a set of study samples.
They provide an overview of the mutation/variant types and frequencies across all the samples.
This allows for easier identification of recurrent and co-occuring mutations.

Each column in the oncoplot represent a sample or individual, and each row corresponds to a gene.
Mutation types are represented with different colors and symbols may be added to indicate extra information on the mutation.

{{< figure src="oncoplot.jpg" title="Basic oncoplot" >}}

Oncoplots are often presented with additional information such as clinical 
parameters that can be added under, top, or to the sides of the oncoplot.

There are several tools for creating oncoplots and currently the most 
popular one is Bioconductor's maftools oncoplot. 

# Why use oncoplots?

Oncoplots are popular in cancer research because they make visualising complex data easier. 
Cancer genomes often carry a large number of mutations, which is why simplifying and visualising
the most frequent ones is an important task. 

In addition, oncoplot makes it easier to compare the mutation landscape between specific cancer types or
therapeutic responses. This allows the identification of patterns associated with specific outcomes which can
in turn gives new information about the cancer mechanisms or biomarkers for diagnosis.

# How to create an oncoplot?

Creating an oncoplot requires few steps from data preparation to visualisation.

## Data requirements

For this tutorial, you need genetic mutation data in MAF format. You can
refer to this previous [post]({{< ref "/posts/Make-maf-for-oncoplot/index" >}}) on how to convert your annotated data into MAF
format with a simple piece of code, or follow vcf2maf tutorial if you have
VCF file.

If you want to add clinical information as metadata to the oncoplot,
you need the clinical data in tabular format:

| Tumor_Sample_Barcode | Annotation1 | Annotation2 |
|         :---:        |    :---:    |    :---:     |
| Same sample codes as maf | eg. diagnosis1 | eg. sex |

Save the clinical data file with ending ".clin"

## Tool requirements

There are several tools and packages available for creating oncoplots.
The most commonly used one is Bioconductor's maftools oncoplot, a popular R package. 
For installation, run the following in R:

```r {class="my-class" id="my-codeblock" lineNos=inline height="600"}
install.packages("BiocManager")
BiocManager::install("maftools")
```

In python, there are some options such as package [fuc](https://github.com/sbslee/fuc).


## Creating oncoplot with maftools oncoplot

### Read data
Read in both MAF file with mutations and the optional clinical annotation file.

```r {class="my-class" id="my-codeblock" lineNos=inline height="600"}
data <- read.table("mutations.maf", sep="\t", header=TRUE)
clindata <- read.table("clinicaldata.clin", sep="\t", header=TRUE)

```

Make any necessary filterings and changes to the file if needed. For example remember to modify VEP 
annotation mutation type nomenclature to an ANNOVAR types. Check the previous post on how to make a MAF file.

### Create maftools dataobject

Read files into maftools dataobject

```r {class="my-class" id="my-codeblock" lineNos=inline height="600"}
laml <- read.maf(maf = data,
		clinicalData = clindata,
		verbose = FALSE)

getClinicalData(x = laml)
```

### Set annotation colors

Maftools gives default colors for mutation types and other annotations, but if you want to change these,
you need to declare the color separately.
Also, if you have a large number of variables in some annotations, for example 15 unique diagnosis types,
it will be wise to make your own color palette. 

If you have a reasonable number of variables in an annotation, you can use RColorBrewer and the color palettes.
Many of the RColorBrewer color palettes allow maximum of 12 colors.
To display all the available color palettes in RColorBrewer run the following lines:
```r{class="my-class" id="my-codeblock" lineNos=inline height="600"}
library(RColorBrewer)
display.brewer.all()
```
{{< figure src="Rcolorbrewer_palettes.png" title="RColorBrewer palettes" >}}

First retrieve a number of colors from a palette you want and then assign them to the annotations.
Here's an example how to annotate each mutation type their own colors from the 'Paired' palette.

```r {class="my-class" id="my-codeblock" lineNos=inline height="600"}
vc_cols <- RColorBrewer::brewer.pal(n=6, name='Paired')
names(vc_cols) <- c(
		'Frame_Shift_Del',
		'Missense_Mutation',
		'Nonsense_Mutation',
		'Multi_Hit',
		'Frame_shift_Ins',
		'Splice_Site'
)
```

In case of more than 12 colors, it is probably the best to construct a palette of your own by declaring
all the colors of the palette separately. This is also a good way to construct a palette if you want
some specific or different colors for some annotations.

Here I constructed a list of distinct colors and assigned them to cancer diagnoses and sex.

```r {class="my-class" id="my-codeblock" lineNos=inline height="600"}
dx_cols <-c(
	"dodgerblue2", "#E31A1C", #red
	"green4", "#6A3D9A", # purple
	"#FF7F00", # orange
  	"aquamarine3", "gold1",
  	"skyblue2", "deeppink1", # lt pink
  	"brown",
  	"#CAB2D6", # lt purple
  	"#FDBF6F", # lt orange
	"gray70", "khaki2",
  	"maroon", "orchid1", "blue4", "steelblue4",
  	"darkturquoise",  "yellow4", "yellow3",
  	"darkorange4" 
)

names(dx_cols) <- c(
	"AML", "ALL", "Breast",
	"MM", "Colon", "Lung","Skin",
	"BMF","Ovary","Lymphoma", 
	"Pancreas"
)

sex_cols <- c("skyblue2", "orchid3")
names(sex_cols) <- c("M","F")
```

For annotations you need to save the colors into a list before making the oncoplot. 
Make sure that the list parameters (Dx_group and Sex in this case) are the same ones
you have as column names in your clinical data annotation file!

```r{class="my-class" id="my-codeblock" lineNos=inline height="600"}
anno_cols <- list(Dx_group=dx_cols, Sex=sex_cols
```

### Make oncoplot with clinical annotations

You have a lot of options how to modify an oncoplot and I suggest you to look into maftools oncoplot documentation
to find out what additional things you can do with oncoplots.
Here, we will draw a basic oncoplot with two different clinical annotations at the bottom of the plot.

```r{class="my-class" id="my-codeblock" lineNos=inline height="600"}
oncoplot(maf = laml, #read in the maftools dataobject
	colors = vc_cols, #colors for mutation types
	clinicalFeatures = c("Dx_group", "Sex"), #the column names of features in the annotation file
	annotationColor = anno_cols, #list of colors for annotations
	removeNonMutated = TRUE, #doesn't show samples if there are no mutations
	showTitle = FALSE,
	top = 10, #show only top 10 mutated genes
	showPct = FALSE, #shows percentage of individuals carrying mutation in a gene
	drawColBar = FALSE #draws a barplot on top to show how many mutations each individual have
)
```
And this gives a basic oncoplot with two clinical annotations. Sometimes you may need to adjust the
legend parameters in order to make all the legends with (mutation type, Dx_group and Sex should
all have their own legens underneath the oncoplot).
Parameters to adjust for this are for example: legend_height, anno_height, gene_mar, annotationFontSize.

{{< figure src="oncoplot.jpg" title="Basic oncoplot" >}}






# References

- maftools oncoplot [documentation](https://bioconductor.org/packages/release/bioc/vignettes/maftools/inst/doc/oncoplots.html)
- vcf2maf [documentation](https://github.com/mskcc/vcf2maf)
