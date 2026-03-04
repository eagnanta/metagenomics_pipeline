# Metagenomics Pipeline

This pipeline is designed for the comprehensive analysis of metagenomics data, spanning from initial data acquisition to the generation and annotation of **Metagenome-Assembled Genomes (MAGs)**.

---

## Workflow Overview

The pipeline follows a five-step process:



## 1. Data Acquisition 
Public data can be retrieved using two methods:

### 1.1 SRA run selector
SRA Run Selector (https://www.ncbi.nlm.nih.gov/Traces/study/) is a web-based tool that allows you to search and download metagenomics datasets

### 1.2 Terminal
Since you have the accession numbers, you can download the data directly via command line (e.g., `sra-tools`). Install the SRA Toolkit on your system and then use the following command to download the data:

`fastq-dump --split-files <accession_number>` 

replacing `<accession_number>` with the actual accession number of the sample you want to download

## 2. Data preprocessing

### 2.1 Quality Control (QC)
You initially have to use tools to assess the raw reads (e.g., FastQC or MultiQC). These tools will provide you with multiple metrics to identify any potential issues with the data.

```
mkdir fastqc_out

# Example command to run FastQC
for each in *.fastq.gz
do 
fastqc ${each} -o fastqc
```

### 2.2 Trimmomatic
After the first QC, you need to use Trimmomatic in order to trim adapter sequences and low-quality reads.

```
# Example command to run Trimmomatic
trimmomatic PE -threads 4 -phred33 input_forward.fastq.gz input_reverse.fastq.gz \
                output_forward_paired.fastq.gz output_forward_unpaired.fastq.gz \
                output_reverse_paired.fastq.gz output_reverse_unpaired.fastq.gz \
                ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

## 3. Assembly of the data into contigs
Reconstruct the metagenome from short reads into longer sequences:
* **Tool**: `metSPAdes`
* **Output**: Scaffolds and Contigs.

```
# Example command to run metaSPAdes
metaspades.py -1 input_forward_paired.fastq.gz -2 input_reverse_paired.fastq.gz -o metaspades_output
```

## 4. Generating coverage statistics
Before creating the metagenome-assembled genomes (MAGs), you need to generste coverage statistics by mapping reads to the contigs. 
* **Tool**: bwa, samtools

```
# index the contigs file that was produced by metaSPAdes:
bwa index contigs.fasta

# map the original reads to the contigs:
bwa mem contigs.fasta input_forward_paired.fastq input_reverse_paired.fastq > input.fastq.sam

# reformat the file with samtools:
samtools view -Sbu input.fastq.sam > junk
samtools sort junk input.fastq.sam
```

## 5. MAG Generation
Bin the assembled contigs into putative metagenome-assembled genomes (MAGs), and then estimate the completeness and the quality of the resulting MAGs
* **Binning**: `MetaBAT2`
* **Quality Assessment**: `CheckM` 

```
# Example command to run MetaBAT2
metabat2 -i metaspades_output/contigs.fasta -o metabat2_output/bin \
         -m 1500 --unbinned \
         --saveCls metabat2_output/bin.cls.tsv \
         --saveLog metabat2_output/metabat2.log

# Example command to run CheckM
checkm lineage_wf -t 4 -x fa metabat2_output/bin checkm_output
```

## 6. Visualization of the phylogenetic tree
To visualize and plot the tree you have produced, you can use the web-based interactive Tree of Life (iTOL):
http://itol.embl.de/index.shtml
However, iTOL only takes in newick formatted trees, so you need to reformat the tree with Figtree (http://tree.bio.ed.ac.uk/software/figtree/)

Other software option for visualization:
* Archaeopteryx: https://sites.google.com/site/cmzmasek/home/software/archaeopteryx
* EvolView: https://www.evolgenius.info/evolview/
* TreeView: http://taxonomy.zoology.gla.ac.uk/rod/treeview.html

## 7. Functional Annotation 
Assigning biological function to the genes within the MAGs:
* **Tools**: `Prokka` (rapid prokaryotic annotation) or `eggNOG-mapper` (functional orthology).

```
# Example command to run Prokka
prokka --outdir prokka_output --prefix sample_name metaspades_output/contigs.fasta
```


