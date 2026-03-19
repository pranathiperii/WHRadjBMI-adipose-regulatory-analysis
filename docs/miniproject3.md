## Mini-project 3: Cell-type resolution of adipose regulatory signals using single-cell data

Mini-projects 1 and 2 used bulk tissue data throughout. GTEx gives you 
one expression value per gene per tissue, averaged across every cell in 
the biopsy. Adipose tissue is not one cell type though. It contains 
adipocytes, preadipocytes, endothelial cells, macrophages, mesothelial 
cells, and others all mixed together. When you average across all of 
them, any signal coming from a rare cell type gets diluted and can 
disappear entirely.

This is exactly what seemed to be happening with VEGFA in mini-project 2. The eQTL was weak and the GWAS SNP was far from the top eQTL hit. 
The question this project asks is whether that failure to detect a clean 
signal in bulk data makes more sense once you know which specific cell 
type each gene is actually expressed in.

---

### Data source

**A Single-Cell Atlas of Human White Adipose Tissue** — Emont et al. 2022, 
Nature. Accessed via CellxGene (cellxgene.cziscience.com).

166,149 cells from both omental (visceral) and subcutaneous adipose 
tissue. Cell types are fully annotated, covering adipocytes, 
preadipocytes, endothelial cells, macrophages, mesothelial cells, 
immune cells, pericytes, and smooth muscle cells.

---

### Approach

Each of the six candidate genes from mini-projects 1 and 2 was searched 
in the CellxGene explorer. The UMAP was used to visualise which cell 
clusters showed the highest expression. The cell type panel on the left 
showed expression distributions per cell type, making it possible to 
identify the dominant expressing population for each gene. Findings were 
then connected back to the mini-project 2 eQTL verdict for each gene to 
explain why the bulk signal looked the way it did.

![Cell type UMAP — Emont et al. 2022 adipose atlas](docs/figures/umpa_celltypes.png)

---

### Results

#### RSPO3

RSPO3 expression was concentrated in the preadipocyte cluster. When 
filtering to adipocytes, preadipocytes, and pericytes, the preadipocyte 
population showed the highest and most consistent expression. The 
histogram showed a rightward tail driven by this population, while 
mature adipocytes had lower and more scattered expression.

This makes biological sense. RSPO3 encodes R-spondin 3, which 
potentiates WNT signalling by blocking degradation of WNT receptors. 
WNT signalling is the master regulator of the adipocyte differentiation 
decision, it keeps cells in the progenitor state and suppresses 
terminal differentiation into mature adipocytes. You would expect it 
to be active in preadipocytes rather than mature fat cells.

Preadipocytes make up a large fraction of adipose tissue (52,482 cells 
in this dataset), which explains why the bulk GTEx eQTL for RSPO3 was 
strong and clean. The expressing cell type is abundant enough to 
dominate the bulk signal.

![RSPO3 expression UMAP](../figures/rspo3_umap.png)

| Field | Value |
|---|---|
| Primary cell type | Preadipocytes |
| Also expressed in | Adipocytes (lower), pericytes (low) |
| Bulk eQTL verdict | Strong — same SNP, subcutaneous adipose |
| Does scRNA explain it | Yes — preadipocytes abundant, bulk signal clean |
| Biological interpretation | WNT signalling in adipose progenitors drives fat depot development |

---

#### CCDC92

CCDC92 showed broad expression across the whole adipocyte lineage,
both preadipocytes and mature adipocytes lit up on the UMAP, while 
immune cells, endothelial cells, and macrophages showed essentially 
nothing. The signal was uniform rather than concentrated in one 
subpopulation, which is different from the RSPO3 pattern.

This fits with CCDC92's known role in adipogenesis and lipid metabolism. These
processes happen throughout fat cell development rather than at 
one specific stage. RSPO3 acts before differentiation in progenitors. 
CCDC92 acts during and after it in maturing cells.

Because both adipocytes and preadipocytes together make up the majority 
of cells in adipose tissue, CCDC92 having pan-adipose lineage expression 
explains why its bulk eQTL signal was so strong and clean.

![CCDC92 expression UMAP](../figures/ccd92_umap.png)

| Field | Value |
|---|---|
| Primary cell type | Adipocytes and preadipocytes — whole adipocyte lineage |
| Immune/endothelial expression | Absent |
| Bulk eQTL verdict | Strong — same SNP, subcutaneous adipose |
| Does scRNA explain it | Yes — dominant cell types drive strong bulk signal |
| Biological interpretation | Adipogenesis and lipid metabolism gene active throughout fat cell differentiation |

---

#### VEGFA

VEGFA told a completely different story. The large adipocyte and 
preadipocyte clusters that dominated for RSPO3 and CCDC92 were almost 
entirely flat green which means low expression. The signal was concentrated in 
the small elongated cluster at the top of the UMAP, which corresponds 
to endothelial cells.

Looking at the cell type panel, endothelial cells numbered only 112 
out of 7,193 cells in the filtered view. That is less than 2% of the 
adipose cell population. When GTEx takes a bulk adipose biopsy, the 
VEGFA signal from those endothelial cells gets averaged in with the 
other 98% of cells that barely express it. The result is exactly what 
was seen in mini-project 2, a weak, below-threshold eQTL signal that 
looked like a failed result but is actually a cell-type dilution problem.

VEGFA is named after the cell type it acts in. It encodes vascular 
endothelial growth factor A and drives angiogenesis in endothelial 
cells. It was never going to show a strong signal in bulk adipose tissue.

![VEGFA expression UMAP](../figures/vegfa_umap.png)

| Field | Value |
|---|---|
| Primary cell type | Endothelial cells |
| Adipocyte/preadipocyte expression | Low — largely absent |
| Bulk eQTL verdict | Unlikely — weak signal, 630kb from GWAS SNP |
| Does scRNA explain it | Yes — endothelial cells are ~2% of bulk adipose |
| Biological interpretation | Vascular remodelling in adipose — cell-type eQTL in sorted endothelial cells would likely be much stronger |

---

#### COBLL1

COBLL1 was expressed almost exclusively in mature adipocytes. Every 
immune cell, endothelial cell, and macrophage showed zero expression. 
Preadipocytes were nearly flat too despite being a large population. 
The signal jumped specifically in the mature adipocyte cluster, with 
a bimodal histogram, a spike at zero followed by a long rightward 
tail from the adipocyte subpopulation that expresses it highly.

This pattern is apparently classic for a differentiation-specific gene, where one that 
switches on only when cells complete the transition to mature adipocytes.

The mini-project 2 result for COBLL1 was classified as possible rather 
than strong, with the GWAS SNP matching a thyroid eQTL rather than 
a clean adipose one. The single-cell data helps explain this. Mature 
adipocytes are notoriously difficult to capture in single-nucleus 
RNA-seq. They are enormous lipid-filled cells that fragment during 
tissue dissociation and are systematically underrepresented. GTEx bulk 
tissue captures them better. So the moderate bulk eQTL signal is not 
surprising for a gene that is strongly but specifically expressed in 
a cell type that scRNA-seq underrepresents.

The thyroid connection also becomes clearer here. Thyroid follicular 
cells and mature adipocytes share transcriptional programmes and both 
are metabolically active secretory cells. The same regulatory variant 
may be operating in both tissues via shared transcription factor 
binding, rather than thyroid being the biologically relevant tissue 
for WHR.

![COBLL1 expression UMAP](../figures/cobll1_umap.png)

| Field | Value |
|---|---|
| Primary cell type | Mature adipocytes exclusively |
| Preadipocyte expression | Absent |
| Immune/endothelial expression | Absent |
| Bulk eQTL verdict | Possible — visceral adipose, moderate p-value |
| Does scRNA explain it | Yes — adipocyte dropout in scRNA-seq explains weak bulk signal |
| Biological interpretation | Late-stage adipocyte differentiation gene — regulatory variant acts in mature fat cells |

---

#### RFLNA

RFLNA produced the most unexpected result of the whole project. Only 
736 cells expressed the gene at all across the dataset. Of those, 419 
were mesothelial cells, over 56% of the expressing population. 
Mesothelial cells are the cells that line body cavities, including the 
peritoneum and the omentum. The omentum is visceral adipose tissue, 
and mesothelial cells form its outer lining.

This finding directly explains the mini-project 2 result. RFLNA's GWAS 
SNP colocalised with a nerve tibial eQTL rather than an adipose one, 
and there was an independent strong visceral adipose eQTL 216kb away 
at a different variant. The visceral-specific signal makes complete 
sense now — it is not adipocytes driving the visceral eQTL, it is the 
mesothelial lining of the omentum. Mesothelial cells are present in 
visceral but not subcutaneous adipose, which explains perfectly why 
any regulatory signal at this locus appeared visceral-specific and 
why nothing showed up in subcutaneous.

The nerve tibial connection also makes more sense now. RFLNA encodes 
refilin A, a cytoskeletal regulator. Mesothelial cells and peripheral 
nerve cells share cytoskeletal biology, so the same gene being active 
in both tissues reflects shared structural function rather than shared 
metabolic function.

![RFLNA expression UMAP](../figures/rflna_umap.png)

| Field | Value |
|---|---|
| Primary cell type | Mesothelial cells (56% of expressing cells) |
| Adipocyte expression | Low — 250 cells only |
| Bulk eQTL verdict | Complex — colocalises with nerve tibial, not adipose directly |
| Does scRNA explain it | Yes — mesothelium lines the omentum, explaining visceral-only signal |
| Biological interpretation | Cytoskeletal regulation in omental mesothelium — not an adipocyte gene |

---

#### ZNF664

ZNF664 showed only 161 expressing cells across the entire dataset. 
Expression was split loosely between mesothelial cells (70) and 
adipocytes (80), with very low counts throughout. The histogram was 
ragged and irregular, which is what low-expressed genes look like in 
single-cell data when you are working with very few counts per cell.

This confirmed the mini-project 2 finding directly. No clean adipose 
eQTL, thyroid-dominant signal, sign discordance across tissues. ZNF664 
simply is not a strongly or specifically expressed gene in adipose 
tissue. There is no dominant cell type and no clean single-cell story. 
The absence of a story is itself the finding it explains why bulk 
GTEx found nothing in adipose despite strong chromatin marks at the 
SNP position.

![ZNF664 expression UMAP](../figures/znf644_umap.png)

| Field | Value |
|---|---|
| Primary cell type | None — diffuse low expression |
| Bulk eQTL verdict | No adipose eQTL detected |
| Does scRNA explain it | Yes — lowly expressed throughout, no cell-type story |
| Biological interpretation | Multi-tissue regulator not active in adipose at detectable levels |

---

### Summary

| Gene | Dominant cell type | Mini-project 2 verdict | Does scRNA explain it |
|---|---|---|---|
| RSPO3 | Preadipocytes | Strong | Yes — abundant progenitor cell drives bulk signal |
| CCDC92 | Mature adipocytes | Strong | Yes — dominant cell type drives bulk signal |
| VEGFA | Endothelial cells | Unlikely | Yes — 2% of tissue, signal diluted in bulk |
| COBLL1 | Mature adipocytes | Possible | Yes — adipocyte dropout in scRNA-seq |
| RFLNA | Mesothelial cells | Complex | Yes — mesothelium lines the omentum |
| ZNF664 | None dominant | No adipose signal | Yes — lowly expressed throughout |

---

### What this means overall

Every verdict from mini-project 2 now has a cell-type explanation. The 
two strong results, RSPO3 and CCDC92 worked in bulk data because 
their expressing cell types are abundant enough to dominate the bulk 
signal. The weak results on the other hand (VEGFA, COBLL1, RFLNA) each failed for a 
different reason that only becomes visible at single-cell resolution. 
VEGFA is a rare cell type problem. COBLL1 is a technical dropout 
problem. RFLNA is a tissue compartment problem. The relevant cells 
are in visceral fat only and are not adipocytes at all.

The most unexpected finding was RFLNA in mesothelial cells. This was 
not predicted and raises a question that has not been well addressed 
in the literature what role does the omental mesothelium play in 
fat distribution, and are there other WHR GWAS genes expressed there 
that have been overlooked because most studies focus on adipocytes? 
That question is the starting point for mini-project 4.

---

### Limitations

This analysis used a single published dataset (Emont et al. 2022) and 
relied on visual interpretation of UMAPs and cell type panels rather 
than formal statistical testing. Cell-type-specific eQTL analysis in 
sorted populations would be needed to confirm whether the expressing 
cell type is actually responsible for the regulatory effect at each 
locus. Mature adipocytes are underrepresented in single-nucleus 
RNA-seq data due to technical dropout, which means COBLL1 expression 
may be even more adipocyte-specific than this dataset suggests.

---
