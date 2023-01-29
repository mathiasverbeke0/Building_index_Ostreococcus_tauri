# Building Genome and Transcriptome Indexes for Ostreococcus tauri with HISAT2
## Introduction
Ostreococcus tauri is a small eukaryotic organism with a relatively small genome, making it suitable for quick construction of genome and transcriptome indexes. In this README, we will outline the steps to build these indexes.

## Prerequisites
* Files: Ostreococcus tauri genome (.fna) and general transfer format (.gtf)
* Software: Hisat2

## Building the Genome Index
1. Run the following command to download the genomic fasta file.

```bash     
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/214/015/GCF_000214015.3_version_140606/GCF_000214015.3_version_140606_genomic.fna.gz
```
2. Run the following command to unzip the genomic fasta file.

```bash     
gunzip GCF_000214015.3_version_140606_genomic.fna.gz
```
3. Run the following command to create a genome index folder.
```bash     
mkdir GenomeIndex
```
4. Run the following command to build the genome index (4 threads used). Take a look in the GenomeIndex folder after executing this command.
```bash     
hisat2-build GCF_000214015.3_version_140606_genomic.fna GenomeIndex/genome -p 4
```

## Building the Transcriptome Index
1. Run the following command to download the general transfer file. 

```bash     
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/214/015/GCF_000214015.3_version_140606/GCF_000214015.3_version_140606_genomic.gtf.gz
```
2. Run the following command to unzip the general transfer file.
```bash     
gunzip GCF_000214015.3_version_140606_genomic.gtf.gz
```
3. Run the following command to create a transcriptome index folder. 
```bash     
mkdir TranscriptomeIndex
```
4. Run the following command to extract the splice sites from the Ostreococcus tauri genome (information about splice sites can be found in the .gtf file). Take a look in the TranscriptomeIndex folder after executing this command.
```bash     
hisat2_extract_splice_sites.py GCF_000214015.3_version_140606_genomic.gtf > TranscriptomeIndex/SpliceSites.ss
```
5. Run the following command to extract the exons from the Ostreococcus tauri genome (information about exons can be found in the .gtf file). Take a look in the TranscriptomeIndex folder after executing this command.
```bash     
hisat2_extract_exons.py GCF_000214015.3_version_140606_genomic.gtf > TranscriptomeIndex/Exons.exon 
```
6. Run the following command to build the transcriptome index (4 threads used). Take a look in the TranscriptomeIndex folder after executing this command.
```bash     
hisat2-build --ss TranscriptomeIndex/SpliceSites.ss --exon TranscriptomeIndex/Exons.exon GCF_000214015.3_version_140606_genomic.fna TranscriptomeIndex/transcriptome -p 4 
```

## Testing the Genome and Transcriptome Index
1. Run the following command to download a few spots from a sample record that is linked to Ostreococcus tauri.
```bash     
fastq-dump --gzip --split-3 -X 1000 SRR7121135 
```
2. Run the following command to create a results folder
```bash     
mkdir Results 
```
3. Align the RNA-seq reads to the genome index:
```bash     
hisat2 -x GenomeIndex/genome -U SRR7121135_1.fastq.gz -S Results/SRR7121135_1_genome.sam 
```
4. Align the RNA-seq reads to the transcriptome index:
```bash     
hisat2 -x TranscriptomeIndex/transcriptome -U SRR7121135_1.fastq.gz -S Results/SRR7121135_1_transcriptome.sam 
```
