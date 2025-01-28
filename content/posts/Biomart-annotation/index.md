+++
title = 'Add gene coordinates to AnnData object'
date = 2025-01-28T18:24:06+02:00
draft = false
categories = ['tutorial']
tags = ['single cell', 'R', 'Python', 'scRNA-seq']
+++



Some downstream analyses such as copy number analysis with inferCNV tool require
 additional information to the AnnData object. In copy number analysis, the gene 
coordinates ie. chromosome, start, and end are required by inferCNV for the analysis.

It is possible to annotate the AnnData object with a file/dataframe as long as the 
indexes match. Here is how to add the gene coordinates to the AnnData object “.var”. 

## Retrieve gene coordinates from biomart with R

You can easily retrieve the gene coordinates from biomart with R.
 Or you can use the webtool for that: (https://www.ensembl.org/biomart/martview/)

In R, install the required packages:

```r
install.packages("BiocManager")
BiocManager::install("biomaRt")

library("biomaRt")

ensembl <- useEnsembl(biomart="genes", dataset="hsapiens_gene_ensembl")

#retrieve all the filters and attributes included in biomart
# you can look through them and add any other things possibly useful for you

filters <- listFilters(ensembl)
attribures <- listAttributes(ensembl)

# here we're adding only the "ensembl_gene_id"m "chromosome_name", "start_position", and "end_position" from the attributes

bm <- getBM(attributes = c("ensembl_gene_id", "chromosome_name", "start_position", "end_position"), mart=ensembl)

write.table(bm, file="biomart_gene_post.txt")
```

## Annotate gene coordinates to AnnData object with scanpy

With small tweaks you can annotate your AnnData object with the file you created. 
In your jupyter notebook or Python script do the following:

```python
import pandas as pd
import scanpy as sc

def annotate_biomart_info(adata, biomart_file):
	"""Annotate adata object (in place) with gene position information from biomart.

	Args:
		adata: AnnData object
		biomart_file: path to gene position file from biomart. Includes columns ["ensembl_gene_id", "chromosome_name", "start_position","end_position"]
	"""

	biomart_locations = pd.read_csv(biomart_file, sep=" ")

	# add "chr" to chromosome name (biomart file doesn't have these)
	biomart_locations["chromosome_name"] = "chr" + biomart_locations["chromosome_name"]
	
	# when annotating adata object with data from dfs, the index has to match
	biomart_locations.index = biomart_locations["ensembl_gene_id"]

	# in youe own object, switch the index (adata.var_names) for gene ids so that it matches the biomart file index
	adata.var["gene_name"] = adata.var_names
	adata.var_names = adata.var["gene_ids"]

	# now annotate the chromosome, start, and end from the biomart file
	adata.var["chromosome"] = biomart_locations["chromosome_name"]
	adata.var["start"] = biomart_locations["start_position"]
	adata.var["end"] = biomart_locations["end_position"]

	# change the index back to gene names
	adata.var_names = adata.var["gene_name"]
```

After the annotation, you can find the gene coordinates in 
adata.var[[”chromosome”, “start”, “end”]]. 

For copy number analysis with inferCNV, you have to remove all the “non-standard”
 chromosomes (include only chromosomes 1-22 + XY). In addition, for the tool to work,
 you have to add “chr” to the chromosome name because it’s not included in the biomart
 file.

