# The impact of genomic variation on environment-induced changes in pancreatic beta cell states

## Goal

Characterize the transcriptomic and epigenomic responses of stem-cell-derived
islet (SC-islet) cell types to metabolic and inflammatory stressors using
10X Multiome (RNA + ATAC) and snm3C-seq (methylation + chromatin conformation).

## Experimental Design

### Organoid differentiation and stimulation

Differentation protocol: doc/dacc/protocols/Pancreatic_beta_cell_diff_v2_Oct2022_CMP05979.pdf

### Sample tracker

[Google Sheets — Sample tracker](https://docs.google.com/spreadsheets/d/1fvjK-2oZLOi-dFaP15h7vXcPuoPEkTjN_Ft0xI0N6r8/edit#gid=1331888441)

### Cell lines

| Cell line | Notes |
|-----------|-------|
| A2 | CRISPR modified H1-hESC line |

### Conditions

| Condition | Description |
|-----------|-------------|
| control | Untreated |
| 3-cyt | Three-cytokine cocktail (inflammatory) |
| IFNg | Interferon gamma (inflammatory) |
| Ex-4_HG | Exendin-4 + high glucose (metabolic) |
| palmitate | Palmitate (metabolic/lipotoxic) |
| dex | Dexamethasone (glucocorticoid) |

### Timepoints

0, 6, 24, 48, 72 hours

### Replicates

Targeting 2 biological replicates per condition-timepoint, with up to 3 total replicates for controls.

_Coming soon_

## IGVF data

### 10x multiome

Joint scRNA-seq + scATAC-seq from SC-islets under stimulation conditions. Detailed instructions in `igvf-data/igvf_sc-islet_10X-Multiome`. Processing steps: data download → Cell Ranger ARC → sample QC → integration → peak calling → differential analysis.

### snm3c-seq

Single-nucleus methyl-3C (joint methylation + chromatin conformation) from SC-islets. Detailed instructions in `igvf-data/igvf_sc-islet_snm3c`. Processing steps: data download → mapping → methscan → allcools → hicluster → pseudobulk → cpgnet.

### GSIS functional data

Glucose-stimulated insulin secretion data from SC-islets. Currently a placeholder with methods doc in `igvf-data/igvf-sc-islet-gsis/`.
