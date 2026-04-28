## Major TODOs

### Retrain ChromBPNet models at different resolutions

For igvf-data/igvf_sc-islet_10X-Multiome, we need to retrain ChromBPNet models at different resolutions. This includes:

1. Using the v2 pseudobulks and peak calls to retrain cell_type-condition models
2. Call TF-to-peak links using each model. This needs to continue to be flushed out, but the latest code for this is at manuscripts/ChromBetaNet/bin/7_motif2cRE_linking

### Recall peak-to-gene links using Gaulton lab pipeline

Currently something Luca and Sara are working on for the igvf-data/igvf_sc-islet_10X-Multiome data. We need to stay on top of:

1. Getting them the right data for v2
2. Analyzing the results they give us

### Impute a INS secretion score at single cell level using RNA expression of known insulin secretion gene sets

1. Curate a set of known insulin secretion gene sets (e.g. from Reactome, KEGG, literature)
2. Use decoupler implementation of AUCell to calculate an insulin secretion score for each cell based on expression of these gene sets
3. Validate the score by checking if it correlates with expected patterns (e.g. higher in beta cells, higher in stimulated conditions, etc.) and by comparing to any available experimental data on insulin secretion if possible --> perform an overall exploratory analysis of the score to see if it captures meaningful biology

### Finalize new DESeq2 pseudobulk methodology and run on RNA and ATAC

For igvf-data/igvf_sc-islet_10X-Multiome, we need to finalize the new DESeq2 pseudobulk methodology and run it on both RNA and ATAC data. This includes:

1. Investigating and summarizing the results of tests of various approaches in igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_02_19
2. Comapring to old code (igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_02_02/7_differential_analysis) and results (igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30/7_differential_analysis)
3. Deciding on the final approach and implementing it in `bin/7_differential_analysis`
4. Noting down any remaining issues or future directions for improving the differential analysis approach (e.g. collinearity issues, pseudobulk vs single-cell DE, etc.)

### Finalize peak calling

For igvf-data/igvf_sc-islet_10X-Multiome, we need to finalize the peak calling approaches we want to use. This includes:
 
1. Reviewing what has beenn done in v2 (igvf-data/igvf_sc-islet_10X-Multiome/bin/5_peak_calling) and the results (igvf-data/igvf_sc-islet_10X-Multiome/results/5_peak_calling)
2. Calling peaks without replicates (to be used for sequence model training)
3. Implementing and running the peak annotation and post processing methodology in `bin/5_peak_calling` and applying it to all sets of peaks (pseudobulk, single-cell, with and without replicates). Peak annotation only needs to be done for consensus peaks, postprocessing for all
4. Doing a comparitive analysis of peak calls. First, all new results to each other, then the new results vs the old: igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30/5_peak_analysis

### Upload v2 data to GCP (`bin/9_upload/1_upload_to_gcp.ipynb`)

For igvf-data/igvf_sc-islet_10X-Multiome, we need to upload the v2 results to GCP. This includes:

1. Formatting and uploading single cell matrices matrices (especially ATAC ones)
2. Formatting and uploading pseudobulk results
3. Formatting and uploading differential analysis results (when finalized)

### Identifcation of gene modules correlated with INS secretion score and/or disease

For igvf-data/igvf_sc-islet_10X-Multiome, we need to identify gene modules correlated with the insulin secretion score and/or disease status. Our previous attenopts at this are nested in the igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30/7_differential_analysis. We basically just cluster genes using deg patterns across the different conditions and then check if any of the clusters are correlated with the insulin secretion score or disease status. We need to revisit this

1. Coordinating with Luca and Sara to run LIGER on the data to identify gene modules
2. Other latent factor models? hdWGCNA?
3. Checking if any of the identified modules are correlated with the insulin secretion score or disease status. E.g. we used MAGMA to check if gene clusters were enriched for genes in T1D/T2D GWAS loci. This was the basis for me building the tools/gene-set-analysis.

### Begin v3 with IGVF uniform pipeline processed data

For igvf-data/igvf_sc-islet_10X-Multiome, we need to begin v3 of the analysis using IGVF uniform pipeline processed data. This includes:

1. Sorting out the R2 barcode issue for the element samples
2. Downloading the new data and running sample_qc
3. Running integration and peak calling on the new data
4. Running differential analysis on the new data (once methodology is finalized)

### Integrative analysis of igvf-data/igvf_sc-islet_10X-Multiome and public-data/Augsornworawat2023_sc-islet_10X-Multiome

Both are multiome datasets of sc-islet samples. We would like to see what can be learned by integrating the two datasets. This includes:

1. Integrating the RNA of the two datasets using a method into a joint UMAP
2. Training chrombpnet models on ATAC data for matched cell types so we can compare the sequence determinants of chromatin accessibility in the two datasets
3. Comparing peak-to-gene links called in the two datasets (using the same method)


### Integrative analysis of igvf-data/igvf_sc-islet_10X-Multiome and public-data/islet_10X-Multiome

Both are datasets of treated islets with different stiumuli. One is stem cell derived and the other is primary. We would like to see what can be learned by integrating the two datasets. This includes:

1. Integrating the RNA of the two datasets using a method into a joint UMAP
2. Comparing the response to stimuli in the two datasets, both at the RNA and ATAC level. This includes comparing differential analysis results and doing a more global comparison of the response to stimuli in the two datasets (e.g. by comparing the gene programs that are upregulated in response to stimuli in the two datasets, comparing the changes in chromatin accessibility in the two datasets, etc.)
3. Comparing peak-to-gene links called in the two datasets (using the same method)
4. Comparing the sequence determinants of chromatin accessibility in the two datasets by training chrombpnet models on ATAC data for matched cell types in the two datasets and comparing the important motifs and sequences identified by the models

### Reprocess snm3c-seq with new samples

For igvf-data/igvf_sc-islet_snm3c we need to reprocess the data with new samples. This includes:

1. Transferring the new data from TSCC to this cluster
2. Running the data through the same pipeline as before (bin/1_process) and doing sample QC (bin/2_sample_qc) ...
3. Dealing with batch correction
...

### Integrative analysis of igvf-data/igvf_sc-islet_snm3c and public-data/Dominguez2020_sc-islet_bulk methylation data

public-data/Dominguez2020_sc-islet_bulk has bulk methyl seq that it would be interesting to compare to the snm3c-seq data in igvf-data/igvf_sc-islet_snm3c. This includes:

1. Processing the bulk methylation data to get comparable metrics to the snm3c-seq data (e.g. methylation levels in different genomic regions, etc.). Previously failed the nextflow pipeline
2. Comparing the methylation patterns in the bulk data to the snm3c-seq data, both globally and at specific loci of interest (e.g. promoters of key genes, etc.)

### Rebuilding base-GRNs for SC-beta cell types

Also needs to get flushed out, but the latest code for that is here: manuscripts/ChromBetaNet/bin/8_GRN

Prune edges with SCENIC like approach
In silico TF perturbations
In silico cRE perturbations?

### Retrain CpGNet methylation models on new pseudobulk methylation data

The first pass of this is here: igvf-data/igvf_sc-islet_snm3c/bin/7_cpgnet

I have had a postgrad student working on this, so we need to integrate and possible get him to train mdoels

### Repeat variant effect predictions, with inclusion of more. This will become more a variant analysis

1. I think the simplest thing to first do is just look for intersections. Here we should look broadest first, then narrow down. So first look for any GWAS (maybe LD expanded) that intersect peaks, then look for any that intersect peak-to-gene links, then look for any that intersect TF-to-peak links. We can also look for any that intersect the important motifs identified by the chrombpnet models after the next few steps
2. ChromBPNet models trained on new pseudobulk peaks
3. CpGNet models trained on new pseudobulk methylation data
4. Others? The network?

### Prioritizing cREs to validate

The last step to all of this is to find a set of cREs to validate with a CRISPRi experiment. This will likely be based on a combination of the results of all of the above analyses, but we can start to get a list together and prioritize based on factors like: strength of evidence from the above analyses, relevance to disease, novelty, etc.

A first pass of this can be found here: manuscripts/ChromBetaNet/scratch/2025_12_22. We will come back to this