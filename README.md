<!-- Project directory guide. For the GitHub org landing page, see profile/README.md -->

# The impact of genomic variation on environment-induced changes in pancreatic beta cell states

## Organoid differentiation and stimulation

Differentation protocol: doc/dacc/protocols/Pancreatic_beta_cell_diff_v2_Oct2022_CMP05979.pdf

## IGVF data

### 10x multiome

Joint scRNA-seq + scATAC-seq from SC-islets under stimulation conditions. Detailed instructions in `igvf-data/igvf_sc-islet_10X-Multiome`. Processing steps: data download → Cell Ranger ARC → sample QC → integration → peak calling → differential analysis.

### snm3c-seq

Single-nucleus methyl-3C (joint methylation + chromatin conformation) from SC-islets. Detailed instructions in `igvf-data/igvf_sc-islet_snm3c`. Processing steps: data download → mapping → methscan → allcools → hicluster → pseudobulk → cpgnet.

### GSIS functional data

Glucose-stimulated insulin secretion data from SC-islets. Currently a placeholder with methods doc in `igvf-data/igvf-sc-islet-gsis/`.

## Publicly available reference datasets

Each subdirectory in `public-data/` is a standalone repo with processing code for a published islet dataset:

SC-islet (stem cell-derived):
- `Dominguez2020_sc-islet_bulk` — Bulk RNA/ATAC
- `Zhu2023_sc-islet_scRNA-seq` — scRNA-seq across differentiation timepoints
- `Zhu2023_sc-islet_snATAC-seq` — snATAC-seq across differentiation timepoints
- `Augsornworawat2023_sc-islet_10X-Multiome` — 10X Multiome of 3 independent differentiations

Primary islet:
- `Chiou2021_islet_snATAC-seq` — snATAC-seq
- `Wang2023_islet_snATAC-seq` — snATAC-seq
- `islet_10X-Multiome` — 10X Multiome
- `Maestas2024_islet_10X-Multiome` — 10X Multiome
- `HPAP` — Human Pancreas Analysis Program

Cell line:
- `EndoC-bH1_treated_ATAC-seq` — EndoC-bH1 treated ATAC-seq
- `mo_EndoC-bH1_ATAC-seq` — EndoC-bH1 bulk ATAC-seq

## Gene network analysis

`gene-networks/` is where we evaluate and compare gene network inference methods across datasets and tools. Currently contains a gene set evaluation pipeline built on the `gene-set-analysis` toolkit (see Tools) with Nextflow-orchestrated runs. Input gene sets are versioned in `inputs/`, analysis notebooks live in `src/`, and pipeline run records are in `runs/`.

## Pipelines

Multiome-specific processing workflows in `pipelines/`:

- `multiome-sample-qc` — Automated sample QC and processing for RNA/ATAC multiome
- `multiome-cell-annotation` — Semi-automated cell annotation for RNA/ATAC multiome
- `multiome-peak-analysis` — Semi-automated pseudobulk peak calling and analysis

## Tools

Reusable packages in `tools/`:

- `gene-set-analysis` — Gene set annotation pipeline (ORA, GSEA, motif, GWAS enrichment)
- `CellCommander` — CLI for common single-cell analysis tasks
- `single_cell_utilities` — Helper functions and scripts for single-cell data handling
- `cpgnet` — CpG S2F analysis

## Manuscripts

See `manuscripts/README.md`. Two iterations:

- `ChromBetaNet` — v1 (thesis, July 2025). Analysis code, figures, and draft.
- `beta_cell_networks` — v2 (in progress). Building on v1 with new integration and analyses.
