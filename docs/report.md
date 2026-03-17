# Computational Integration of GWAS, eQTL, and Epigenomic Data to Prioritise Regulatory Disease Loci

---

## Mini-project 1 — Identifying and prioritising WHR-associated GWAS loci with epigenomic regulatory evidence in adipose tissue

### Aim

To move from raw genome-wide association study (GWAS) output for waist-hip ratio (WHR) to a curated set of candidate loci with biological plausibility in adipose tissue, by integrating statistical association data, tissue-specific gene expression, and epigenomic regulatory annotations.

---

### Background

Waist-hip ratio adjusted for BMI (WHRadjBMI) captures fat distribution rather than overall adiposity. High WHR, driven by excess visceral relative to subcutaneous fat, is independently associated with type 2 diabetes, cardiovascular disease, and metabolic syndrome. Understanding its genetic basis is therefore directly relevant to cardiometabolic disease risk.

Most GWAS variants associated with WHRadjBMI lie in non-coding regions of the genome. They do not change protein sequence and so cannot act by altering protein function directly. Instead, they are thought to act by subtly altering gene regulation changing how strongly a transcription factor binds to a regulatory element, which changes how much a nearby gene is expressed, which ultimately influences the trait. Identifying which gene is regulated, and in which tissue, is the central challenge this project addresses.

Gene assignment in GWAS is also not straightforward. The reported gene in a GWAS result is typically the nearest gene by genomic coordinates or the gene suggested by the original study authors. These are starting hypotheses, not conclusions. This project applies independent biological criteria tissue expression and epigenomic evidence to evaluate and refine those assignments.

---

### Data sources

| Resource | Purpose | Link |
|---|---|---|
| GWAS Catalog | WHRadjBMI associations | ebi.ac.uk/gwas |
| GTEx v8 | Tissue-specific gene expression | gtexportal.org |
| ENCODE SCREEN | Epigenomic cCRE annotations | screen.wenglab.org |

---

### Methods

#### Step 1 — GWAS data acquisition and curation

The full WHRadjBMI associations file was downloaded from the GWAS Catalog in .tsv format and opened in Microsoft Excel. The raw file contains extensive publication metadata not required for biological interpretation. The dataset was reduced to the following columns:

| Column | Purpose |
|---|---|
| Genomic region | Cytogenetic band for locus identification |
| Chromosome | Genomic location |
| Base pair position (GRCh38) | Exact coordinate for downstream queries |
| Reported gene(s) | Author-assigned gene annotation |
| Mapped gene(s) | GWAS Catalog coordinate-based gene assignment |
| Strongest SNP and risk allele | rsID and effect allele |
| SNP context | Functional annotation (intron, intergenic etc.) |
| Intergenic flag | Whether SNP lies between genes |
| Risk allele frequency | Minor allele frequency in reference population |
| p-value | Association significance |
| OR or Beta | Effect size |
| Mapped trait | Trait descriptor |

Associations were filtered at genome-wide significance (p < 5×10⁻⁸), the standard threshold correcting for approximately one million simultaneous tests across the genome. Where multiple SNPs mapped to the same locus, the variant with the smallest p-value was retained as the representative signal. Two additional columns were added manually during downstream analysis: adipose subcutaneous expression (yes/no) and adipose visceral expression (yes/no), populated from GTEx lookups described in step 2.

---

#### Step 2 — Tissue expression filter using GTEx

For each candidate gene at a prioritised locus, expression was assessed in adipose subcutaneous and adipose visceral (omentum) tissues using the GTEx portal. Genes were classified as expressed if they showed detectable median TPM values in GTEx adipose samples, and as unexpressed if expression was absent or negligible across donors.

A regulatory variant can only influence fat distribution through a given gene if that gene is transcriptionally active in adipose tissue. Genes showing no adipose expression were deprioritised as primary candidates regardless of their proximity to the associated SNP. This step acts as a biological plausibility filter before any further annotation is applied.

One important note on gene annotation: the GWAS Catalog provides both reported genes (from the original publication) and mapped genes (assigned systematically by genomic coordinate). These do not always agree. Both were recorded and evaluated independently throughout this analysis, since the true regulated gene may differ from the author-reported candidate.

---

#### Step 3 — Epigenomic annotation using ENCODE SCREEN

For each prioritised SNP, the genomic coordinate was queried in SCREEN (screen.wenglab.org) to assess regulatory context. The following features were evaluated for each SNP:

**Chromatin accessibility** — DNase-seq or ATAC-seq signal indicating whether the DNA at that position is physically open and accessible to regulatory proteins. Open chromatin is a prerequisite for regulatory activity.

**H3K27ac** — Histone H3 lysine 27 acetylation marks active enhancers and active promoters. Strong H3K27ac at a SNP position is the single most informative mark for active regulatory function.

**H3K4me1** — Histone H3 lysine 4 monomethylation marks poised and active enhancers. Elevated H3K4me1 without H3K4me3 indicates an enhancer rather than a promoter.

**H3K4me3** — Histone H3 lysine 4 trimethylation marks active promoters specifically. Elevated H3K4me3 at a SNP position suggests proximity to a gene transcription start site.

**cCRE overlap** — ENCODE annotates candidate cis-regulatory elements (cCREs) across the genome based on the above marks. Categories include promoter-like signatures (PLS), proximal enhancer-like signatures (pELS), and distal enhancer-like signatures (dELS). SNP overlap with a cCRE provides independent evidence of regulatory function.

Based on these features, each SNP was classified as having strong, moderate, or weak epigenetic support for regulatory activity. Where sufficient data existed, the regulatory category was noted as enhancer-like or promoter-like.

The underlying rationale: GWAS variants are overwhelmingly non-coding. The most plausible mechanism by which they act is by altering transcription factor binding at regulatory elements, which changes gene expression levels. Epigenomic data allows us to ask whether a variant sits in a region where that kind of regulation is biologically plausible.

---

### Results

Eleven SNPs across six loci passed genome-wide significance for WHRadjBMI and were retained after curation.

#### Table 1 — Curated WHRadjBMI GWAS loci with expression and epigenomic evidence

| Region | Chr | Position | Strongest SNP | Gene(s) | p-value | MAF | Beta | SubQ adipose | Visceral adipose | Epigenetic support |
|---|---|---|---|---|---|---|---|---|---|---|
| 6q22.33 | 6 | 127,133,748 | rs72959041-A | RSPO3 | 2×10⁻²⁹³ | 0.042 | 0.162 | Yes | Yes | Strong — cCRE, open chromatin, H3K27ac |
| 6p21.1 | 6 | 43,791,136 | rs6905288-A | VEGFA, LINC02537 | 9×10⁻¹⁴⁰ | 0.574 | 0.044 | Yes | Yes | Weak/moderate — open chromatin, limited H3K27ac |
| 2q24.3 | 2 | 164,672,366 | rs13389219-T | COBLL1 | 5×10⁻¹²⁷ | NR | 0.069 | Yes | Yes | Strong — ATAC, H3K27ac, H3K4me1, active enhancer |
| 6q22.33 | 6 | 127,130,971 | rs1936805-T | RSPO3 | 2×10⁻¹²⁰ | 0.53 | 0.041 | Yes | Yes | Not assessed |
| 6q22.33 | 6 | 127,077,749 | rs148306315-GCT | RSPO3 | 3×10⁻¹¹⁷ | NR | 0.187 | Yes | Yes | Weak/moderate — active chromatin, no direct cCRE |
| 12q24.31 | 12 | 123,984,025 | rs7978610-C | ZNF664, RFLNA | 4×10⁻¹⁰¹ | NR | 0.064 | Yes | Yes | Strong — cCRE, open chromatin, H3K27ac, enhancer |
| 12q24.31 | 12 | 124,020,897 | rs863750-T | RFLNA | 4×10⁻¹⁰¹ | 0.584 | 0.037 | No | No | Strong/mixed |
| 6q22.33 | 6 | 127,117,497 | rs10872311 | RSPO3 | 4×10⁻⁹⁹ | NR | — | Yes | Yes | H3K4me3 — promoter-associated |
| 12q24.31 | 12 | 124,004,615 | rs11057413-G | ZNF664, RFLNA | 2×10⁻⁹⁷ | NR | 0.063 | Yes | Yes | Not assessed |
| 12q24.31 | 12 | 123,985,191 | rs952632-A | ZNF664, RFLNA | 5×10⁻⁹⁷ | 0.658 | 0.040 | Yes | Yes | Not assessed |
| 12q24.31 | 12 | 123,924,955 | rs7133378-G | CCDC92, DNAH10 | 2×10⁻⁹⁵ | 0.670 | 0.039 | Yes | Yes | Strong — cCRE, open chromatin, H3K27ac, enhancer |

---

### Key observations

**Three genomic regions account for all eleven signals.** The 6q22.33 region contains four independent SNPs all mapping to or near RSPO3. The 12q24.31 region is gene-dense, with five SNPs mapping across CCDC92, DNAH10, ZNF664, and RFLNA within approximately 100kb. One locus on chromosome 2 maps to COBLL1.

**Ten of eleven SNPs show adipose expression.** The single exception is rs863750 at the RFLNA locus, which showed no detectable expression in either adipose depot. All other candidate genes passed the expression filter in both subcutaneous and visceral adipose.

**Epigenomic support is strongest at RSPO3, COBLL1, and CCDC92.** The RSPO3 lead SNP overlaps a cCRE with open chromatin and strong H3K27ac a classic active enhancer signature. COBLL1 shows co-occurrence of ATAC accessibility, H3K27ac, and H3K4me1, confirming active enhancer activity. CCDC92 shows similarly strong enhancer marks. VEGFA shows weaker evidence, suggesting possible context-specific regulation. RFLNA shows mixed evidence.

**All prioritised SNPs are non-coding.** Variant contexts are intron or intergenic none alter protein-coding sequence. This confirms that regulatory mechanisms are the plausible mode of action across all six loci.

**Effect sizes and allele frequencies vary considerably.** RSPO3 rs72959041 has a low minor allele frequency (0.042) but a large effect size (beta 0.162) a rare variant with strong individual impact. CCDC92 rs7133378 (MAF 0.67) and VEGFA rs6905288 (MAF 0.574) are common variants with smaller per-allele effects (beta ~0.04). This range reflects the typical genetic architecture of complex traits.

---

### Interpretation

The strongest candidates based on combined statistical, expression, and epigenomic evidence are RSPO3, COBLL1, and CCDC92. Each combines a robust GWAS signal, confirmed adipose expression, and an active enhancer signature at the lead SNP position.

VEGFA shows strong statistical association but weaker epigenomic evidence and ambiguous gene assignment alongside LINC02537. RFLNA showed no adipose expression at its own lead SNP, raising questions about the tissue mediating its effect on fat distribution. The 12q24.31 region presents a complex regulatory landscape multiple independent signals and overlapping gene annotations require each SNP to be evaluated individually rather than attributed to a single gene.

Reported and mapped gene assignments were treated as hypotheses throughout. In several cases these disagreed, for example rs7978610 was reported as ZNF664/FAM101A but mapped to RFLNA/ZNF664 by coordinate. Final gene prioritisation was based on the combination of adipose expression evidence and epigenomic support rather than proximity alone.

---

### Limitations

This analysis relied on bulk epigenomic data from ENCODE and bulk expression data from GTEx, which represent averages across heterogeneous cell populations. Regulatory effects specific to minority cell types within adipose tissue may be diluted or undetectable. The epigenomic annotations reflect available ENCODE biosamples, which may not capture the most relevant adipose cell types or physiological conditions. Gene assignment remains probabilistic throughout — proximity and epigenomic overlap support but do not prove that a given gene is the functional target of a GWAS variant.

---
