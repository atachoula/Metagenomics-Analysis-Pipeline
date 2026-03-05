# Metagenomics-Analysis-Pipeline

## **1. Download data**

  ### **1.1 SRA run selector**
   SRA run selector (https://www.ncbi.nlm.nih.gov/sra) is an NCBI web-based tool that allows you to search and download metagenomics datasets. 

  ### **1.2 Terminal**

  If you have the accession numbers, you can download the data directly from the command line, using the SRA toolkit. After you install the SRA toolkit, use the following command in order to download the data:

 ```javascript
     fastq-dump --split-files <accession_number>
  ```

Where `<accession_number>` will be replaced by the actual accession number of the data you want to download.


## **2. Data preprocessing**

  ### **2.1 Quality Control**
You should start with a wuality control of your data, in order to identify any potential issues before proceeding with the analysis. You can use tools like FastQC which will provide you with various metrics such as GC content, per base sequence quality and sequence duplication levels.


```javascript
  # If all the fastq.gz files are in the same place in your system, you can do:
  fastqc *.fastq.gz

  ```

  ### **2.2 Trimmomatic**
After the first QC you need to trim adapter sequences and low-quality reads to improve the quality of your data. You can use tools like Trimmomatic

```javascript
# Example command to run Trimmomatic
trimmomatic PE -threads 4 -phred33 input_forward.fastq.gz input_reverse.fastq.gz \
                output_forward_paired.fastq.gz output_forward_unpaired.fastq.gz \
                output_reverse_paired.fastq.gz output_reverse_unpaired.fastq.gz \
                ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```


5. Assemble data into contigs
6. 
