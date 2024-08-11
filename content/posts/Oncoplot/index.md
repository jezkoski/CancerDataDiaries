+++
title = 'Oncoplot'
date = 2024-08-11T13:43:50+09:00
draft = True
categories = ['tutorial']
series = ['Bio Visualisation Tools']
tags = ['oncoplot','maftools']
+++

# Introduction

What
Oncoplots are types of plots that help to visualise genetic 
mutations across different genes and patients.
Commonly the genes carrying mutations/variants are the rows and the
individuals carrying mutations are the columns. 

[figure of example oncoplot]

Oncoplots are often presented with additional info such as clinical 
parameters that can be added under, top, or to the sides of the oncoplot.

Why
- Allows to simultaneously view the most mutated genes in the whole 
individual set/population
- Allows to view individuals with co-occuring mutations 

How
There are several tools for creating oncoplots and currently the most 
popular one is Bioconductor's maftools oncoplot. 


# Tutorial

## Data requirements

For this tutorial, you need genetic mutation data in MAF format. You can
follow this previous post on how to convert your annotated data into MAF
format with a simple piece of code, or follow vcf2maf tutorial if you have
VCF file.

If you want to add clinical information as metadata to the oncoplot,
you need the clinical data in tabular format:

## Tool requirements


# References

- maftools link
- vcf2maf link
