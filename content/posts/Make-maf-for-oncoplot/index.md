+++
title = 'Make Maf for Oncoplot'
date = 2024-08-10T14:23:30+09:00
draft = true
categories = ['bioinformatics']
tags = ['oncoplot','maftools']
+++

# MAF files

Mutation Annotation Format (MAF) is a data format used to store mutation data. 
Some visualization tools such as Bioconductor's oncoplot, require the data
to be in MAF format. 

MAF files have mandatory and optional fields. 
Mandatory fields:
- Hugo_Symbol: Gene name (it's Hugo nomenclature name)
- Chromosome
- Start_Position
- End_Position
- Reference_allele
- Tumor_Seq_Allele2: alternative allele
- Variant_Classification: variant consequence eg. missense, stop, splice
- Variant_Type: single nucleotide variant, insertion, deletion
- Tumor_Sample_Barcode:	Tumor/Patient/Sample name or number

Optional fields:
- non MAF specific fields containing VAF and amino acid change information

# Generating MAF files

When generating MAF files your starting point is usually a VCF file or a tabular file with annotations.
Tool [vcf2maf](https://github.com/mskcc/vcf2maf) will annotate, prioritize transcripts, and generates
a MAF suitable for further use. The tool requires VEP to be installed in order for it to be able to do the annotations for mutations. 

Because I already had a VEP-annotated tabular file, I made a short code in python to generate
MAF file and to change the values in Variant_Type and Variant_Classification columns
to be maftools oncoplot compatible.

Taking the required fields from the VEP-annotated tabular file and renaming the column names into oncoplot suitable ones.

```{python}
import pandas as pd

def make_maf(data):
	data['End_Position'] = data['pos'] + 1
	maf = data[['SYMBOL','chrom','pos','End_Position','ref','alt','Consequence','VARIANT_CLASS', 'patient']]
	maf.columns = ['Hugo_Symbol', 'Chromosome','Start_Position','End_Position', 'Reference_Allele', 'Tumor_Seq_Allele2','Variant_Classification','Variant_Type','Tumor_Sample_Barcode']
	return maf

maf = make_maf(data)
```

In R, I change the Variant_Type and Variant_Classification values.

```{R}
library(dplyr)

data <- read.table('variants.maf', sep='\t', header=TRUE)

change_values <- function(df) {
	df <- df %>%
		mutate(Variant_Classification = case_when((Variant_Classification == 'missense_variant') ~ 'Missense_Mutation',
			(Variant_Classification == 'stop_gained') ~ 'Nonsense_Mutation',
			(Variant_Classification == 'splice_acceptor_variant') ~ 'Splice_Site',
			(Variant_Classification == 'splice_donor_variant') ~ 'Splice_Site',
			(Variant_Classification == 'missense_variant&splice_region_variant') ~ 'Missense_Mutation',
			(Variant_Classification == 'frameshift_variant' & Variant_Type =='deletion') ~ 'Frame_Shift_Del',

}
```



