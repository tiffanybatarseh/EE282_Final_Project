# Fundamentals of Informatics Final Project: *Xyella fastidiosa* genome analysis

I am interested in an evolutionary analysis of the plant pathogen *X. fastidiosa*. I would like to do the analysis at the genome level.

First, I am interesetd in the gene content of *X. fastidiosa* isolates

I've downloaded complete, chromosome level assemblies of *X. fastdiosa* isolates from NCBI. I have also downloaded the *X. taiwanensis* genome to use as an outgroup for analysis.

## Basic genome characteristics

**Length of the genomes**

```
bioawk -c fastx '{ print $name, length($seq) }' < xylella_genomes.fasta
AE003849	2679306
AE009442	2519802
CP000941	2475130
CP001011	2535690
CP002165	2491203
CP006696	2750603
CP006740	2642186
CP009790	2681334
CP009823	2788789
CP009826	2666242
CP009829	2748594
CP009885	2813297
CP010051	2733974
CP016608	2508097
CP016610	2508296
CP020870	2508465
CP011800.1	2824527
```

**GC percentage**

Concatanate all of the genomes into one file for easier comparisons. All of the genome fasta files are stored in a single directory.

```
cat *.fasta > xylella_genomes.fasta
```

GC percentage of the genomes and GC distribution.

```
module load jje/jjeutils
bioawk -c fastx '{ print $name, gc($seq) }' xylella_genomes.fasta > GC_genomes.txt
less GC_genomes.txt

AE003849        0.526741
AE009442        0.517764
CP000941        0.519175
CP001011        0.517568
CP002165        0.517813
CP006696        0.521037
CP006740        0.519744
CP009790        0.52676
CP009823        0.529057
CP009826        0.526322
CP009829        0.526319
CP009885        0.52699
CP010051        0.52625
CP016608        0.519824
CP016610        0.519814
CP020870        0.519847
CP011800.1      0.532545
```

To make the plot in R to visualize the GC distribution

```
GC <-read.table("GC_genomes.txt", header=TRUE)

GC$GC_Percentcut <-cut(x=GC$GC_Percent, breaks = 11)

library(ggplot2)

GCgraph <-ggplot(data=GC)

GCgraph + geom_bar(mapping = aes(x=GC$GC_Percentcut)) + labs(title="Sequence GC Distribution", x="GC Percentage", y="Count (Isolates)") + theme_bw()+ theme(axis.text.x = element_text(angle = 60, hjust = 1))

```

![graphGC](https://github.com/tiffanybatarseh/EE282_Final_Project/blob/master/Gc_xylella_11breaks.png?raw=true)

Majority of the isolates tend to have a 52% GC content.

## Prokka: Genome annotation

I want to annotate all of the genomes with the same software to have consistency and an up-to-date annotation that can be compared.

I am using the software Prokka, version 1.13, to annotate the genomes.

```
prokka *.fasta
```

This results in roughly 10 files, of importance is a GFF file with nucleotide sequence appended on it that is neccessary for the subsequent analysis. 

## Roary: to find the genes shared among all of the genomes

I am using version 3.11.2

```
roary -f /Users/tiffanybatarseh/RR_XFT_75blast -i 75 -e -n -cd 100 -v -r /Users/tiffanybatarseh/xylella_PLS_gff/*.gff
```

I am using the program roary to identify the genes shared amongst all of the genomes and to generate a presence-absence matrix of all of the genes and all of the genomes. I specify my directory for the output files as well as the directory with my GFF files. I use the options -i to adjust the blast stage minimum percentage identity and -cd to specify the percentage of isolates a gene must be in to be considered a "core" gene.

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
