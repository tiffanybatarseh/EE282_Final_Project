# Fundamentals of Informatics Final Project: *Xyella fastidiosa* genome analysis

I am interested in an evolutionary analysis of the plant pathogen *X. fastidiosa*. I would like to do the analysis at the genome level.

First, I am interesetd in the gene content of *X. fastidiosa* isolates

I've downloaded complete, chromosome level assemblies of *X. fastdiosa* isolates from NCBI. I have also downloaded the *X. taiwanensis* genome to use as an outgroup for analysis.

## Roary: to find the genes shared among all of the genomes

I am using version 3.11.2

```
roary -f /Users/tiffanybatarseh/RR_XFT_75blast -i 75 -e -n -cd 100 -v -r /Users/tiffanybatarseh/xylella_PLS_gff/*.gff
```

I am using the program roary to identify the genes shared amongst all of the genomes and to generate a presence-absence matrix of all of the genes and all of the genomes. I use the options -i to adjust the blast stage minimum percentage identity and -cd to specify the percentage of isolates a gene must be in to be considered a "core" gene.

This analysis results in many files, one general one is a summary statistic file about the genes found:

```
Core genes	(100% <= strains <= 100%) 1320
Soft core genes	(95% <= strains < 100%)	0
Shell genes	(15% <= strains < 95%)	2370
Cloud genes	(0% <= strains < 15%)	3087
Total genes	(0% <= strains <= 100%)	6777
```

Here this shows that out of the 6,777 identified genes from these genomes, there are 1,320 that occur in all of the genomes including the outgroup. These are genes that I am presently intesrsted in. 

## Tree construction

The roary results also output a nucleotide core gene alignment that I can use to construct a phylogenetic tree. I have used Gblocks to clean up the alignment and remove gaps. 

I can construct a phylogenetic tree with RAXML using the core gene alignment to see the relationship among the isolates. I submitted the following as a job through the UCI HPC.

```
#!/bin/bash
#$ -pe openmp 1-30
#$ -N raxml16test
#$ -q bio

module load RAxML/8.2.10

raxml -s /data/users/tbatarse/raxmlanalysis/core_gene_alignment.aln-gb -w /data/users/tbatarse/raxmlanalysis -n core_gene_16_genomes.out -m GTRCAT -f a -x 8952 -N 100 -p 7341
```

![tree](https://github.com/tiffanybatarseh/EE282_Final_Project/blob/master/Tree_Raxml.png?raw=true)
