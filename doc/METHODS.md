# Methods

Paper-style methods section for the project. High-level, version-agnostic where possible; where versions diverge (v1 CellRanger-ARC + YAP vs. v3 IGVF uniform pipeline) the active pipeline is noted. For active analysis version and per-step code pointers see [OVERVIEW.md](OVERVIEW.md) and [PIPELINES.md](PIPELINES.md).

## Experimental design

Stem-cell-derived islets (SC-islets) were differentiated from the A2 cell line (CRISPR-modified H1-hESC) and exposed to six conditions spanning inflammatory, metabolic, and glucocorticoid stress: untreated control, three-cytokine cocktail (3-cyt), interferon-γ (IFNγ), exendin-4 plus high glucose (Ex-4_HG), palmitate, and dexamethasone (dex). Samples were collected at 0, 6, 24, 48, and 72 hours post-stimulation, targeting two biological replicates per condition–timepoint (three for controls). The analysis cohort comprises 54 samples across three differentiation batches (DM023, DM041, DM060) profiled by 10X Multiome (joint snRNA + snATAC) and snm3C-seq (joint methylation + chromatin conformation).

## 10X Multiome processing

FASTQs were processed with the IGVF single-cell uniform pipeline (v3 analysis; v1/v2 used 10X CellRanger-ARC), producing per-sample fragment files, filtered cell-barcode matrices, and per-cell QC metrics aligned to hg38 / GENCODE.

## Sample-level QC

Per-sample RNA and ATAC QC was orchestrated by [CellCommander](../tools/CellCommander/). **RNA:** ambient-RNA removal with SoupX, doublet detection with scDblFinder and Scrublet, threshold-based filtering on counts, gene number, mitochondrial and ribosomal fractions, followed by normalization (log1p and SCTransform) and PCA/UMAP. **ATAC:** AMULET multiplet detection on uniform-pipeline fragments, quality filtering on fragment counts, TSS enrichment, and peak-region fragment fraction. Benchmarking of v3 uniform-pipeline QC against v2 CellRanger-ARC QC showed ~85% barcode Jaccard at the post-threshold stage and ~79% Jaccard for AMULET doublets, with v3 being marginally more stringent on cells and calling ~22% more doublets.

## Integration and cell type annotation

RNA modality: per-sample normalized matrices were merged; genes expressed in fewer than three cells across the full atlas were dropped prior to normalization to control memory for SCTransform (retaining ~49k of 63k features). SCTransform-variance-stabilized counts were reduced by PCA and batch-corrected across differentiation batches with Harmony. Shared-nearest-neighbor clustering (Leiden) on Harmony embeddings yielded clusters annotated by canonical islet-lineage markers into SC.alpha, SC.beta, SC.delta, SC.EC (enterochromaffin-like), and several progenitor/off-target populations. ATAC modality: merged TF-IDF-normalized peak matrices were embedded via LSI and Harmony-corrected for batch; cluster labels were transferred from RNA using joint barcodes.

## Peak calling

Per cell-type × condition pseudobulk peak sets were called with SnapATAC2 feeding MACS3 on fragment files aggregated at cell-type–condition resolution. Three peak-set variants were generated (pseudo-replicate, timepoint-split, no-replicate) and compared for reproducibility. Consensus peaks across cell-type–condition groups were iteratively extended and merged to a fixed-width union set used for downstream matrix construction and sequence modeling (top 250,000 peaks per group retained for ChromBPNet training).

## Differential analysis

Pseudobulk RNA and ATAC matrices were aggregated per cell-type × condition × replicate and passed to DESeq2. Multiple modeling choices were benchmarked including likelihood-ratio tests (LRT) against reduced models and Wald tests across condition contrasts. The union of significant genes across old-LRT and new-Wald parameterizations served as the working DEG/DAR set, with cell-type-stratified contrasts.

## ChromBPNet sequence modeling

For each of 24 cell-type × condition groups, ChromBPNet models were trained on ATAC fragments within the top-250k group peaks using hg38 as reference. A shared Tn5 bias model was pre-trained on the SC.beta_control background and reused across all cell-type × condition models (single scaling parameter β = 1.0; see `fold_0/bias/1.0/` and `fold_0/chrombpnet/1.0/`). Currently one cross-validation fold is trained (`fold_0`); the layout supports additional folds without code changes. Counts-head Pearson r exceeded 0.80 across all 24 models. Nucleotide-resolution contribution scores for counts and profile heads were computed with `chrombpnet contribs_bw` over the top-250k peaks and stored as `.h5` and `.bw` (`fold_0/chrombpnet/1.0/contributions/`). Per–cell-type–condition averages across folds are written to `<ctc>/average/contributions/` (identity for single-fold runs).

## TF-MoDISco motif discovery

De novo motif discovery was run with TF-MoDISco-lite on averaged counts- and profile-head contribution scores per cell-type × condition (48 runs: 24 × {counts, profile}) with 100,000 seqlets, Leiden resolution 2, and a 400-bp window. Motif reports were annotated against the Vierstra non-redundant motif database (motif-clustering v2.0beta). PFMs were exported and consolidated per group for downstream motif clustering across cell-type–conditions.

## snm3C-seq processing

Single-nucleus methylome plus chromatin conformation data were processed with the YAP pipeline (v1). Reprocessing against IGVF-standard methylation/3C pipelines is planned.

## Integrative analyses (in progress)

- **Peak-to-gene linking:** exploratory multi-modal embedding and correlation-based enhancer–promoter links over peaks and genes within ±500 kb.
- **Gene regulatory networks (GRN):** combining TF-MoDISco motif hits, cell-type–specific accessibility, and DEG sets into condition-specific TF → gene networks.
- **GWAS variant effect prediction:** scoring type-2-diabetes / islet-function GWAS variants through ChromBPNet models to identify cell-type–specific regulatory effects.
- **GSEA / module analyses:** gene-set enrichment on condition-responsive modules via the in-house GSA toolkit ([tools/gene-set-analysis](../tools/gene-set-analysis/)).

## Data and code availability

All processed data are deposited to the IGVF Data Portal under the Hannah Carter lab analysis sets (see [igvf-data/SUBMISSION.md](../igvf-data/SUBMISSION.md) and [igvf-data/PORTAL.md](../igvf-data/PORTAL.md)). Code is organized under `pipelines/`, `tools/`, and `manuscripts/`, with analysis-version layout described in [OVERVIEW.md](OVERVIEW.md).
