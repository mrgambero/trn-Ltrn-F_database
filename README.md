## Instructions for the installation, set-up and use of the tool CRABs to create a custom database for the intergenic spacer trnL-trnF to use for amplicon sequencing analysis

### First step, installation of crabs
As reported in the github https://github.com/gjeunen/reference_database_creator using conda

```
conda create -n CRABS
conda activate CRABS
conda install -c bioconda crab
```
### Second step, download database

```
crabs db_download --source ncbi --database nucleotide --query '((((trnL-trnF[All Fields] OR trnL[All Fields]) OR trnF[All Fields]) OR tRNA-Leucine[All Fields]) OR tRNA-Phenylalanine[All Fields]) OR trnL-F[All Fields] AND plants[filter]' --output lach.fasta --keep_original yes --email youremail@gmail.com --batchsize 5000
```

### Third step, insilico PCR (optional)
```
crabs insilico_pcr --input lach.fasta --output output.fasta --fwd GGTTCAAGTCCCTCTATCCC --rev ATTTGAACTGGTGACACGAG --error 4.5
```

### 4th step, assign taxonomy
For this file you need to first download and extract these two files from the NCBI: <br />
'ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/accession2taxid/nucl_gb.accession2taxid.gz' <br />
'ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/taxdump.tar.gz'

```
crabs assign_tax --input lach.fasta --output output.tsv --acc2tax nucl_gb.accession2taxid --taxid nodes.dmp --name names.dmp
```
### 5th step, dereplication

```
crabs dereplicate --input output.tsv --output wank.tsv --method uniq_species                                            
```

### 6th step, visualize

```
 crabs visualization --method diversity --input input.tsv --level class
``` 

### 7th step, export for  DADA2

```
crabs tax_format --input wank2.tsv --output output.fasta --format dad                                               
```

### ready to use with dada2 assign taxonomy

```
library(dada2)
its2taxa <- assignTaxonomy(itsasvs, output.fasta, multithread=TRUE)
```
