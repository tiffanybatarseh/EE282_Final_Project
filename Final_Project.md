# Fundamentals of Informatics Final Project: *Xyella fastidiosa* genome analysis

I am interested in an evolutionary analysis of the plant pathogen *X. fastidiosa*. I would like to do the analysis at the genome level.

First, I am interesetd in the gene content of *X. fastidiosa* isolates

I've downloaded complete, chromosome level assemblies of *X. fastdiosa* isolates from NCBI. I have also downloaded the *X. taiwanensis* genome to use as an outgroup for analysis.

## Roary: to find the genes shared among all of the genomes

Version 3.11.2

```
roary -f /Users/tiffanybatarseh/RR_XFT_75blast -i 75 -e -n -cd 100 -v -r /Users/tiffanybatarseh/xylella_PLS_gff/*.gff
```

I am using the program roary to identify the genes shared amongst all of the genomes and to generate a presence-absence matrix of all of the genes and all of the genomes. I use the options -i to adjust the blast stage minimum percentage identity and -cd to specify the percentage of isolates a gene must be in to be considered a "core" gene.

This results in a summary statistic file about the genes found:

```
Core genes	(100% <= strains <= 100%)	1320
Soft core genes	(95% <= strains < 100%)	0
Shell genes	(15% <= strains < 95%)	2370
Cloud genes	(0% <= strains < 15%)	3087
Total genes	(0% <= strains <= 100%)	6777
```
