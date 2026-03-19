# Computational integration of GWAS, eQTL, and epigenomic data to prioritise regulatory disease loci


Most genetic variants associated with fat distribution lie in non-coding DNA. 
They don't change proteins but they likely change gene regulation. But which 
gene, in which tissue, in which cell type?

This project builds an evidence chain from GWAS signal → regulatory variant 
→ gene expression → cell-type specificity, using only public datasets.

---

## Project structure

This analysis is organised as three linked mini-projects, each answering 
the limitation of the one before it.

| Mini-project | Question | Tools |
|---|---|---|
| 1 — GWAS + epigenomics | Where are the signals and do they sit in regulatory DNA? | GWAS Catalog, ENCODE SCREEN, GTEx |
| 2 — eQTL colocalisation | Are signals mediated by gene expression changes in adipose? | GTEx eQTL, colocalisation logic |
| 3 — Single-cell resolution | Which cell type within adipose is responsible? | CellxGene, Emont et al. 2022 |

---

## Data sources

- **GWAS Catalog** — WHRadjBMI associations, downloaded as .tsv
- **GTEx v8** — tissue-specific expression and eQTL data
- **ENCODE SCREEN** (screen.wenglab.org) — epigenomic annotations, cCREs
- **CellxGene / Emont et al. 2022** — single-cell atlas of human white adipose tissue (166,149 cells)

---

## Key findings

Six loci were prioritised from WHRadjBMI GWAS signals. Each was evaluated 
for adipose expression, epigenomic regulatory support, eQTL colocalisation, 
and cell-type-specific expression.

| Gene | GWAS p | eQTL colocalisation | Primary cell type | Verdict |
|---|---|---|---|---|
| RSPO3 | 2×10⁻²⁹³ | Strong — same SNP, subcutaneous | Preadipocytes | Strong — WNT signalling in progenitors |
| CCDC92 | 2×10⁻⁹⁵ | Strong — same SNP, subcutaneous | Mature adipocytes | Strong — adipogenesis/lipid metabolism |
| COBLL1 | 5×10⁻¹²⁷ | Possible — visceral adipose eQTL | Mature adipocytes | Possible — insulin resistance/lipid metabolism |
| RFLNA | 4×10⁻¹⁰¹ | Complex — colocalises with nerve tibial | Mesothelial cells | Complex — omental mesothelium biology |
| VEGFA | 9×10⁻¹⁴⁰ | Unlikely — signals 630kb apart | Endothelial cells | Unlikely — cell-type dilution in bulk data |
| ZNF664 | 4×10⁻¹⁰¹ | No adipose eQTL detected | Low/diffuse | No adipose signal — multi-tissue regulator |

---

## The main biological findings

**RSPO3** is the strongest result. The lead GWAS SNP rs72959041 turned out to be the same SNP driving expression changes in subcutaneous adipose tissue, and it sits right inside an active enhancer with open chromatin and strong H3K27ac signal. In single-cell data, RSPO3 expression is highest in preadipocytes rather than mature fat cells, which makes biological sense given its role in WNT signalling. Carrying the A allele appears to increase RSPO3 expression in adipose progenitor cells, which could plausibly alter how fat depots develop and expand.

**CCDC92** shows equally clean colocalisation. The same SNP rs7133378 shows up as both the GWAS lead and the top eQTL in subcutaneous adipose (p=1.34×10⁻²⁰), and single-cell data shows expression spread across the mature adipocyte lineage, consistent with its known involvement in adipogenesis and lipid metabolism.

**VEGFA** illustrates the cell-type specificity problem in bulk data. 
 The GWAS signal is extremely strong (p=9×10⁻¹⁴⁰) but the eQTL in adipose is weak and the top eQTL SNP sits over 600kb away from the GWAS lead. The single-cell data explained why. VEGFA is predominantly expressed in endothelial cells, which make up only around 2% of bulk adipose tissue. That small fraction is simply not enough to produce a detectable eQTL signal in bulk GTEx data, which averages across all cell types together.

**RFLNA** is the unexpected finding. 
Rather than colocalising with an adipose eQTL, the GWAS SNP matched a nerve tibial signal. When looking at single-cell data, RFLNA expression turned out to be concentrated in mesothelial cells, which line the omental fat pad. These cells are present in visceral but not subcutaneous adipose, which neatly explains why any regulatory signal at this locus would appear visceral-specific and why bulk adipose eQTL analysis struggled to capture it.

---

## Repository contents
```
data/
  gwas_curated_table.csv       — filtered WHRadjBMI GWAS associations
tables/
  miniproject2_eqtl_summary    — eQTL colocalisation results per gene
  miniproject3_singlecell      — cell-type expression summary
figures/
  (GTEx violin plots, CellxGene UMAPs per gene)
```

---

## What is next

- Formal colocalisation using the `coloc` R package for COBLL1 and RFLNA
- Cell-type eQTL analysis in sorted adipose populations
- Python pipeline to automate GWAS → GTEx → SCREEN annotation
- Mini-project 4: characterising the omental mesothelial cell population 
  across WHR GWAS genes

---
