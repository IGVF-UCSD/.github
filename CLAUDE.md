# stimulated_sc-islets

Research project studying gene regulatory networks in stimulated human stem-cell-derived islets (SC-islets) using single-cell multiomics (10X Multiome RNA+ATAC and snm3C-seq). The work connects gene modules, regulatory elements, sequence models, and GWAS variants to understand beta cell function and diabetes genetics. Part of the IGVF consortium.

## Workflow

- **Task tracking:** GitHub Issues in `IGVF-UCSD/.github` (#1–#16), organized on [project board](https://github.com/orgs/IGVF-UCSD/projects/4). See `profile/README.md` for the dependency graph.
- **Daily tasks:** `TODAY.md` at the project root is a scratchpad for daily progress. Update it as work progresses and sync findings back to the relevant GitHub issue thread.
- **Methods:** [doc/METHODS.md](doc/METHODS.md) is the paper-style methods reference — consult it for design rationale and parameter choices before explaining or modifying a pipeline step, and keep it current when methodology changes.
- **Exploratory work** goes in date-stamped `scratch/` subdirectories. Pipeline/production code goes in `bin/` or `src/`.
- **Scratch locations** (there is NO top-level `scratch/` — always use the full path):
  - `igvf-data/igvf_sc-islet_10X-Multiome/scratch/YYYY_MM_DD/` — primary multiome analysis scratch (v1/v2/v3 workspaces all live here)
  - `manuscripts/beta_cell_networks/scratch/` and `manuscripts/ChromBetaNet/scratch/` — manuscript-specific exploration
  - `pipelines/multiome-cell-annotation/scratch/`, `pipelines/multiome-sample-qc/src/scratch/` — pipeline dev scratch

## Directory structure

```
stimulated_sc-islets/
├── profile/                # Project README with dependency graph and experimental design
├── TODAY.md                # Daily task tracker (scratchpad)
├── gene-networks/          # Gene module analysis pipeline (inputs/, src/, outputs/, runs/)
├── pipelines/              # Modular single-cell pipelines
│   ├── multiome-sample-qc/     # Sample-level QC
│   ├── multiome-cell-annotation/  # Cell type annotation
│   └── multiome-peak-analysis/    # Peak calling and analysis
├── tools/                  # Custom analysis toolkits
│   ├── gene-set-analysis/      # GSA package (ORA, GSEA, motifs, MAGMA, LLM reports)
│   ├── CellCommander/          # CLI for single-cell analysis automation
│   ├── chrombpnet/             # ChromBPNet sequence modeling
│   ├── cpgnet/                 # CpGNet methylation models
│   ├── crested/                # CRESTED regulatory element modeling
│   ├── deeptopic/              # DeepTopic topic modeling
│   └── single_cell_utilities/  # Shared single-cell utility functions
├── manuscripts/            # Manuscript figures and analyses
│   ├── beta_cell_networks/     # Primary analysis workspace
│   └── ChromBetaNet/           # ChromBetaNet analysis
├── igvf-data/              # IGVF consortium data
│   ├── igvf_sc-islet_10X-Multiome/  # 10X Multiome (RNA+ATAC)
│   ├── igvf_sc-islet_snm3c/         # snm3C-seq (methylation+3C)
│   ├── igvf-sc-islet-gsis/          # GSIS functional data
│   ├── PORTAL.md                    # IGVF portal submission requirements
│   └── SUBMISSION.md                # Submission tracking
├── public-data/            # Public reference datasets
├── ref-data/               # Reference data (external/linked)
├── doc/                    # Documentation hub
│   ├── OVERVIEW.md             # Project overview and analysis versions (v1/v2/v3)
│   ├── METHODS.md              # Paper-grade methods section (cite this for reproducibility-level detail)
│   ├── EXPERIMENTAL_DESIGN.md  # Conditions, timepoints, replicates
│   ├── PIPELINES.md            # Pipeline version comparison and presentation links
│   ├── INTEGRATIVE_ANALYSES.md # Cross-modality analysis plans
│   ├── DATA.md                 # Data locations and descriptions
│   ├── SLURM.md                # Cluster job submission reference
│   └── LINKS.md, TODO.md, references.md
└── updates/                # Meeting notes and analysis reports
```

## Analysis versions

- **v3 (current, as of 2026-04-28):** IGVF uniform pipeline (kallisto-bustools + chromap), 54 samples. Code in `igvf-data/.../bin/{1_get_data,2_sample_qc,3_cell_annotation}/`, outputs in `results/{1_get_data,2_sample_qc,3_cell_annotation}/`. Stages 1–3 migrated; stages 4–10 still need to be ported from v2 — see `igvf-data/.../bin/README.md`.
- **v2 (archived):** CellRanger-ARC + YAP, 54 samples. Frozen 2026-04-28. Code at `igvf-data/.../archive/v2/bin/`, outputs at `archive/v2/results/`. Manifest: `archive/v2/MANIFEST.md`.
- **v1 (archived):** CellRanger-ARC + YAP, 44 samples. 3.0 TB. Code at `igvf-data/.../archive/v1/bin/`, results at `igvf-data/.../archive/v1/results/` (moved from `scratch/2026_01_30/` on 2026-04-29; same filesystem rename). Manifest: `archive/v1/MANIFEST.md`. Supplementary: `scratch/2026_02_02/` (182 MB, refined peak-calling notebooks + v1 DESeq2).

## Technical notes

- **Environments**: see [doc/ENVS.md](doc/ENVS.md) for the full inventory of conda envs and uv venvs (which script uses which env, and common gotchas like `source activate` silently falling back to `/usr/bin/python`).
- **Python environments**: `gene-networks/` is managed with `uv` and installs `gsa` as a local editable dependency. Most pipeline scripts use conda envs under `/cellar/users/aklie/opt/miniconda3/envs/`.
- **Nextflow**: used to orchestrate the gene set analysis pipeline. Configs support Docker, Singularity, and Conda profiles.
- **Notebooks**: Jupyter notebooks across the project are the primary interactive analysis interface.
- **R code**: some R scripts in `manuscripts/beta_cell_networks/scratch/` for Seurat/Harmony integration comparisons.
- **Cluster**: SLURM-based HPC. Must run `module load git` before git commands.
- **Git**: top-level repo tracks project files. Individual subdirectories (e.g., `tools/gene-set-analysis`, `tools/CellCommander`) may have their own git history.
