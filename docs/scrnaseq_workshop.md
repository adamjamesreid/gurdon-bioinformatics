# Gurdon scRNA-seq workshop

Author: Adam Reid, Head of Bioinformatics, Gurdon Institute, University of Cambridge
Email: ajr236@cam.ac.uk

Much of this material has been remixed from training materials developed by University of Cambridge Bioinformatics Training Facility course on [Analysis of scRNA-seq data](https://training.csx.cam.ac.uk/bioinformatics/course/bioinfo-scRNAseq), Licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

### Aim of the course

- Learn the initial steps of analysing single cell RNA-seq data from mapping to differential expression

### Learning outcomes
- You will be able to:
  - map 10X chromium single-cell data to a reference genome to generate a read count matrix
  - perform quality control, filtering out uninformative cells and genes
  - normalise raw read counts and visualise the data using UMAP
  - correct the data for batch effects
  - cluster cells and identify cell types
  - determine genes which are differentially expressed between different cell types
- You will also gain experience on processing your own data

### Pre-requisites
- Some knowledge of the R programming language - [try this tutorial](https://www.w3schools.com/r/r_intro.asp)
- If you do not have your own data, it would be useful to identify some public data of interest

## Contents

*[Day 1](docs/101-seurat_part1.html) – Tuesday March 21st (9am-5pm)*

Level 1 meeting room

- Raw data processing
- Quality Control
- Normalisation
- Dimension reduction
- Batch correction
- Clustering

*[Day 2](docs/101-seurat_part2.html) – Wednesday March 22nd (9am-5pm)*

Level 3 meeting room

- Identifying cell types
- Identifying differentially expressed genes
- Working with your own data or public data of interest
  - Working with your own data is always harder than with the nice streamlined examples given in a workshop! We can help you get started with some data of specific interest to you.
