# metagenomics_pipeline
This pipeline is used for analyzing metagenomics data. The steps span from data download to the creation of metagenome-assembled genomes (MAGs) and their annotation.

1) Downloading public data
   - with SRA run selector
   - or with downloading from terminal

2) Data preprocessing
  - Quality control
  - Trimming (trimmomatic)
  - Quality control
  - Taxonomic profiling (Kraken2 and Krona)

3) Assemblying of the data into contigs
  - metSPAdes

4) Creating metagenome-assembled genomes (MAGs)
  - MetaBAT2
  - CheckM

5) Annotating
  - Prokka or eggNOG-mapper

