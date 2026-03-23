# The impact of genomic variation on environment-induced changes in pancreatic beta cell states

## Goal

Characterize the transcriptomic and epigenomic responses of stem-cell-derived
islet (SC-islet) cell types to metabolic and inflammatory stressors using
10X Multiome (RNA + ATAC) and snm3C-seq (methylation + chromatin conformation).

---

## Active Projects

### [Stimulated SC-Islets](https://github.com/orgs/IGVF-UCSD/projects/4)

Multi-omic characterization of SC-islet responses to metabolic and inflammatory stress. Tasks are tracked as [GitHub Issues](https://github.com/IGVF-UCSD/.github/issues) and organized on the [project board](https://github.com/orgs/IGVF-UCSD/projects/4).

**Looking to contribute?** Start with unblocked tasks, then work down the dependency graph:

```
No blockers (start here):
  #3  INS secretion score
  #4  DESeq2 pseudobulk
  #5  Peak calling
  #11 Reprocess snm3C-seq

Layer 2:
  #1  ChromBPNet ← #5
  #2  Peak-to-gene links ← #4, #5
  #6  Upload v2 to GCP ← #4, #5
  #7  Gene modules ← #3, #4
  #8  Begin v3 ← #4
  #12 snm3C × Dominguez2020 ← #11
  #14 CpGNet retraining ← #11

Layer 3:
  #9  Integration × Augsornworawat2023 ← #1, #2
  #10 Integration × primary islet ← #1, #2, #4
  #13 Base-GRNs ← #1
  #15 Variant effect predictions ← #1, #2, #5, #14

Final:
  #16 Prioritize cREs for CRISPRi ← #1, #2, #7, #13, #15
```

<!-- Add new projects below this line -->

---

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

## IGVF data

### 10x multiome

Joint scRNA-seq + scATAC-seq from SC-islets under stimulation conditions. Detailed instructions in `igvf-data/igvf_sc-islet_10X-Multiome`. Processing steps: data download → Cell Ranger ARC → sample QC → integration → peak calling → differential analysis.

### snm3c-seq

Single-nucleus methyl-3C (joint methylation + chromatin conformation) from SC-islets. Detailed instructions in `igvf-data/igvf_sc-islet_snm3c`. Processing steps: data download → mapping → methscan → allcools → hicluster → pseudobulk → cpgnet.

### GSIS functional data

Glucose-stimulated insulin secretion data from SC-islets. Currently a placeholder with methods doc in `igvf-data/igvf-sc-islet-gsis/`.
