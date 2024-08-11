+++
title = 'ACMG-AMP classification for germline variant pathogenicity'
date = 2024-08-03T15:29:55+09:00
draft = false
categories = ['bioinformatics']
tags = ['ACMG', 'germline','variant interpretation']
+++

## Germline variant effect interpretation is challenging

Assessing the clinical significance of germline variants is an important task in genetics. Identification of pathogenic germline variants may affect the diagnosis and treatment decisions and hence potentially improve and personalize the patient care. In addition, the identification of such heritable risk is important for the family of the patients as they now may be provided genetic counseling and cancer surveillance if they are found to be carriers of the same variant.

Determining variant pathogenicity can be challenging due to the complexity and diversity of genetic variations. Evidence for variant interpretation may be scarce or conflicting which often leads variants to be classified as variants of uncertain significance (VUS).

How to determine the clinical significance, pathogenicity, of a variant?

## Evidence-based system for variant interpretation

The American College of Medical Genetics and Genomics and the Association for Molecular Pathology (ACMG-AMP) (Richards et al., 2015) has developed guidelines for variant interpretation and is currently widely used system in this task. 

The guidelines provide a standardized and systematic framework for evaluating the pathogenicity of germline variants. It is based on a system that looks through evidence for the variant and scores the evidence based on how strongly it supports pathogenicity or benignity. 

For final variant interpretation the evidence for the variant in question is combined and variant will be assigned to one of the five pathogenicity classes: Pathogenic (P), Likely pathogenic (LP), Variant of uncertain significance (VUS), Likely benign (LB) or Benign (B) (Figure 1). 

{{< figure src="images/ACMGpipeline.jpg" title="Figure 1. ACMG classification system" >}}

Evidence used for variant interpretation includes:

- population control data (eg. gnomAD (Karczewski, et al., 2020))
- *in silico* /computation predictions (such as pathogenicity prediction score AlphaMissense (Cheng et al., 2023))
- Functional data (eg. Hotspot regions)
- Clinical data/earlier findings (eg. ClinVar (Landrum et al., 2014))

In addition, the variant may be evaluated based on segregation, *de novo*, and allelic data if such information is available. 

All the ACMG-AMP guideline evidence and their perceived strength can be seen from table 1. 


{{< figure src="acmgrules.jpg" title="Table 1. Richards et al., ACMG classification rules" >}}


## Implementing the ACMG-AMP guidelines

Multiple tools have been created to implement these guidelines, such as TAPES (Xavier et al., 2019). Recently, tools utilizing machine learning methods for variant prediction, such as MLVar (Nicora et al., 2022), have been published.These tools have been created to help automate the variant interpretation as going through a large number of variants manually is time-consuming task. Automation creates some restrictions because it is not possible to automate evidence such as segregation data which is patient specific. Some of these problems can be dealt with by making the user provide the additional data separately for the analysis. 

## Challenges of the automated classification system

Automation can speed up the analysis and help to filter and decrease the variant set to those predicted as pathogenic. However, because the rules allow for some subjectivity in the interpretation, there are inconsistencies  between clinical laboratories (Kim et al., 2019). In addition, the varying prevalence of variants in different populations poses a challenge when determining whether a variant is rare or not. 

Another huge challenge in the guidelines is the rules using lists of genes with some ‘known’ qualities. For example, very strong rule PVS1 is applied for a null variant located in a gene for which loss-of-function (LOF) is a known mechanism of disease. In practice, many tools interpret this rule by constructing a list of genes for which there are certain number of earlier identified pathogenic null variants. Benign rule, BP1 missense variant in a gene where only truncating variants cause disease, may reduce the importance of new pathogenic missense variant findings because in many cases disease associated genes have been found by identifying variants with clear, big impact (truncating/null variants). 

It’s important to be aware of the potential caveats and limitations of the classification system, especially when identifying pathogenic variants in a clinical setting. ACMG system should work as a guideline to restrict the number of variants of interest for further inspection and not completely erase the manual work required to assess the importance of variants on patient level.

More work should still be done to clarify and develop the guidelines in order to reduce the inconsistencies.  This is important also for the machine learning based methods because many tools use the variant databases, consisting of variants with manual or automated ACMG classifications, as training data. 

## References

Richards S, Aziz N, Bale S, et al. Standards and guidelines for the interpretation of sequence variants: a joint consensus recommendation of the American College of Medical Genetics and Genomics and the Association for Molecular Pathology. *Genet Med*. 17(5):405-424. (2015) doi:10.1038/gim.2015.30

Karczewski KJ., Francioli LC., Tiao, G. et al. The mutational constraint spectrum quantified from variation in 141,456 humans. *Nature* ****581, 434–443 (2020). https://doi.org/10.1038/s41586-020-2308-7

Cheng J et al. Accurate proteome-wide missense variant effect prediction with AlphaMissense. Science 381,eadg7492(2023). DOI:10.1126/science.adg7492

Landrum MJ, Lee JM, Riley GR, Jang W, Rubinstein WS, Church DM, Maglott DR. ClinVar: public archive of relationships among sequence variation and human phenotype. Nucleic Acids Res. 42(1):D980-5 (2014). doi: 10.1093/nar/gkt1113

Xavier A, Scott RJ, Talseth-Palmer BA. TAPES: A tool for assessment and prioritisation in exome studies. PLoS Comput Biol 15(10):e1007453 (2019). doi: 10.1371/journal.pcbi.1007453

Nicora, G., Zucca, S., Limongelli, I. et al. A machine learning approach based on ACMG/AMP guidelines for genomic variant classification and prioritization. Sci Rep 12, 2517 (2022). https://doi.org/10.1038/s41598-022-06547-3

Kim Y, Ki C, Jang M.  Challenges and Considerations in Sequence Variant Interpretation for Mendelian Disorders.  Ann Lab Med 39:421-429 (2019).  https://doi.org/10.3343/alm.2019.39.5.421
