+++
title = 'Make Maf for Oncoplot'
date = 2024-08-10T14:23:30+09:00
draft = false
categories = ['bioinformatics']
tags = ['oncoplot','maftools', 'variant annotation']
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

When generating MAF files your starting point is usually a VCF file or a tabular file with or without annotations.
Tool [vcf2maf](https://github.com/mskcc/vcf2maf) will annotate, prioritize transcripts, and generates
a MAF suitable for further use. The tool requires VEP to be installed in order for it to be able to do the annotations for mutations. 

Because I already had a VEP-annotated tabular file, I used python to generate
MAF file and to change the values in Variant_Type and Variant_Classification columns
to be maftools oncoplot compatible. More about bioconductor's maftools can be found [here](https://bioconductor.org/packages/release/bioc/vignettes/maftools/inst/doc/maftools.html#2_Generating_MAF_files).

## VEP annotated tabular file to MAF file

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

## Modify variant annotations

In addition to VEP, there's another popular variant annotation tool ANNOVAR. They use a bit 
different nomenclature on the variant consequence and variant type fields. 
Maftools oncoplot uses the ANNOVAR type of annotations and hence in R, I change the 
Variant_Type and Variant_Classification values from VEP nomenclature to
ANNOVAR nomenclature.

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
			(Variant_Classification == 'start_lost') ~ 'Nonsense_Mutation',
			(Variant_Classification == 'frameshift_variant & Variant_Type == 'insertion') ~ 'Frame_Shift_Ins')) %>%
		mutate(Variant_Type = case_when((Variant_Type == 'SNV') ~ 'SNP',
			(Variant_Type == 'insertion') ~ 'INS',
			(Variant_Type == 'deletion') ~ 'DEL'))

	return(df)
}

data <- change_values(data)
```

## Be careful with the variant nomenclature

Each tool, VEP, ANNOVAR, and other annotation tools use different systems for variant nomenclature
and they may vary. ANNOVAR annotations are more general compared to VEP. For example,
in ANNOVAR, splice-site variants are annotated as 'Splice_Site', whereas in VEP they are 
annotated as 'splice_acceptor_variant' or 'splice_donor_variant' depending on their location. 
In addition, variants in multiple transcripts are dealt with differently. Some tools list
each different annotation the variant gets in the different transcripts whereas for example,
ANNOVAR outputs only the most deleterious variant based on their priorisation system. 


# References

VEP: https://asia.ensembl.org/info/docs/tools/vep/index.html
ANNOVAR: https://annovar.openbioinformatics.org/en/latest/
