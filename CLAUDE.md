# stimulated_sc-islets

Research project studying gene regulatory networks in stimulated human pancreatic islets using single-cell multiomics. The work connects gene modules, regulatory elements, and GWAS variants to understand beta cell function and diabetes genetics.

## Directory structure

```
stimulated_sc-islets/              ← .github repo (tracks org-level docs only)
├── profile/README.md              # GitHub org landing page
├── CLAUDE.md                      # This file — codebase guide
├── DATA.md                        # Data versioning (v1/v2/v3)
├── PIPELINES.md                   # Processing pipeline docs
├── EXPERIMENTAL_DESIGN.md         # Experimental design details
├── INTEGRATIVE_ANALYSES.md        # Cross-dataset integration notes
├── OVERVIEW.md                    # Project overview
├── TODO.md                        # High-level TODOs (also tracked as GitHub Issues)
├── hpc_setup.md                   # HPC reorganization instructions
│
├── igvf-data/                     # IGVF consortium generated data
│   ├── igvf_sc-islet_10X-Multiome/   # 10X Multiome (RNA + ATAC)
│   ├── igvf_sc-islet_snm3c/          # snm3C-seq (methylation + 3C)
│   ├── igvf-sc-islet-gsis/           # GSIS functional data (placeholder)
│   ├── igvf_portal_patches.md        # Portal submission guide + patches
│   └── README.md
│
├── public-data/                   # Published reference datasets (each a git repo)
│   ├── Augsornworawat2023_sc-islet_10X-Multiome/  # SC-islet 10X Multiome
│   ├── Chiou2021_islet_snATAC-seq/                 # Primary islet snATAC-seq
│   ├── Dominguez2020_sc-islet_bulk/                # SC-islet bulk RNA/ATAC
│   ├── EndoC-bH1_treated_ATAC-seq/                 # EndoC-bH1 treated ATAC-seq
│   ├── HPAP/                                       # Human Pancreas Analysis Program
│   ├── islet_10X-Multiome/                         # Primary islet 10X Multiome
│   ├── Maestas2024_islet_10X-Multiome/             # Primary islet 10X Multiome
│   ├── mo_EndoC-bH1_ATAC-seq/                      # EndoC-bH1 bulk ATAC-seq
│   ├── Wang2023_islet_snATAC-seq/                   # Primary islet snATAC-seq
│   ├── Zhu2023_sc-islet_scRNA-seq/                  # SC-islet scRNA-seq (differentiation)
│   └── Zhu2023_sc-islet_snATAC-seq/                 # SC-islet snATAC-seq (differentiation)
│
├── gene-networks/                 # Gene network method evaluation
│   ├── inputs/                    # Versioned gene sets
│   ├── outputs/                   # Analysis results
│   ├── runs/                      # Nextflow pipeline run records
│   ├── src/                       # Analysis notebooks
│   └── gsa_use_cases.md           # GSA pipeline use cases
│
├── pipelines/                     # Multiome processing workflows (each a git repo)
│   ├── multiome-sample-qc/        # Sample QC and processing
│   ├── multiome-cell-annotation/  # Cell annotation
│   └── multiome-peak-analysis/    # Pseudobulk peak calling
│
├── tools/                         # Reusable packages (each a git repo)
│   ├── gene-set-analysis/         # ORA, GSEA, motif, GWAS enrichment pipeline
│   ├── CellCommander/             # CLI for single-cell analysis tasks
│   ├── single_cell_utilities/     # Helper functions for single-cell data
│   └── cpgnet/                    # CpG network / S2F analysis
│
├── manuscripts/                   # Manuscript iterations (each a git repo)
│   ├── ChromBetaNet/              # v1 (thesis, July 2025) — analysis code + figures
│   └── beta_cell_networks/        # v2 (in progress) — new integration + analyses
│
├── doc/                           # Grants, presentations, protocols, references
│   ├── annual_meeting/
│   ├── dacc/
│   ├── presentations/
│   ├── slides/
│   └── references.md              # Key paper bibliography
│
├── ref-data/                      # Shared reference data
└── updates/                       # Chronological analysis reports and meeting notes
```

## GitHub organization

All repos live under [IGVF-UCSD](https://github.com/IGVF-UCSD). Repos are tagged with topics for filtering:
- `igvf-data` — IGVF-generated datasets
- `public-data` — Published reference datasets
- `pipeline` — Processing workflows
- `tools` — Reusable packages
- `manuscript` — Manuscript repos

Tasks tracked as [GitHub Issues](https://github.com/IGVF-UCSD/.github/issues) on the [project board](https://github.com/orgs/IGVF-UCSD/projects/4).

## Technical notes

- **Version control**: Top level is the IGVF-UCSD/.github repo (tracks org docs only). Subdirectories have their own git repos. The `.gitignore` ignores all subdirectories.
- **Python environments**: Managed with `uv`. The gene-networks pipeline installs `gsa` as a local editable dependency.
- **Nextflow**: Orchestrates the gene set analysis pipeline. Configs support Docker, Singularity, and Conda profiles.
- **Notebooks**: Jupyter notebooks are the primary interactive analysis interface across the project.
- **R code**: R scripts in `manuscripts/beta_cell_networks/scratch/` for Seurat/Harmony integration.
- **HPC**: nrnb-login.ucsd.edu. Data lives in `/cellar/users/aklie/data/datasets/` and is symlinked into the project. See `hpc_setup.md` for reorganization instructions.
- **IGVF Portal**: Submission scripts in `igvf-data/igvf_sc-islet_10X-Multiome/bin/10_submission/`. See `igvf-data/igvf_portal_patches.md` for the full guide.
