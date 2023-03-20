# Gurdon scRNA-seq workshop

Author: Adam Reid, Head of Bioinformatics, Gurdon Institute, University of Cambridge
Email: ajr236@cam.ac.uk

Much of this material has been remixed from training materials developed by University of Cambridge Bioinformatics Training Facility course on [Analysis of scRNA-seq data](https://training.csx.cam.ac.uk/bioinformatics/course/bioinfo-scRNAseq), Licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

### Aim of the course

- Learn the initial steps of analysing single cell RNA-seq data from mapping to differential expression using the Seurat packge in R
- The course aims to be practical, focussing on effective analysis with enough detail to avoid common pitfalls 

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
- Most of the course involves a preprepared dataset, but towards the end we aim to work with your own data. If you do not have your own data, it would be useful to identify some public data of interest

## Course set up

To use the course materials you will need the following set up on your computer

### Install R and RStudio

If you do not already have R and Rstudio installed, follow the instructions [here](https://posit.co/download/rstudio-desktop/).

### Install R packages

Open R studio and, in the console, run:

```
install.packages('Seurat')
install.packages("sctransform")
install.packages("tidyr")
install.packages("dplyr")
```

### Get dataset

The course data is based on [Caron et al., 2020](https://www.nature.com/articles/s41598-020-64929-x), a study on pediatric leukemia, with four sample types, including
normal pediatric Bone Marrow Mononuclear Cells (PBMMCs) and three tumour types: ETV6-RUNX1, HHD and PRE-T.

Please note that: these data have been processed for teaching purposes and are therefore not suitable for research use.

You can download the data from DropBox [here](https://www.dropbox.com/sh/s79ttds7px190xu/AAAjHzirFkfik1i08Gh26uU_a?dl=0).

n.b. the file needs to be unzipped and is ~2.6GB, so make sure you have enough space on your computer.

Where these data are stored on your computer will affect some of the commands in the exercises!

## Contents

*[Day 1 – Tuesday March 21st (9am-5pm)](101-seurat_part1.html)*

Level 1 meeting room

- Check set up (9am)
- Introduction ([slides](Gurdon_scrnaseq_Day1.pptx))
- Raw data processing (Demo)
- Quality Control
- **LUNCH 12.30pm-13.30pm**
- Normalisation
- Dimension reduction
- Batch correction
- Clustering

*[Day 2 – Wednesday March 22nd (9am-5pm)](101-seurat_part2.html)*

Level 3 meeting room

- Introduction 9am ([slides](Gurdon_scrnaseq_Day2.pptx))
- Identifying cell types
- Identifying differentially expressed genes
- **LUNCH 12.30pm-13.30pm**
- Working with your own data or public data of interest
  - Working with your own data is always harder than with the nice streamlined examples given in a workshop! We can help you get started with some data of specific interest to you.


## Acknowledgements

Much of this material has been remixed from training materials developed by University of Cambridge Bioinformatics Training Facility course on [Analysis of scRNA-seq data](https://training.csx.cam.ac.uk/bioinformatics/course/bioinfo-scRNAseq), Licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

A variety of people contributed to these, including:

Abigail Edwards, Ashley D Sawle, Chandra Chilamakuri, Kamal Kishore, Stephane Ballereau, Zeynep Kalendar Atak, Hugo Tavares, Jon Price, Katarzyna Kania, Roderik Kortlever, Adam Reid, Tom Smith - Apologies if we have missed anyone!

Much of the material in *that* course was derived from the demonstrations found in the [OSCA book](http://bioconductor.org/books/3.14/OSCA/) and the [Hemberg Group course materials](https://www.singlecellcourse.org/).

