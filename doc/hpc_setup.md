# HPC Reorganization for stimulated_sc-islets

Reorganize existing HPC directories into a unified project structure. Data repos stay in place and get symlinked. Project/tool/pipeline repos get moved. Anything missing gets cloned.

## Target structure

```
stimulated_sc-islets/
├── doc/                                           # [create] Non-repo scaffolding
├── igvf-data/
│   ├── igvf_sc-islet_10X-Multiome/                # [symlink] from datasets
│   ├── igvf_sc-islet_snm3c/                       # [symlink] from datasets
│   └── igvf-sc-islet-gsis/                        # [create] placeholder
├── public-data/                                   # All symlinked from datasets/
│   ├── Augsornworawat2023_sc-islet_10X-Multiome/  # [symlink]
│   ├── Chiou2021_islet_snATAC-seq/                 # [symlink] (clone to datasets/ if missing)
│   ├── Dominguez2020_sc-islet_bulk/                # [symlink] (clone to datasets/ if missing)
│   ├── EndoC-bH1_treated_ATAC-seq/                 # [symlink] (clone to datasets/ if missing)
│   ├── HPAP/                                       # [symlink] (clone to datasets/ if missing)
│   ├── islet_10X-Multiome/                         # [symlink]
│   ├── Maestas2024_islet_10X-Multiome/             # [symlink]
│   ├── mo_EndoC-bH1_ATAC-seq/                      # [symlink] (clone to datasets/ if missing)
│   ├── Wang2023_islet_snATAC-seq/                   # [symlink] (clone to datasets/ if missing)
│   ├── Zhu2023_sc-islet_scRNA-seq/                  # [symlink] (clone to datasets/ if missing)
│   └── Zhu2023_sc-islet_snATAC-seq/                 # [symlink] (clone to datasets/ if missing)
├── gene-networks/                                  # [create] non-repo scaffolding
│   ├── inputs/
│   ├── outputs/
│   ├── runs/
│   └── src/
├── pipelines/
│   ├── multiome-sample-qc/                         # [move] from projects/igvf/
│   ├── multiome-cell-annotation/                   # [move] from projects/igvf/
│   └── multiome-peak-analysis/                     # [move] from projects/igvf/
├── tools/
│   ├── gene-set-analysis/                          # [move] from projects/igvf/
│   ├── CellCommander/                              # [move] from projects/igvf/
│   ├── single_cell_utilities/                      # [move] from projects/igvf/
│   └── cpgnet/                                     # [move] from projects/ML4GLand/
├── manuscripts/
│   ├── ChromBetaNet/                               # [move] from projects/igvf/
│   └── beta_cell_networks/                         # [move] from projects/igvf/
├── ref-data/                                       # [create] placeholder
├── updates/                                        # [create] placeholder
└── README.md                                       # [create] copy from local
```

## Known current locations on HPC

```
# projects/igvf/  (move these)
/cellar/users/aklie/projects/igvf/beta_cell_networks
/cellar/users/aklie/projects/igvf/ChromBetaNet
/cellar/users/aklie/projects/igvf/single_cell_utilities
/cellar/users/aklie/projects/igvf/multiome-sample-qc
/cellar/users/aklie/projects/igvf/multiome-peak-analysis
/cellar/users/aklie/projects/igvf/multiome-cell-annotation
/cellar/users/aklie/projects/igvf/gene-set-analysis
/cellar/users/aklie/projects/igvf/CellCommander

# projects/ML4GLand/  (move this)
/cellar/users/aklie/projects/ML4GLand/cpgnet

# data/datasets/  (symlink these — data stays in place)
/cellar/users/aklie/data/datasets/igvf_sc-islet_10X-Multiome
/cellar/users/aklie/data/datasets/igvf_sc-islet_snm3c
/cellar/users/aklie/data/datasets/Augsornworawat2023_sc-islet_10X-Multiome
/cellar/users/aklie/data/datasets/islet_10X-Multiome
/cellar/users/aklie/data/datasets/Maestas2024_islet_10X-Multiome
```

## Step 1: Create the base directory and scaffolding

```bash
BASE="/cellar/users/aklie/projects/igvf/stimulated_sc-islets"
mkdir -p "$BASE"
cd "$BASE"

mkdir -p doc igvf-data/igvf-sc-islet-gsis public-data
mkdir -p gene-networks/inputs gene-networks/outputs gene-networks/runs gene-networks/src
mkdir -p pipelines tools manuscripts
mkdir -p ref-data updates
```

## Step 2: Symlink data repos (data stays in place)

All data repos should live in `/cellar/users/aklie/data/datasets/`. Symlink each one into the project structure. For each repo below, first check if it exists in `datasets/` — if it does, symlink it. If not, clone it into `datasets/` first, then symlink.

```bash
DATASETS="/cellar/users/aklie/data/datasets"

# Helper: symlink if exists in datasets/, otherwise clone there first then symlink
symlink_or_clone() {
  local name="$1" target_dir="$2" clone_url="$3"
  if [ -d "$DATASETS/$name" ]; then
    echo "SYMLINK: $name (exists in datasets/)"
    ln -s "$DATASETS/$name" "$target_dir/$name"
  else
    echo "CLONE+SYMLINK: $name (not in datasets/, cloning)"
    git clone "$clone_url" "$DATASETS/$name"
    ln -s "$DATASETS/$name" "$target_dir/$name"
  fi
}

# IGVF data
symlink_or_clone igvf_sc-islet_10X-Multiome igvf-data https://github.com/IGVF-UCSD/igvf_sc-islet_10X-Multiome.git
symlink_or_clone igvf_sc-islet_snm3c igvf-data https://github.com/IGVF-UCSD/igvf_sc-islet_snm3c.git

# Public data
symlink_or_clone Dominguez2020_sc-islet_bulk public-data https://github.com/IGVF-UCSD/Dominguez2020_sc-islet_bulk.git
symlink_or_clone Zhu2023_sc-islet_scRNA-seq public-data https://github.com/IGVF-UCSD/Zhu2023_sc-islet_scRNA-seq.git
symlink_or_clone Zhu2023_sc-islet_snATAC-seq public-data https://github.com/IGVF-UCSD/Zhu2023_sc-islet_snATAC-seq.git
symlink_or_clone Augsornworawat2023_sc-islet_10X-Multiome public-data https://github.com/IGVF-UCSD/Augsornworawat2023_sc-islet_10X-Multiome.git
symlink_or_clone Chiou2021_islet_snATAC-seq public-data https://github.com/IGVF-UCSD/Chiou2021_islet_snATAC-seq.git
symlink_or_clone Wang2023_islet_snATAC-seq public-data https://github.com/IGVF-UCSD/Wang2023_islet_snATAC-seq.git
symlink_or_clone islet_10X-Multiome public-data https://github.com/IGVF-UCSD/islet_10X-Multiome.git
symlink_or_clone Maestas2024_islet_10X-Multiome public-data https://github.com/IGVF-UCSD/Maestas2024_islet_10X-Multiome.git
symlink_or_clone HPAP public-data https://github.com/IGVF-UCSD/HPAP.git
symlink_or_clone EndoC-bH1_treated_ATAC-seq public-data https://github.com/IGVF-UCSD/EndoC-bH1_treated_ATAC-seq.git
symlink_or_clone mo_EndoC-bH1_ATAC-seq public-data https://github.com/IGVF-UCSD/mo_EndoC-bH1_ATAC-seq.git
```

## Step 3: Move project repos into new structure

```bash
IGVF="/cellar/users/aklie/projects/igvf"

# Pipelines
mv "$IGVF/multiome-sample-qc" pipelines/
mv "$IGVF/multiome-cell-annotation" pipelines/
mv "$IGVF/multiome-peak-analysis" pipelines/

# Tools
mv "$IGVF/gene-set-analysis" tools/
mv "$IGVF/CellCommander" tools/
mv "$IGVF/single_cell_utilities" tools/
mv /cellar/users/aklie/projects/ML4GLand/cpgnet tools/

# Manuscripts
mv "$IGVF/ChromBetaNet" manuscripts/
mv "$IGVF/beta_cell_networks" manuscripts/
```

## Step 4: Verify

```bash
# Check symlinks resolve
ls -la igvf-data/ public-data/

# Check moved repos have .git
for d in pipelines/* tools/* manuscripts/*; do
  [ -d "$d/.git" ] && echo "OK: $d" || echo "MISSING GIT: $d"
done

# Check nothing was left behind
ls "$IGVF"/{multiome-sample-qc,multiome-cell-annotation,multiome-peak-analysis,gene-set-analysis,CellCommander,single_cell_utilities,ChromBetaNet,beta_cell_networks} 2>&1
# Should all say "No such file or directory"
```

## Notes

- **All data repos live in `/cellar/users/aklie/data/datasets/`** and are symlinked into the project. If a repo isn't there yet, clone it to `datasets/` first, then symlink. Never clone data repos directly into the project tree.
- **Project repos are moved**, not symlinked. Consider leaving a symlink at the old location if other scripts reference the old path: `ln -s "$BASE/tools/gene-set-analysis" "$IGVF/gene-set-analysis"`
- Before running, check `ls /cellar/users/aklie/data/datasets/` for any additional repos that should be symlinked.
