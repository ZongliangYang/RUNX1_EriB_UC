# RUNX1_EriB_UC

Analysis code and underlying data for:

> Zongliang Yang et al. *Integrative single-cell and machine learning analysis defines a RUNX1-centered regulatory network in ulcerative colitis*. PLOS ONE, PONE-D-26-40968.

This repository accompanies the manuscript and satisfies the PLOS ONE **Materials, Software and Code Sharing** policy. All analyses are computational; **no wet-laboratory experiments were performed**.

---

## Files for the revised submission

- [data.zip](data.zip) includes the submission workbook `S1_Underlying_data_revised.xlsx`, sample and expression tables, fitted model and saved scores, the patient-adjusted bulk differential-expression results, and the selected RUNX1 contrast and enrichment outputs.
- [code.zip](code.zip) includes the original analysis scripts and two additional scripts in `revision_checks/` for verifying the revised results.
- [MANIFEST_sources.txt](MANIFEST_sources.txt) lists the archive members, their provenance and their SHA-256 checksums.

The 15 fitted predictors include **NNMT**. RUNX1 was examined separately in the single-cell analyses. `Signature_expression.csv` contains 2,085 values for 15 predictors across 139 biopsies. `Sample_manifest.csv` records the GEO phenotype and historical matrix label for each biopsy. `ROC_with_GEO_labels.csv` retains original scores and makes both sets of labels available.

| Cohort | Samples and role | Revised RF AUC | Revised XGBoost AUC |
|---|---|---:|---:|
| GSE107597 | 119 training biopsies from 13 participants with UC, 75 lesional and 44 non-lesional | — | — |
| GSE96698 | 14 biopsies from two participants, eight active and six inactive | 0.552 | 0.542 |
| GSE134025 | Six samples, three UC and three healthy | 1.000 | 1.000 |

The archived GSE96698 labels tracked biopsy site. The archived GSE134025 labels were reversed relative to GEO diagnosis. The revised AUCs use the saved scores with a fixed direction and the GEO phenotypes. The model fits are unchanged. `Corrected_AUC.csv` contains unrounded values. Historical scores and summary output are retained for provenance. In particular, `archive/ML_final.txt` uses old endpoints and automatic ROC direction.

`Bulk_DEG_patient_adjusted.csv` supplies the revised Fig 1E/F results. It derives from a model with patient fixed effects in the 119 training biopsies and was computed separately from the archived classifier. `archive/DEG_results.txt` is the earlier output. `RUNX1_contrast_deg.csv` contains the 7,096 fibroblast contrast genes meeting BH-adjusted P < 0.01 and absolute log2 fold change > 0.5. It replaces the earlier pseudobulk export. `KO_enrichment.rds`, the GO and Reactome tables, and their background lists document the corresponding enrichment.

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

## Verify the revised results

Extract `data.zip` into a folder called `data` and `code.zip` into a sibling folder called `code`. Run these commands from inside `data` using R and the limma package. The first command reads the archived model scores and verifies the corrected AUCs and the selected RUNX1 contrasts. The second regenerates `Bulk_DEG_patient_adjusted.csv`.

```sh
Rscript ../code/revision_checks/Verify_model_and_contrast.R
Rscript ../code/revision_checks/Recalculate_bulk_DEG.R
```

For the historical workflows, directories `01` through `10` in the code archive follow the analysis order from bulk preprocessing and machine learning through single-cell analysis and docking. The historical ML code reflects the original matrix labels. Use the revision checks above to reproduce the final reported AUCs.

## Known data-provenance notes

These notes are provided for transparency; see also the Analysis_notes sheet of the S1 workbook.

1. **15-gene signature**: the fitted model, its RF feature names, and the revised S1 all include **NNMT** among the 15 predictors.
2. **GSE107597 gene matrix**: the symbol-collapsed `GSE107597.normalize.txt` lacks a RUNX1 row (RUNX1 probes 11732933_a_at / 11742191_a_at / 11745150_x_at are present in the probe annotation but were dropped during probe→symbol collapse). GSE96698 and GSE134025 retain RUNX1.
3. **PDB ID**: docking used **1CMO** (not 1EAQ, which appeared in an older draft).
4. **EriB formula**: PubChem CID 16202215 is **C20H24O5** (MW 344.4).

## License

Code is released under the MIT License (see `LICENSE`). Downstream GEO datasets retain their original terms of use.

## Citation

If you use this code or data, please cite the PLOS ONE article (DOI to be added upon publication).
