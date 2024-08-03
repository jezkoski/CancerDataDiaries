+++
title = 'Human Reference Genomes: GRCh37, GRCh38, hg19, hg38, v37, b37?'
date = 2024-08-03T15:30:11+09:00
draft = true
tags = ['reference genome']
+++

## What is a reference genome?

A human reference genome, reference assembly, is made up from a combination multiple individual's DNA and hence not representing a genome of any single human. The first version was released in 2000 and there have been multiple improvements and versions after that. 

Reference genomes are used in genetic research as a reference map to help assembling DNA sequencing or transcriptomics data. 

## What are the different versions?

There are number of reference genome assemblies available from different sources which are then named based on the institution.


UCSC -hg19, hg38

The University of California at Santa Cruz

Based on GRCh!, but contains some alterations: contig name changes

http://hgdownload.cse.ucsc.edu/goldenPath/hg19/bigZips/hg19.fa.gz

Ensembl - GRCh

The Genome Reference Consortium (GRC) Human Build 

- Assembly constructed from sequenced bacterial artificial chromosomes (BACs)
- limitations of BAC cloning
    
    → underrepresentation of repetitive sequences
    
    → several assembly gaps are unsolvable because of incompatible structural polymorphisms on their flanks
    
    [ftp://ftp.ncbi.nlm.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/GCF_000001405.25_GRCh37.p13_genomic.fna.gz](ftp://ftp.ncbi.nlm.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/GCF_000001405.25_GRCh37.p13_genomic.fna.gz)
    

Broad institute - b37

Based on GRCh, the Genomics platform refers to this when they use hg19!

Alterations, changes for bases with low confidence, masked out

https://console.cloud.google.com/storage/browser/_details/broad-references/hg19/v0/Homo_sapiens_assembly19.fasta 

100 Genome project v37/human_g1k_v37

Equivalent to b37 except it doesn’t contain the decoy sequence of human herpesvirus 4 type 1. 

http://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/reference/human_g1k_v37.fasta.gz 

Mostly the references contain the same data, although they use different naming for contigs (hg19 and GRCh use ‘chr’ prefix whereas other two don’t).

However, there are some sequence differences between the references. There are differences on chromosomes 3 and Y, and mitochondrial contig as can be seen on the reference comparison table at broad institutes article.

References:

https://www.science.org/doi/10.1126/science.abj6987

https://gatk.broadinstitute.org/hc/en-us/articles/360035890711-GRCh37-hg19-b37-humanG1Kv37-Human-Reference-Discrepancies?id=23390

https://www.ncbi.nlm.nih.gov/grc
