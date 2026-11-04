# RUNX1_EriB_UC

Analysis code and underlying data for:

> Zongliang Yang et al. *Integrative single-cell and machine learning analysis of a RUNX1-centered regulatory network in ulcerative colitis: a hypothesis-generating computational study*. (PLOS ONE, PONE-D-26-40968)

This repository accompanies the manuscript and satisfies the PLOS ONE **Materials, Software and Code Sharing** policy. All analyses are computational; **no wet-laboratory experiments were performed**.

---

## Repository layout

```
code/
  01_bulk_normalize_DEG/     bulk normalization, ComBat, limma DEG
  02_machine_learning/       LASSO / Random Forest / XGBoost signature
  03_single_cell/            Seurat QC, Harmony, cell-type annotation
  04_TF_activity/            pySCENIC + decoupleR TF activity
  05_CellChat/               CellChat cell–cell communication
  06_pseudotime_Monocle3/    Monocle3 trajectories
  07_fibroblast_subtypes/    fibroblast subclustering
  08_RUNX1_contrast/         RUNX1 virtual-KO / pseudobulk contrast, scTenifoldKnk
  09_pathway_enrichment/     GSEA / Reactome, GRN
  10_docking_MD/             AutoDock Vina config + output, MD plotting, EriB SDF, RUNX1 1CMO
data/
  S1Underlying_data_...xlsx  minimal underlying dataset (same file as S1 Supporting Information)
  DEG_results.txt            bulk DEG (training cohort)
  LASSO_genes.txt            LASSO-selected genes (raw output)
  ML_final.txt               RF importance + external-validation AUC
  TF_activity_decoupleR.csv  TF activity scores
  RUNX1_contrast_deg.csv     RUNX1-high vs RUNX1-low contrast
  scRNA_DEG/                 per-cell-type differential expression
```

## Data sources (public)

| Dataset | Role | Accession |
|---|---|---|
| Bulk training cohort | DEG + ML | GEO [GSE107597](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE107597) |
| Bulk validation 1 | external validation | GEO [GSE96698](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE96698) |
| Bulk validation 2 | external validation | GEO [GSE134025](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE134025) |
| Single-cell / snRNA | organoids + parental fibroblasts | GEO [GSE152999](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE152999) |

The single-cell dataset is an **organoid–fibroblast model system**, not directly sampled UC patient mucosa.


## Path configuration (required before running)

The scripts were written for a Windows workstation and contain **absolute working-directory
calls** (`setwd(...)`, `workDir <- ...`, `OUT_DIR <- ...`) pointing to the original machine
(e.g. `C:/Users/.../Desktop/溃疡/...`). Before running, edit these lines at the top of each
script so they point to your local clone / data folders. No other changes are required for
the analysis logic. Raw GEO inputs are not redistributed here — download them from GEO
(see table above) and point the scripts at your local copies.

## Key molecular entities

| Item | Value |
|---|---|
| RUNX1 Runt domain (docking receptor) | PDB **1CMO**, residues **52–178**, chain A |
| Eriocalyxin B | PubChem CID **16202215**, C20H24O5, MW 344.4 |
| EriB canonical isomeric SMILES | `CC1(C=CC(=O)[C@]23[C@@H]1[C@@H]([C@]([C@]45[C@H]2CC[C@H](C4)C(=C)C5=O)(OC3)O)O)C` |
| EriB InChIKey | `RTIKCEKESDRWAE-UJVKWQRCSA-N` |
| Top Vina pose | −6.8 kcal/mol (exhaustiveness 32; 40 Å cube) |
| MD | GROMACS 2020.6, CHARMM36 (March 2021), 100 ns |

## Software versions

- R ≥ 4.x with: limma, sva, glmnet, randomForest, xgboost, pROC, rms, Seurat, harmony, SingleR, decoupleR, AUCell, CellChat (v2.1.0), monocle3 (v1.4.27), fgsea/ReactomePA
- Python 3.x with: pyscenic (v0.12.1), pandas, numpy, matplotlib
- AutoDock Vina v1.2.0
- GROMACS 2020.6

## How to reproduce (overview)

1. **Bulk**: download the three GEO series matrices → `code/01_bulk_normalize_DEG/01_normalize.R` → `02_sva_combat.R` → `03_limma_DEG.R` (thresholds: BH-adj. P < 0.05 and |log2FC| > 0.5).
2. **Machine learning**: `code/02_machine_learning/01_RUNX1_ML_signature_v2.R` (LASSO → RF/XGBoost → external validation).
3. **Single-cell**: `code/03_single_cell/` on GSE152999 (Seurat → Harmony → annotation).
4. **TF activity**: `code/04_TF_activity/` (pySCENIC, then decoupleR/CollecTRI).
5. **CellChat / Monocle3 / fibroblast subtypes / RUNX1 contrast**: `code/05`–`code/08`.
6. **Docking / MD**: `code/10_docking_MD/` (Vina config file, ligand SDF, receptor 1CMO; MD analysis scripts).

Exact parameters are reported in the manuscript Methods.

## Known data-provenance notes

These notes are provided for transparency; see also the README sheet of the S1 workbook.

1. **15-gene signature**: `data/LASSO_genes.txt` and `data/ML_final.txt` list **NNMT**; the manuscript reports **RUNX1** (force-inserted by `code/02_machine_learning/01_RUNX1_ML_signature_v2.R`). Both lists are preserved as generated.
2. **GSE107597 gene matrix**: the symbol-collapsed `GSE107597.normalize.txt` lacks a RUNX1 row (RUNX1 probes 11732933_a_at / 11742191_a_at / 11745150_x_at are present in the probe annotation but were dropped during probe→symbol collapse). GSE96698 and GSE134025 retain RUNX1.
3. **PDB ID**: docking used **1CMO** (not 1EAQ, which appeared in an older draft).
4. **EriB formula**: PubChem CID 16202215 is **C20H24O5** (MW 344.4).

## License

Code is released under the MIT License (see `LICENSE`). Downstream GEO datasets retain their original terms of use.

## Citation

If you use this code or data, please cite the PLOS ONE article (DOI to be added upon publication).
