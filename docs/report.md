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
---

## Mini-project 2 — Checking whether GWAS signals work through gene expression changes in adipose tissue

### What this project is about

Finding a GWAS signal tells you where in the genome something interesting 
is happening. It does not tell you how. The next logical question is whether 
the variant influences how much a nearby gene is expressed. If the same SNP 
that associates with a trait also changes gene expression in the relevant 
tissue, that is a strong sign the variant is acting through gene regulation.

This is called colocalisation — checking whether the GWAS signal and the 
expression signal share the same causal variant. Doing this properly 
requires statistical tools and full regional data. Here, a simpler 
conceptual version was applied by comparing where the GWAS signal peaks 
versus where the expression signal peaks at each locus.

The classification used throughout was straightforward. If the top GWAS 
SNP and the top eQTL SNP were the same variant, that was called strong. 
If they were different but nearby, that was called possible. If they were 
clearly at different positions or no adipose eQTL existed at all, that was 
called unlikely.

---

### Where the data came from

GTEx v8 was used for all eQTL lookups. For each gene, the eQTL violin 
plot page was checked for adipose subcutaneous and adipose visceral 
(omentum) specifically. Where no adipose signal was found, other tissues 
were reviewed to understand the broader eQTL picture for that gene.

---

### What was done for each gene

For each of the six candidate genes from mini-project 1, the following 
was recorded from GTEx:

- Whether the gene has a significant eQTL in subcutaneous or visceral 
  adipose tissue
- The position of the top eQTL SNP
- The eQTL p-value and effect size
- Which allele increases or decreases expression
- Whether that top eQTL SNP matches the GWAS lead SNP from mini-project 1

---

### Results

#### RSPO3

RSPO3 was the clearest result by a considerable margin. The GWAS lead SNP 
rs72959041 at chr6:127,133,748 turned out to be the exact same variant 
driving the top eQTL in GTEx adipose subcutaneous tissue. The eQTL 
p-value is 2.27×10⁻⁸, which is statistically robust. People carrying the 
A allele show higher RSPO3 expression in subcutaneous fat compared to 
those with the GG genotype, and the effect follows a clean dose-dependent 
pattern across the three genotype groups.

The GWAS association for this SNP is p=2×10⁻²⁹³, which is one of the 
most significant associations ever reported for any complex trait. The SNP 
sits inside an active enhancer with open chromatin and strong H3K27ac 
signal. Everything points to the same variant, the same tissue, and the 
same direction of effect.

RSPO3 encodes R-spondin 3, which activates WNT signalling. WNT signalling 
plays a well-established role in adipogenesis and fat depot development, so 
a variant that increases RSPO3 expression in subcutaneous adipose 
preadipocytes is a biologically plausible explanation for altered fat 
distribution.

| Field | Value |
|---|---|
| GWAS SNP | rs72959041-A at chr6:127,133,748 |
| GWAS p | 2×10⁻²⁹³ |
| eQTL tissue | Adipose subcutaneous |
| eQTL p | 2.27×10⁻⁸ |
| Effect direction | A allele increases RSPO3 expression |
| SNP overlap | Same SNP |
| Epigenomic evidence | Strong — cCRE, open chromatin, H3K27ac |
| Verdict | Strong colocalisation |

---

#### CCDC92

CCDC92 told an equally clean story. The GWAS lead SNP rs7133378 at 
chr12:123,924,955 is the same variant as the top eQTL in adipose 
subcutaneous tissue, with a p-value of 1.34×10⁻²⁰. The A allele 
increases CCDC92 expression and the pattern across genotype groups is 
textbook additive — each additional copy of A adds expression in a 
consistent stepwise fashion.

One interesting complication at this locus is that the same SNP also 
acts as an eQTL for a neighbouring gene, DNAH10, in the same tissue 
(p=6.93×10⁻¹⁷). One variant affecting two genes simultaneously is 
known as a pleiotropic regulatory effect. CCDC92 was prioritised as 
the primary candidate because its eQTL is stronger and it has prior 
evidence linking it to adipogenesis and lipid metabolism. DNAH10 encodes 
a ciliary motor protein with no obvious connection to fat distribution 
biology, so it was retained as a secondary note rather than a primary 
candidate.

The eQTL here is subcutaneous rather than visceral, which contrasts with 
some of the other loci where visceral adipose carried the signal. 
Subcutaneous fat makes up the larger fraction of total body fat and is 
arguably more relevant to a whole-body measure like WHR.

| Field | CCDC92 | DNAH10 |
|---|---|---|
| GWAS SNP | rs7133378-G at chr12:123,924,955 | Same |
| eQTL p | 1.34×10⁻²⁰ | 6.93×10⁻¹⁷ |
| eQTL tissue | Adipose subcutaneous | Adipose subcutaneous |
| Effect direction | A increases expression | A increases expression |
| SNP overlap | Same SNP | Same SNP |
| Verdict | Strong — primary candidate | Secondary — pleiotropic effect |

---

#### COBLL1

COBLL1 was more complicated. The GWAS lead SNP rs13389219 at 
chr2:164,672,366 matched the top eQTL SNP in thyroid (p=3.44×10⁻⁵) 
rather than adipose. Two separate eQTL signals were found in visceral 
adipose at nearby positions — one at chr2:164,711,185 (p=6.29×10⁻⁶, 
39kb from the GWAS lead) and one at chr2:164,753,988 (p=3.22×10⁻⁷, 
81kb from the GWAS lead). Both show clean additive effects on COBLL1 
expression in visceral fat.

The GWAS SNP itself sits in an active enhancer region with strong ATAC, 
H3K27ac, and H3K4me1 signal, so the regulatory context is convincing. 
The question that cannot be resolved without formal colocalisation is 
whether the thyroid and adipose signals reflect the same underlying 
causal variant in linkage disequilibrium, or genuinely independent 
tissue-specific effects. Given the two visceral adipose eQTLs and strong 
epigenomic support, COBLL1 is kept as a possible adipose regulatory 
candidate.

| Field | Value |
|---|---|
| GWAS SNP | rs13389219-T at chr2:164,672,366 |
| GWAS p | 5×10⁻¹²⁷ |
| Thyroid eQTL | Same SNP, p=3.44×10⁻⁵ |
| Adipose visceral eQTL 1 | chr2:164,711,185, p=6.29×10⁻⁶, 39kb from GWAS SNP |
| Adipose visceral eQTL 2 | chr2:164,753,988, p=3.22×10⁻⁷, 81kb from GWAS SNP |
| Epigenomic evidence | Strong — ATAC, H3K27ac, H3K4me1 |
| Verdict | Possible colocalisation in adipose |

---

#### RFLNA

RFLNA produced the most unexpected finding in mini-project 2. The GWAS 
lead SNP rs863750 at chr12:124,020,897 matched the top eQTL in nerve 
tibial tissue (p=9.32×10⁻⁹) rather than adipose. A separate visceral 
adipose eQTL exists at chr12:124,236,825 (p=2.19×10⁻²²) but this is 
216kb away from the GWAS lead and driven by a completely different variant.

This suggests the locus has two independent regulatory variants each 
active in a different tissue. The nerve tibial colocalisation is 
biologically coherent rather than a dead end — the sympathetic nervous 
system innervates adipose tissue directly and regulates lipolysis and fat 
depot behaviour, so a variant affecting RFLNA expression in peripheral 
nerve could plausibly influence fat distribution through that route. The 
adipose visceral eQTL signal is exceptionally strong but is not captured 
by the GWAS lead SNP, which suggests it may not independently reach 
genome-wide significance for the trait.

| Field | Value |
|---|---|
| GWAS SNP | rs863750-T at chr12:124,020,897 |
| GWAS p | 4×10⁻¹⁰¹ |
| Nerve tibial eQTL | Same SNP, p=9.32×10⁻⁹ |
| Adipose visceral eQTL | chr12:124,236,825, p=2.19×10⁻²², 216kb away |
| Epigenomic evidence | Strong/mixed |
| Verdict | Complex — colocalises with nerve tibial, not adipose directly |

---

#### VEGFA

VEGFA had one of the strongest GWAS signals in the dataset at p=9×10⁻¹⁴⁰ 
but produced the weakest eQTL evidence. The only adipose signal found was 
in visceral tissue at chr6:44,421,450 (p=3.06×10⁻⁵), which does not 
reach standard eQTL significance thresholds. More importantly, this SNP 
sits 630kb away from the GWAS lead rs6905288, and the two variants have 
very different allele frequencies — the GWAS lead has a MAF of 0.574 
while the eQTL SNP has a MAF of around 0.018. These are almost certainly 
independent signals rather than the same underlying variant.

The GWAS SNP sits between two genes, VEGFA and LINC02537, without clearly 
belonging to either. Epigenomic evidence at the position is weak to 
moderate — chromatin is open but H3K27ac signal is limited. The most 
plausible explanation for the weak bulk adipose eQTL is that VEGFA is 
predominantly expressed in a specific cell type within adipose tissue that 
makes up only a small fraction of the total tissue, diluting any signal 
beyond detection in GTEx. This was followed up directly in mini-project 3.

| Field | Value |
|---|---|
| GWAS SNP | rs6905288-A at chr6:43,791,136 |
| GWAS p | 9×10⁻¹⁴⁰ |
| Top adipose eQTL SNP | chr6:44,421,450 |
| eQTL p | 3.06×10⁻⁵ — below threshold |
| Distance between SNPs | approximately 630kb |
| Epigenomic evidence | Weak/moderate |
| Verdict | Unlikely colocalisation — signals are independent |

---

#### ZNF664

ZNF664 had no detectable eQTL in adipose tissue at all. The GWAS SNP 
rs7978610 is a significant eQTL across several other tissues, with thyroid 
showing the strongest effect (p=1.6×10⁻²⁹). An additional complication 
is that the direction of the eQTL effect flips between tissues — the C 
allele decreases ZNF664 expression in thyroid, blood, nerve, and 
fibroblasts but increases it in heart tissue. This kind of sign discordance 
reflects genuinely different regulatory environments in different cell 
types and makes functional interpretation difficult.

The absence of an adipose eQTL likely reflects either genuine tissue 
specificity or insufficient statistical power in GTEx visceral adipose 
samples. The epigenomic evidence at the SNP is strong, so the regulatory 
element exists — it just does not appear to be active in bulk adipose as 
measured here.

ZNF664 sits in the same 12q24.31 cluster as COBLL1, CCDC92, and RFLNA. 
The thyroid-dominant eQTL pattern across multiple genes in this region may 
partly reflect shared regional chromatin architecture rather than 
thyroid-specific biology at each gene individually.

| Field | Value |
|---|---|
| GWAS SNP | rs7978610-C at chr12:123,984,025 |
| GWAS p | 4×10⁻¹⁰¹ |
| Adipose eQTL | Not detected |
| Strongest eQTL tissue | Thyroid, p=1.6×10⁻²⁹, C decreases expression |
| Sign discordance | Yes — opposite direction in heart versus other tissues |
| Epigenomic evidence | Strong — active enhancer, open chromatin, H3K27ac |
| Verdict | No adipose colocalisation |

---

### Summary

| Gene | GWAS p | Same SNP in adipose eQTL | Best adipose eQTL p | Depot | Verdict |
|---|---|---|---|---|---|
| RSPO3 | 2×10⁻²⁹³ | Yes | 2.27×10⁻⁸ | Subcutaneous | Strong |
| CCDC92 | 2×10⁻⁹⁵ | Yes | 1.34×10⁻²⁰ | Subcutaneous | Strong |
| COBLL1 | 5×10⁻¹²⁷ | Nearby, 39 to 81kb | 3.22×10⁻⁷ | Visceral | Possible |
| RFLNA | 4×10⁻¹⁰¹ | Matches nerve, not adipose | 2.19×10⁻²² at separate SNP | Visceral | Complex |
| VEGFA | 9×10⁻¹⁴⁰ | No, 630kb apart | 3.06×10⁻⁵ | Visceral only | Unlikely |
| ZNF664 | 4×10⁻¹⁰¹ | No adipose eQTL found | — | — | No signal |

---

### What this means overall

Two loci — RSPO3 and CCDC92 — show clean evidence that the GWAS variant 
acts through gene expression changes in adipose tissue. These are the 
anchor findings of the project. COBLL1 is a plausible candidate that 
needs formal colocalisation to settle. RFLNA raises an interesting 
question about nerve-to-adipose signalling that is biologically coherent 
but indirect. VEGFA and ZNF664 produced no convincing adipose 
colocalisation, though in both cases the negative result has a plausible 
biological explanation rather than simply being a dead end.

The 12q24.31 region on chromosome 12 is worth noting as a particularly 
complex locus. Four of the six candidate genes cluster within roughly 
100kb, each with different eQTL tissue profiles and different degrees of 
colocalisation evidence. Treating this region as a single signal would 
miss most of the biology — each gene required independent evaluation.

---

### Limitations

The colocalisation approach used here is conceptual rather than 
statistical. Tools like the coloc R package would provide formal posterior 
probabilities of shared causal variants and would be the appropriate next 
step for COBLL1 and RFLNA. GTEx visceral adipose is modestly powered 
compared to subcutaneous, which may explain some absent or weak signals. 
All eQTL data comes from bulk tissue, meaning cell-type-specific effects 
are averaged across the entire tissue and may be undetectable — this 
limitation is what mini-project 3 was designed to address.

---
