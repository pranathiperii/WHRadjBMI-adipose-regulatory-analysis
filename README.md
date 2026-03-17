# Computational integration of GWAS, eQTL, and epigenomic data to prioritise regulatory disease loci

**Status:** Ongoing  
**Trait:** BMI-adjusted waist-hip ratio (WHRadjBMI)  
**Focus:** Adipose tissue — subcutaneous and visceral  

---

## What question am I asking?

Most genetic variants associated with fat distribution lie in non-coding DNA. 
They don't change proteins — they likely change gene regulation. But which 
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

**RSPO3** is the strongest result. The GWAS lead SNP rs72959041 is 
simultaneously the top eQTL SNP in adipose subcutaneous tissue, sits in 
an active enhancer (open chromatin + strong H3K27ac), and is expressed 
preferentially in preadipocytes. The A allele increases RSPO3 expression, 
amplifying WNT signalling in adipose progenitor cells — a plausible 
mechanism for altered fat depot development.

**CCDC92** shows equally clean colocalisation. The same SNP (rs7133378) 
drives both the GWAS signal and a strong subcutaneous adipose eQTL 
(p=1.34×10⁻²⁰). Single-cell data confirms expression across the mature 
adipocyte lineage, consistent with its role in adipogenesis.

**VEGFA** illustrates the cell-type specificity problem in bulk data. 
Despite a strong GWAS signal (p=9×10⁻¹⁴⁰), the eQTL is weak and 
distant from the GWAS SNP. Single-cell data reveals VEGFA is an 
endothelial cell gene — endothelial cells comprise ~2% of bulk adipose 
tissue, diluting any eQTL signal beyond detection in GTEx.

**RFLNA** is the unexpected finding. The GWAS SNP colocalises with a 
nerve tibial eQTL, not adipose. Single-cell data shows RFLNA is 
expressed predominantly in mesothelial cells — the lining of the 
omental fat pad — which are absent from subcutaneous adipose. This 
suggests a visceral-specific regulatory mechanism operating through 
the omental mesothelium rather than adipocytes directly.

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
