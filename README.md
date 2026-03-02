# Metagenomics Pipeline

This pipeline is designed for the comprehensive analysis of metagenomics data, spanning from initial data acquisition to the generation and annotation of **Metagenome-Assembled Genomes (MAGs)**.

---

## Workflow Overview

The pipeline follows a five-step process:



### 1. Data Acquisition 
Public data can be retrieved using two methods:
* **Manual**: Using the [SRA Run Selector](https://www.ncbi.nlm.nih.gov/Traces/study/).
* **Terminal**: Downloading via command line (e.g., `sra-tools`).

### 2. Preprocessing & Quality Control
Before assembly, data must be cleaned and profiled:
- [x] **Quality Control**: Initial assessment of raw reads (e.g., FastQC).
- [x] **Trimming**: Removing adapters and low-quality bases using `Trimmomatic`.
- [x] **Taxonomic Profiling**: Identifying species composition using `Kraken2` and visualizing with `Krona` charts.

### 3. Assembly 
Reconstruct the metagenome from short reads into longer sequences:
* **Tool**: `metSPAdes`
* **Output**: Scaffolds and Contigs.

### 4. MAG Generation (Binning) 
Grouping contigs into individual "bins" representing distinct organisms:
* **Binning**: `MetaBAT2`
* **Quality Assessment**: `CheckM` (to determine Completeness and Contamination).

### 5. Functional Annotation 
Assigning biological function to the genes within the MAGs:
* **Tools**: `Prokka` (rapid prokaryotic annotation) or `eggNOG-mapper` (functional orthology).

---

## 🛠️ Requirements
| Tool | Purpose |
| :--- | :--- |
| **Trimmomatic** | Read Trimming |
| **Kraken2** | Taxonomy |
| **metSPAdes** | Assembly |
| **MetaBAT2** | Binning |
| **Prokka** | Annotation |

> **Note:** Ensure all tools are added to your `PATH` or managed via a Conda environment.
