+++
title = 'Human Reference Genomes: GRCh37, GRCh38, hg19, hg38, v37, b37?'
date = 2024-08-03T15:30:11+09:00
draft = false
categories = ['genetics']
tags = ['reference genome']
+++

## What is a reference genome?

A human reference genome, reference assembly, is a sequence of nucleotides representing genes 
and non-coding regions of the genome.
It is made up from a combination multiple individual's DNA and hence not representing a 
genome of any single human but an average of all humans in the world. However,
it is difficult to get proper representation of all humans due to the bias in the 
selection of participants for the genome sequencing and the great variantion in some parts 
of the genome between populations.
The first version was released in 2000 and there have been 
multiple improvements and versions after that. 

## What is it used for?

Reference genomes are used in genetic research as a reference map to help assemble 
sequencing data. They are also used in sample comparison to identify
differences in the genome between individuals.  

## What are the different versions?

### Institutional differences

There are number of reference genome assemblies available from different sources which 
are then named based on the institution.

| Reference | Institute | Contig names | Resource | Notes |
|   :---:   |   :---:   |     :----:   |   :---:  | :--:  |
| GRCh37, GRCh38 | The Genome Reference Consortium (GRC) | chr prefix | ftp://ftp.ncbi.nlm.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/GCF_000001405.25_GRCh37.p13_genomic.fna.gz |  Constructed from sequenced bacterial artificial chromosomes (BACs) |
|   hg19, hg38 | The University of California at Santa Cruz (UCSC) | chr prefix | http://hgdownload.cse.ucsc.edu/goldenPath/hg19/bigZips/hg19.fa.gz | Based on GRCh reference but contains some alterations |
| b37	    | Broad institute | no 'chr' prefix | https://console.cloud.google.com/storage/browser/_details/broad-references/hg19/v0/Homo_sapiens_assembly19.fasta | Based on GRCh. Alterations: bases with low confidence masked out. | 
| human_g1k_v37 | The 1000 Genome project | No 'chr' prefix | http://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/reference/human_g1k_v37.fasta.gz | Equivalent to b37, but without human herpesvirus 4 type 1 decoy sequence |


Mostly the references contain the same data, although they use different naming for 
contigs (hg19 and GRCh use ‘chr’ prefix whereas other two don’t).

However, there are some sequence differences between the references. 
There are differences on chromosomes 3 and Y, and mitochondrial contig as can be seen 
on the reference comparison table at broad institutes article linked below.


### Release builds 37 vs 38

Each major version of human reference genome assemblies are numbered differently and the
latest ones are GRCh37 (release 2009) and GRCh38 (release 2013) (hg19 and hg38 in the UCSC). Each version comes with 
updated annotations to the earlier. 

GRCh38 assembly has repaired incorrect reads and misassembled areas in GRCh37. 
In addition, it has been updated to include model centromere sequences and alternate
 loci.  GRCh38 has greater amoun of ALT contigs, which are pieces of genome 
that represent alternative haplotypes. 


### References:

https://www.science.org/doi/10.1126/science.abj6987

https://gatk.broadinstitute.org/hc/en-us/articles/360035890711-GRCh37-hg19-b37-humanG1Kv37-Human-Reference-Discrepancies?id=23390

https://www.ncbi.nlm.nih.gov/grc
