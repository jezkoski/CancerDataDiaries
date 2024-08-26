+++
title = 'Oncoplot'
date = 2024-08-11T13:43:50+09:00
draft = true
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

## Tool requirements

There are several tools and packages available for creating oncoplots.
The most commonly used one is Bioconductor's maftools oncoplot, a popular R package. 

In python, there are some options such as:


## Creating oncoplot with maftools oncoplot



# References

- maftools documentation link
- vcf2maf link
