# Neutrophil Temporal Dynamics Analysis

## Background

Neutrophils are the most abundant white blood cells and play critical roles in innate immunity and inflammation. However, their short lifespan and fragility make them challenging to study with single-cell RNA sequencing (scRNA-seq) technologies. The original study ([Comparison of single-cell RNA-seq methods](https://doi.org/10.1016/j.crmeth.2025.101173)) systematically compared multiple scRNA-seq platforms for their ability to capture and profile neutrophils from clinical samples.

**Key findings from the original study:**
- Compared various single-cell capture technologies for neutrophil profiling
- Identified optimal protocols for preserving neutrophil transcriptomes
- Used 10X Genomics Chromium Single Cell Gene Expression **Flex** kit for high-quality neutrophil capture

**The 10X Flex Time-Course Experiment:**

To understand how neutrophils respond to sample processing time, the authors performed a time-course experiment using the Flex technology. Blood samples were collected and cells were profiled at multiple time points (0h, 2h, 4h, 6h, 8h, and 24h) before sequencing. This experimental design allows investigation of:
- Transcriptional changes induced by sample handling
- Stress response signatures over time
- Technical vs. biological variation in neutrophil gene expression

## Motivation for This Analysis

While the original study focused on method comparison, **this repository performs an in-depth temporal analysis** of the neutrophil time-course data using advanced computational methods. Specifically, we apply **DRVI (Disentangled Representation Variational Inference)** to:

1. **Disentangle biological from technical variation** - Separate true temporal dynamics from donor-specific effects
2. **Identify time-varying transcriptional programs** - Discover which biological pathways change systematically over time
3. **Characterize stress responses** - Quantify immediate-early gene activation and cellular stress signatures
4. **Enable biological interpretation** - Map latent dimensions to specific genes and pathways

This analysis demonstrates how modern deep learning approaches can extract deeper biological insights from time-series single-cell data.

## 📋 Overview

This project analyzes time-course 10X Flex single-cell RNA-seq data from blood cells to:
1. Build a multi-cell-type blood atlas while regressing out technical variation
2. Focus on neutrophils and identify temporal transcriptional programs
3. Discover biological pathways activated or suppressed over time

**Key Innovation:** Uses DRVI (Disentangled Representation Variational Inference) to learn interpretable latent representations while controlling for batch effects and donor variation, enabling precise temporal analysis.

## 🔬 Analysis Workflow

```
Raw 10X Data
     ↓
[notebooks/BloodCellAtlas.ipynb]
  • Quality control & doublet removal
  • DRVI training (16 latent dims)
  • Cell type clustering (Leiden)
  • Identify neutrophils (cluster 2)
     ↓
all_celltypes.h5ad
     ↓
[notebooks/NeutrophilTimeLatents.ipynb]
  • Extract neutrophils
  • High-resolution DRVI (48 latent dims)
  • OLS regression for temporal latents
  • Gene-latent associations
  • Pathway enrichment analysis
     ↓
Results & Biological Insights
```

## 📊 Key Findings

- **Time Points:** 0h, 2h, 4h, 6h, 8h, 24h
- **Significant temporal latent dimensions:** Identified via OLS regression (FDR < 0.01)
- **Stress response signatures:** MP5 gene set (FOS, JUN, EGR1, ATF3, etc.)
- **Pathway enrichment:** MSigDB Hallmark, KEGG, Reactome, GO Biological Process

## 📁 Repository Structure

```
neutrophils/
├── README.md                          # This file
├── requirements.txt                   # Python dependencies
├── LICENSE                            # MIT License
├── .gitignore                         # Git ignore rules
└── notebooks/                         # Analysis notebooks
    ├── BloodCellAtlas.ipynb          # Step 1: Multi-cell-type atlas
    └── NeutrophilTimeLatents.ipynb   # Step 2: Neutrophil temporal analysis
```

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Google Colab (recommended) or local Jupyter environment
- GPU recommended for DRVI training

### Installation

1. Clone this repository:
```bash
git clone https://github.com/yourusername/neutrophils.git
cd neutrophils
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

### Key Dependencies

- `scanpy` - Single-cell analysis
- `drvi-py` - Deep representation learning
- `scvi-tools` - Variational inference framework
- `gseapy` - Pathway enrichment analysis
- `decoupler` - Pseudobulk aggregation
- `statsmodels` - Statistical modeling

## 📖 Usage

### Step 1: Build Blood Cell Atlas

Open and run `notebooks/BloodCellAtlas.ipynb`:
- Loads raw 10X data
- Performs QC, doublet detection, and filtering
- Trains DRVI model with batch correction
- Identifies cell type clusters
- Exports `all_celltypes.h5ad`

### Step 2: Neutrophil Temporal Analysis

Open and run `notebooks/NeutrophilTimeLatents.ipynb`:
- Loads processed atlas and extracts neutrophils
- Trains high-resolution DRVI (48 dimensions)
- Identifies time-varying latent dimensions using OLS regression
- Associates genes with temporal patterns
- Performs pathway enrichment

## 🔍 Methods

### DRVI (Disentangled Representation Variational Inference)

DRVI is a deep generative model that learns interpretable latent representations of single-cell data while accounting for technical covariates. Key advantages:
- Learns biologically meaningful latent dimensions
- Regresses out batch effects and donor variation
- Enables interpretation via latent traversal
- Captures complex non-linear relationships

### Statistical Analysis

**Temporal Latent Discovery:**
- Pseudobulk aggregation by donor × time point
- OLS regression: `latent ~ time + donor`
- Multiple testing correction (Benjamini-Hochberg FDR)
- Identification of latent dimensions significantly changing over time (FDR < 0.01)

**Gene-Latent Association:**
- Latent traversal to identify genes responsive to each dimension
- Direction-specific gene sets (latent+ and latent-)
- Mapping to temporal trends (time+ and time-)

## 📊 Data

The analysis uses time-course FLEX single-cell RNA-seq data of blood cells across multiple donors and time points. 

**Source:** Comparison of single-cell RNA-seq methods (see `Comparison-of-single-cell-RNA-seq-methods-to-enabl.pdf`)

**Data Structure:**
- Multiple donors
- 6 time points: 0h, 2h, 4h, 6h, 8h, 24h
- Blood tissue
- 10X Genomics format

**Note:** Raw data files are not included in this repository due to size. Contact the authors or refer to the original publication for data access.

## 📈 Results Interpretation

### Latent Dimensions
Each latent dimension captures a coordinated gene expression program. Dimensions with significant time coefficients represent temporal biological processes.

### Pathway Enrichment
Identifies biological functions enriched in genes that increase (time+) or decrease (time-) over the time course, revealing:
- Immune activation pathways
- Stress response programs
- Metabolic changes
- Cell cycle dynamics

## 📚 References

1. **Original Data Source:** Comparison of single-cell RNA-seq methods to enable transcriptome profiling of neutrophils in clinical samples. *Cell Reports Methods* (2025). DOI: [10.1016/j.crmeth.2025.101173](https://doi.org/10.1016/j.crmeth.2025.101173)

2. **DRVI Method:** Moinfar, A.A. et al. Unsupervised Deep Disentangled Representation of Single-Cell Omics. *bioRxiv* (2025). DOI: [10.1101/2024.11.06.622266](https://doi.org/10.1101/2024.11.06.622266)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.


## 🙏 Acknowledgments

- DRVI developers for the methodology and software
- Original data authors from the 10X Flex time-course study
- Scanpy and scvi-tools communities

## 📧 Contact

For questions or collaborations, please open an issue on GitHub.

---

**Note:** This is a computational analysis repository. For questions about the original data or experimental methods, please refer to the original publication.

