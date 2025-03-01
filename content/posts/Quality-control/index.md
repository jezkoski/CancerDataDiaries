+++
title = 'Single cell RNA-seq: Preprocessing - Quality Control and Filtering'
date = 2025-03-01T12:46:06+02:00
draft = true
categories = ['methods']
tags = ['single cell', 'scRNA-seq', 'Quality Control']
+++



{{< figure src="images/sc-workflow.png" title="Created with BioRender." >}}

Single cell RNA-seq (scRNA-seq) data is noisy by nature. It contains systematic and random noise due to 
technical and biological biases that can obscure the biological signal. The noise and biases can break 
the assumption of having a single viable cell in each droplet in droplet-based single cell sequencing. 
Quality control (QC) and filtering are extremely important steps to attempt to remove the noise. 
The goal in scRNA-seq QC is to identify and keep high quality viable cells in the analysis because 
low-quality data can distort the downstream analysis. 

Typically, QC is performed by setting manual thresholds for following metrics after visual inspection: 

- number of counts per bar code
- number of genes per bar code*
- fraction of counts from mitochondrial genes per bar code*

*Bar code usually refers to a single cell, but that’s not always the case. 
Sometimes two cells can be sequenced inside the same droplet and thus this 
single bar code includes the information of two cells.

There are also other factors to look out for in scRNA-seq QC such as ambient RNA contamination 
and presence of doublets. There are tools for detecting these and whether or not to use them can
 be highly case-specific.

When starting QC, visually inspect the data and know the characteristics of your cells. 
Different tissues or cell types may display distinct characteristics, which is why the same set of
 thresholds and filters may not be appropriate for all cell types! In addition, inspecting each QC 
metric in isolation can lead to misinterpretation of cellular signals. That is why it’s extremely 
important to know your data! For example, typically cells with a high fraction of counts in mitochondrial 
genes are removed as low-quality/dying cells. However, those cells may be involved in respiratory 
processes and you might want to keep them depending on your research question.
 There can also be differences in other quality metrics depending on the cell type.

## Number of counts per bar code

{{< figure src="images/UMIcount.png" title="Created with BioRender." >}}

In scRNA-seq data, the counts in the count matrix refer to UMI counts ie. number of transcripts in the
 cell. Each UMI refers to a single mRNA transcript in the cell and allows for removal of PCR duplicates.
 Looking at the total number of counts per cell is an important quality metric. Low UMI counts indicate 
low RNA content, the RNA was lost during library preparation. Maybe the cells were damaged and the cells 
are dying which lead to decrease in counts in this cell. On the other hand, extremely high UMI counts may
 indicate doublet cells.

## Number of genes per bar code

{{< figure src="images/genedistribution.png" title="Created with BioRender." >}}

Cells should express a diverse range of genes. Too few genes suggests the cells are dead. On the other 
hand, too many genes may suggest a presence of doublets or multiplets. These might often show as a 
shoulder peak next to the main peak in histogram. Depending on your datasets, the second peak might 
also be a group of cells of larger cell type. Number of detected genes strongly correlates with the 
size of the cells so in the case of heterogeneous cell population, it’s important not to look at this 
metrics in isolation from the other metrics.

## Mitochondrial counts

Dying cell or cells with broken membranes typically express distinct features and should be removed 
from the analysis. In the ‘standard’ single cell preprocessing, cells with low number of detected genes
 per cell, low count depth, and high fraction of mitochondrial genes are typically removed as low-quality
 and dying cells.

{{< figure src="images/mtleak.png" title="Created with BioRender." >}}

High fraction of mitochondrial RNA typically indicates dying cell with broken membranes. When the cell 
membrane is broken, the mRNA in the cell solution leaks out of the cell and the mitochondrial stays in 
if the damage is only moderate. This shows as an enrichment of mitochondrial RNA reads when this broken
 cell is captured and sequenced.

## Ambient mRNA/Background mRNA

Ambient/background mRNA arises from either dissociation problems - cells break down during the 
dissociation - or from malignant cells releasing mRNA outside of the cell. Ambient mRNA may disturb 
the analysis by causing misinformed cell typing: mRNA may include transcripts of genes only expressed 
in certain cell types and including these transcripts to another cell type can result in faulty typing. 

## Doublets

{{< figure src="images/doublets.png" title="Created with BioRender." >}}

Doublets or multiplets are formed when two cells are captured inside the same droplet and sequenced 
under the same bar code. Doublets may be formed by two cells of the same cell type, which is called 
homotypic doublet. Homotypic doublets are difficult to identify and the doublet identification methods
 don’t typically focus on these. Doublets that are formed by two cells of different type are called 
heterotypic doublets. Identification of heterotypic doublets is much more critical, because they are 
often misclassified and they distort downstream analysis. Heterotypic doublets may lead to classification
 of in-between cell types which wouldn’t originally be in the data. In downstream analysis, they can be 
seen as strings between cell types in the UMAP, or they can form their own cluster. 

In simple cases, doublets can be filtered out in WC by removing a group of cells with much higher number
 of genes and UMIs compared to the other cells. There are also several emthods for detecting doublets 
computationally such as scDblFinder and DoubletFinder. DoubletFinder generates artificial doublets from
 the original scRNA-seq data, merges the artificial doublets with it and performs PCA. For each cell, a 
proportion of artificial doublets is defined and later those cells are removed whose proportion is larger 
than expected. 

However, depending on your dataset, it might not always be feasible to do doublet removal. Many doublet 
detection methods may get rid of cells with intermediate or continuous phenotypes, because they are 
classified as doublets. Make sure you don’t accidentally remove all cells from a larger cell type or 
from intermediate cell type from the analysis!
