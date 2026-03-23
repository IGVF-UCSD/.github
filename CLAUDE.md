# stimulated_sc-islets

Research project studying gene regulatory networks in stimulated human pancreatic islets using single-cell multiomics (10X Multiome RNA+ATAC). The work connects gene modules, regulatory elements, and GWAS variants to understand beta cell function and diabetes genetics.

## Directory structure

```
stimulated_sc-islets/
├── stimulated_sc-islet_gene-modules/  # Main analysis pipeline
├── tools/                             # Custom analysis toolkits
├── manuscript/                        # Figures and manuscript analyses
├── igvf-data/                         # IGVF consortium 10X Multiome data
├── public-data/                       # Public reference datasets
├── ref-data/                          # Reference data (external/linked)
├── doc/                               # Grants, presentations, methods docs
└── updates/                           # Meeting notes and analysis reports
```

## Key components

### `stimulated_sc-islet_gene-modules/`
The primary gene set evaluation pipeline. Jupyter notebooks in `src/` run validation and enrichment analyses on gene modules derived from the single-cell data. Uses Nextflow for pipeline orchestration (run records in `runs/`). Input gene sets live in `inputs/` (versioned v1, v2). Results land in `outputs/`. Has its own virtual environment (`.venv/`) managed with `uv`; dependencies are in `pyproject.toml` and include the local `gsa` package from `tools/gene-set-analysis`.

### `tools/gene-set-analysis/`
Custom Python package (`gsa`) for annotating gene sets. Provides:
- Over-Representation Analysis (ORA) and GSEA
- Transcription factor motif enrichment (FIMO/JASPAR)
- GWAS enrichment (MAGMA integration)
- LLM-assisted report generation
- Nextflow pipeline (`nextflow/main.nf`) with multiple entry points
- Reference data in `data/` (gene sets, genome annotations, motif DBs, ID mappings)
- Examples in `examples/`

Has its own `pyproject.toml`, `Dockerfile`, test suite, and docs.

### `tools/CellCommander/`
CLI tool for single-cell analysis automation. Modules cover QC, doublet detection, normalization, feature selection, dimensionality reduction, annotation, and integration. Supports both RNA and ATAC modalities.

### `manuscript/`
- `beta_cell_networks/` — Primary analysis workspace with browser session notebooks (ATAC peaks/arcs, cell-type networks, condition comparisons) and integration benchmarking work in `scratch/`
- `chrombetanet/` — ChromBetaNet analysis with islet marker tables
- `fig1/` through `fig5/` — Adobe Illustrator figure files
- `old_figs/` — Archived previous versions

### `igvf-data/`
IGVF consortium 10X Multiome (scRNA-seq + scATAC-seq) data in `igvf_sc-islet_10X-Multiome/`.

### `updates/`
Chronological analysis reports and meeting materials. Notable entries:
- `2025_12_09_cRE_prioritization/` — cis-regulatory element prioritization
- `2026_02_01_chr14_analysis_report/` — Detailed chr14 locus analysis
- `2026_02_05_meeting/` — Latest meeting notes

### `doc/`
Grant documents (NIH E-grant PDF), annual meeting materials, DACC docs, presentations, and a methods write-up.

## Technical notes

- **Python environments**: managed with `uv`. The gene-modules pipeline installs `gsa` as a local editable dependency.
- **Nextflow**: used to orchestrate the gene set analysis pipeline. Configs support Docker, Singularity, and Conda profiles.
- **Notebooks**: 58 Jupyter notebooks across the project are the primary interactive analysis interface.
- **R code**: some R scripts exist in `manuscript/beta_cell_networks/scratch/` for Seurat/Harmony integration comparisons.
- **Not a git repo** at the top level. Individual subdirectories (e.g., `tools/gene-set-analysis`, `tools/CellCommander`) may have their own git history.
