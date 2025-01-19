+++
title = 'Introduction to single cell RNA sequencing'
date = 2025-01-19T16:20:21+02:00
draft = false
categories = ['methods']
tags = ['single cell', 'scRNA-seq', 'introduction']
+++


# Single cell vs bulk RNA sequencing

Advances in sequencing technologies have transformed the way we study biological systems.
 They have enabled shifting  the analysis from tissue to single cell level, and allowed a 
deeper level of understanding of complex tissues. Human tissues are often composed of a wide 
variety of different cell types, each with unique molecular characteristics such as gene expression. 
In order to better understand the gene expression and regulation in heterogeneous tissues and for 
example in clonal tumors, it is useful to take the analysis to the single cell level.

In bulk RNA sequencing, the gene expression is analyzed from a mixture of cells within a tissue sample
 (Figure 1). This gene expression is a weighted average across all cells. In heterogeneous samples, 
containing various cell types, the gene expression predominantly reflects the abundant cell types and
 can mask the effect of more rare cell types. This can potentially obscure a signal critical for the 
disease in cases where there are cell-type-specific effects such as blood cancers.

Bulk sequencing cannot reveal differences in gene expression between cell types. However, there are few
 methods developed to estimate cell type specific expression from bulk RNA-seq data such as MuSiC [1].

Single cell RNAseq (scRNA-seq) measures the gene expression at the level of individual cells.
 Single cell methods enable the detection of different cell types in the tissues as well as 
their abundance (Figure 1). Studying gene expression on a single cell level can give more 
insights into the molecular characteristics of different cell types and how the expression 
varies between the cells in health and disease.

{{< figure src="images/single_cell_intro.png" title="Figure 1. Created with BioRender." >}}

# Overview of single cell RNA sequencing 

## Droplet-based sequencing 

There are several different types of methods for scRNA-seq but the most popular of them is 
droplet-based method such as 10X Genomics [2].

The 10X Genomics scRNA-seq method creates Gel beads-in-emulsion (GEMs) (Figure 2). 
The workflow generates oil droplets, that encapsulates one barcoded cell bead, single
 cell, and reagents for the reverse transcription and cell lysis. By encapsulating all 
the items necessary for the sequencing library preparation inside a droplet, it’s possible 
to mark the RNA transcripts of each cell separately.

{{< figure src="images/10XGenomics.png" title="Figure 2. Image credit: 10X Genomics [2]." >}}

## How the cells and transcripts are detected

In the 10X Genomics scRNAseq method, the Gel beans are barcoded ie. they include a short 
sequence which is sequenced with the transcript that helps to identify the cell of origin. 
There are also other tags, such as unique molecular identifier (UMI) that gives each transcript
 its own unique molecular fingerprint. UMIs are useful in the quality control of the sequencing 
data and can be used to reduce PCR amplification bias. Poly(dT) sequence is used to capture mRNA 
transcripts from their 3’ or 5’ ends. In addition, adapter sequences required for sequencing are 
included.

The composition of the capture/sequencing tag and the workflow what happens inside a single GEM 
can be seen in figure 3. Inside a GEM, there is the gel bead with the capture/adapter tags attached, 
one cell (ideally), and reagents for creating a sequencing library from the mRNA transcripts inside 
the cell. The cell barcode is the same for each tag attached to a single GEM.This marks all the 
transcripts inside this GEM to a single cell. UMIs, however, are unique and they give an unique 
tag for each single transcript in the cell. 

The enzymes inside the GEM lyse (break down) the cell, releasing the mRNA trapped inside the cell. 
The mRNAs pair to the poly(dT) part of the tag with their poly-A tail and the reverse transcription 
can start. The original mRNA is used as a template to build complementary DNA that is attached to our
 capture/sequencing tag. The gel bead dissolves and the tagged cDNA transcripts can be purified from 
the GEM. All the cDNAs can be now sequenced together, because they all contain a cell specific barcode
 and a UMI tag.

{{< figure src="images/GEMs.png" title="Figure 3. Created with BioRender. Workflow figure adapted from 10X Genomics." >}}

# Challenges and limitations

Despite the advancements in single cell technologies, scRNA-seq is still a complex technique with 
many challenges and limitations. These all should be taken into careful consideration during the 
planning of scRNA-seq experiments, and when deciding whether single cell resolution is even needed 
or would bulk sequencing be enough.

## Costs and scalability

For single cell methods, the sample generation, library preparation, and sequencing is more expensive
 compared to bulk methods.This is a significant barrier for many researchers in using these methods, 
especially when the number of cells or samples studied is large. In addition to higher costs of single
 cell sample preparation, it is also more laborious and time consuming.

While droplet-based methods like 10X Genomics allow high throughput, there are limitations in terms of
 the maximum number of cells or the sensitivity for rare transcripts. With the limited number of 
maximum cells, it might be difficult to study rare cell types efficiently.

## Computational challenges

In short, analysis of single cell data is more complicated and difficult to interpret. 
ScRNA-seq generates a large volume of data when each sample consists of thousands of cells and for
 each cell from 50,000 to 150,000 reads are generated [3]. In comparison, a typical bulk RNA-seq 
analysis is up to 20 million reads per sample. ScRNA-seq data has high dimensionality that requires
 advanced statistical methods as well as high computational power for the analysis. This means more
 memory, larger storage, and more running time. 

Many current methods are not optimized for scRNA-seq data with a large amount of samples (Seurat, 
inferCNV). Currently there are few GPU-based solutions for analysis and more should be developed 
because the datasets and thus the computational requirements keep growing 
(For example rapids-singlecell within the scverse : [4]).

## Technical challenges

Technical variability across cells and samples may cause more variation than the actual biological 
cell state and steps should be taken to remove them. 

### Capture efficiency

Characteristics typical for scRNA-seq data that complicate the analysis include the low sequencing 
depth per cell. Droplet-based methods have shallow sequencing depth (10-50% of the transcriptome 
detected) which results in zero counts for  many genes. This creates an issue because we cannot say
 if the zero counts are due to the transcript not captured or if the gene is actually not expressed.
 The capture efficiency may also be cell specific - different cells may get different types and 
numbers of transcripts captured. 

### Library quality

The quality of the sequencing library affects the quality of the data and downstream analyses.
 ScRNA-seq analysis is based on an assumption that each GEM contain a single cell and many of 
the library quality issues violate this. Library quality issues often indicate problems at sample
 processing: 

- samples were not processed fast enough and RNA degraded or cells started to die
- cell dissociation was not done gently enough which might have led to a release of RNA causing ambient RNA effect
- dissociation also might not have been done ‘hard’ enough and the cells stick together leading to doublets

{{< figure src="images/Singlecell_library_quality.png" title="Figure 4. Created with BioRender." >}}

### Amplification bias

Amplification bias is well known effect in PCR reaction for sequencing　（Figure 5).
 Some transcripts are amplified more than the others which results in faulty quantities
 of the transcripts.This bias is corrected with UMIs - only the unique transcripts are counted.

{{< figure src="images/lexogen.png" title="Figure 5. Image credit: Lexogen [5]." >}}

### Batch effects

Batch effects are one of the most significant confounding issues in scRNA-seq analysis. 
Multiple different factors during the processing and sequencing create batches: 

- Performing RNA isolations on different days
- Library preparations on different days
- Different persons performing sample preparations
- Different reagents
- Different locations of sample preparation or sequencing

Ideally, all the samples would be processed exactly the same way. However, this is not always possible. In this case, it is important to plan the processing batches in a way that the different groups/phenotypes being studied are not in separate batches. Processing the groups in separate
 batches makes it difficult to separate the batch effect from the true biological effect
 between these groups (Figure 6).

{{< figure src="images/confounded_batch.png" title="Figure 6. Image credit Hicks et al [6]." >}}

Instead, try to make sure you split the replicates of each group across batches (Figure 7). 
Make sure to keep the batch information for later analysis stage where you can regress out 
the variation due to batch effect or integrate across batches.

{{< figure src="images/batch_effect_pca.png" title="Figure 7. Image credit: Lexogen [5]." >}}

## Biological variability across cells/samples

Biological variability across cells/samples is both an opportunity and a challenge 
in scRNA-seq studies. Uninteresting sources of biological variation may obscure the 
important differences between cell types/states. Some of the (uninteresting) sources 
of biological variability are transcriptional bursting, varying rates of RNA processing,
 continuous or discrete cell identities, environmental stimuli, and cell cycle. 

### Transcriptional bursting and cell cycle

Genes can be transcribed from DNA to RNA in "bursts" or "pulses," a phenomenon known as
 transcriptional bursting. Since genes aren't needed continuously, transcription occurs 
only when necessary. This bursting pattern explains some of the high variability observed 
between cells. The process is closely tied to the cell cycle, as cells express different genes
 based on their current state or transition phase (G1, S, G2/M). For instance, DNA replication 
genes are upregulated during S phase, while genes controlling mitosis peak during G2/M.

### RNA processing

Messenger RNA (mRNA) is not a very stable molecule and it is quickly processed or degraded 
depending on the needs of the cell. The needs for various mRNA products may differ a lot which 
leads to different rates of mRNA processing: some transcripts may be degraded faster than the others. 

### Cell identities

Cell identities (cell types) can be either continuous or discrete, with each type having a 
unique gene expression profile that reflects its function and state. In discrete cell types, 
the differences are clear—for example, T-cells and B-cells express distinct markers that identify
 their type. However, some cell types are more challenging to distinguish. In bone marrow, for 
instance, hematopoietic stem cells gradually specialize into different blood cell types through 
multiple intermediate progenitor stages. These transitional states make it difficult to clearly 
separate and identify the cells along their developmental pathway. 

### Environmental stimuli

Various sample-specific aspects in the environment may affect the gene expression patterns. 
These can be things such as stress, disease status, infections, and medications. If you are 
studying diseases such as cancers, it is important to take into notice what sorts of treatments 
and medications the patients have been given before sampling. These all can have a serious effect 
in the analysis.

# References

1. Wang, X., Park, J., Susztak, K. *et al.* Bulk tissue cell type deconvolution with multi-subject single-cell expression reference. *Nat Commun* **10**, 380 (2019). https://doi.org/10.1038/s41467-018-08023-x
2. [10X genomics](https://www.10xgenomics.com/blog/the-next-generation-of-single-cell-rna-seq-an-introduction-to-gem-x-technology) 
3. Single cell discoveries https://www.scdiscoveries.com/blog/knowledge/single-cell-transcriptome-sequencing-costs-considerations/ 
4. NVIDIA-Genomics: [RAPIDS single cell](https://github.com/NVIDIA-Genomics-Research/rapids-single-cell-examples)
5. [lexogen.com](https://www.lexogen.com/rna-lexicon-what-are-unique-molecular-identifiers-umis-and-why-do-we-need-them/)
6. Stephanie C Hicks, F William Townes, Mingxiang Teng, Rafael A Irizarry, Missing data and technical variability in single-cell RNA-sequencing experiments, *Biostatistics*, Volume 19, Issue 4, October 2018, Pages 562–578, https://doi.org/10.1093/biostatistics/kxx053
