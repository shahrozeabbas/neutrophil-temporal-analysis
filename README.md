# Disentangling Temporal Variation in Neutrophil Transcriptomes

## Background

Neutrophils are typically abundant white blood cells and play critical roles in innate immunity and inflammation. However, their short lifespan and fragility make them challenging to study with single-cell RNA sequencing (scRNA-seq) technologies. The [original study](https://www.cell.com/cell-reports-methods/fulltext/S2667-2375(25)00209-7#sec-9-1) systematically compared multiple scRNA-seq platforms for their ability to capture and profile neutrophils from clinical samples.

**Key findings from the original study:**
- Compared various single-cell capture technologies for neutrophil profiling
- Identified optimal protocols for preserving neutrophil transcriptomes
- Used 10X Genomics Chromium Single Cell Gene Expression **Flex** kit for high-quality neutrophil capture

**The 10X Flex Time-Course Experiment:**

To understand how neutrophils respond to sample processing time, the authors performed a time-course experiment using the Flex technology. Blood samples were collected and cells were profiled at multiple time points (0h, 2h, 4h, 6h, 8h, and 24h) from 3 donors before sequencing. This experimental design allows investigation of:
- Transcriptional changes induced by sample handling
- Stress response signatures over time
- Technical vs. biological variation in neutrophil gene expression

## Motivation for This Analysis

While the original study focused on method comparison, **this repository performs an in-depth temporal analysis** of the neutrophil time-course data using deep learning methods. Specifically, **DRVI (Disentangled Representation Variational Inference) was applied** to:

1. **Disentangle biological from technical variation** - Separate true temporal dynamics from donor-specific effects
2. **Identify time-varying transcriptional programs** - Discover which biological pathways change systematically over time
3. **Characterize stress responses** - Quantify immediate-early gene activation and cellular stress signatures
4. **Enable biological interpretation** - Map latent dimensions to specific genes and pathways

This analysis demonstrates how modern deep learning approaches can be used to resolve deeper biological insights from time-series single-cell data.

## 📋 Overview

Time-course 10X Flex single-cell RNA-seq data from blood cells was analyzed to:
1. Build a blood cell-type atlas while regressing out technical variation
2. Focus on neutrophils and identify temporal transcriptional programs
3. Discover biological pathways activated or suppressed over time

**Key Innovation:** DRVI (Disentangled Representation Variational Inference) was used to learn interpretable latent representations while controlling for batch effects and donor variation, enabling precise temporal analysis.

## 🔬 Analysis Workflow

```
Raw 10X Data
     ↓
[notebooks/01_build_atlas.ipynb]
  • Quality control & doublet removal
  • DRVI training (16 latent dims)
  • Cell type clustering (Leiden)
  • Identify neutrophils (cluster 1)
     ↓
  Outputs:
    • preprocessed_atlas_anndata.h5ad (QC'd data)
    • all_celltypes.h5ad (annotated atlas)
    • atlas_metadata.tsv (cell type labels)
     ↓
[notebooks/02_learn_neutrophil_latents.ipynb]
  • Load preprocessed data + metadata
  • Extract neutrophils (cluster 1)
  • High-resolution DRVI (48 latent dims)
  • OLS regression for temporal latents
  • Gene-latent associations
  • Pathway enrichment analysis
     ↓
Results & Insights
```

## 📊 Key Findings

- **Temporal transcriptional programs identified:** Multiple latent dimensions significantly associated with time were discovered (FDR < 0.05), representing distinct biological programs that change systematically across the 24-hour ex vivo time course
- **Stress response activation:** Strong enrichment of immediate-early stress response genes (MP5 signature: FOS, JUN, EGR1, ATF3) was observed, with expression increasing over time
- **Genes increasing with time:** Enriched for inflammatory pathways (TNF-α/NF-κB signaling), stress responses (P53 pathway, hypoxia), and apoptosis
- **Genes decreasing with time:** Enriched for active immune defense functions (complement, inflammatory response), cell proliferation programs (E2F targets), and coagulation activity, reflecting functional decline during ex vivo culture
- **Biological interpretation:** Neutrophils undergo progressive functional decline outside their physiological environment, losing immune defense capabilities while activating stress and apoptotic programs

## 📁 Repository Structure

```
neutrophils/
├── README.md                               # This file
├── requirements.txt                        # Python dependencies
├── LICENSE                                 # MIT License
├── .gitignore                              # Git ignore rules
└── notebooks/                              # Analysis notebooks
    ├── 01_build_atlas.ipynb                # Step 1: Multi-cell-type atlas
    └── 02_learn_neutrophil_latents.ipynb   # Step 2: Neutrophil temporal analysis
```

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Google Colab (recommended) or local Jupyter environment
- GPU recommended for DRVI training

### Installation

1. Clone this repository:
```bash
git clone https://github.com/shahrozeabbas/neutrophil-temporal-analysis
cd neutrophil-temporal-analysis
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

### Key Dependencies

- `scanpy` - Single-cell analysis
- `drvi-py` - Deep representation learning
- `scvi-tools` - Variational inference framework
- `gseapy` - Pathway ORA analysis
- `decoupler` - Pseudobulk aggregation
- `statsmodels` - Statistical modeling

## 📖 Usage

### Step 1: Build Blood Cell Atlas

`notebooks/01_build_atlas.ipynb` performs the following steps:
- Raw 10X data is loaded
- QC, doublet detection, and filtering are performed
- DRVI model is trained with batch correction
- Cell type clusters are identified
- Annotated atlas files are exported

### Step 2: Neutrophil Temporal Analysis

`notebooks/02_learn_neutrophil_latents.ipynb` performs the following steps:
- Processed atlas is loaded and neutrophils are extracted
- High-resolution DRVI (48 dimensions) is trained
- Time-varying latent dimensions are identified using OLS regression
- Genes are associated with temporal patterns
- Pathway over-representation analysis (ORA) is performed

## 🔍 Methods

### DRVI (Disentangled Representation Variational Inference)

DRVI is a deep generative model that learns interpretable latent representations of single-cell data. Key advantages:
- Learns biologically meaningful latent dimensions
- Regresses out batch effects and donor variation
- Enables interpretation via latent traversal
- Captures complex non-linear relationships

### Statistical Analysis

**Temporal Latent Discovery:**
- Pseudobulk aggregation by donor × time point
- OLS regression: `latent ~ time + donor`
- Multiple testing correction (Benjamini-Hochberg FDR)
- Identification of latent dimensions significantly changing over time (FDR < 0.05)

**Gene-Latent Association:**
- Latent traversal to identify genes responsive to each dimension
- Direction-specific gene sets (latent+ and latent-)
- Mapping to temporal trends (time+ and time-)

## 📊 Data

The analysis uses time-course 10X Flex single-cell RNA-seq data of blood cells across multiple donors and time points. 

**Source:** Comparison of single-cell RNA-seq methods to enable transcriptome profiling of neutrophils in clinical samples (see references below)

**Data Structure:**
- 3 donors
- 6 time points: 0h, 2h, 4h, 6h, 8h, 24h
- Blood tissue
- 10X Genomics platform

**Note:** Data files are not included in this repository due to size. 

## 📈 Results Interpretation

### Latent Dimensions
Each latent dimension captures a coordinated gene expression program. The OLS regression is a simple linear regression to determine if certain latents capture the signal associated with time. Dimensions with significant time coefficients represent temporal change in neutrophil transcriptomes consistent with what the original authors reported. 

### Pathway Enrichment
Biological functions enriched in genes that increase (time+) or decrease (time-) over the time course are identified, revealing:
- Immune activation pathways
- Stress response programs


## 📚 References

1. **Original Data Source:** Comparison of single-cell RNA-seq methods to enable transcriptome profiling of neutrophils in clinical samples. *Cell Reports Methods* (2025). DOI: [10.1016/j.crmeth.2025.101173](https://www.cell.com/cell-reports-methods/fulltext/S2667-2375(25)00209-7#sec-9-1)

2. **DRVI Method:** Moinfar, A.A. et al. Unsupervised Deep Disentangled Representation of Single-Cell Omics. *bioRxiv* (2025). DOI: [10.1101/2024.11.06.622266](https://doi.org/10.1101/2024.11.06.622266)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.


## 🙏 Acknowledgments

- DRVI developers for the methodology and software
- Original data authors from the 10X Flex time-course study
- Scanpy and scvi-tools communities

## 📧 Contact

For questions or collaborations, please open an issue on GitHub.

Note: This is a computational analysis repository. For questions about the original data or experimental methods, please refer to the original publication.

---

