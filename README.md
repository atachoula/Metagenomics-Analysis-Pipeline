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

## **3. Assemble data into contigs**
Raw sequencing reads are assembled into longer sequences called contigs. The tools used for this step are metagenomics assemblers like MEGAHIT or metaSPAdes. 

Example tool: metaSPAdes

```javascript
# Example command to run metaSPAdes
metaspades.py -1 input_forward_paired.fastq.gz -2 input_reverse_paired.fastq.gz -o metaspades_output
```

## **4. Generate coverage statistics**
The next step is to generate coverage statistics, by mapping the reads to the contigs:

**Example tools:** bwa, samtools

- Index contigs file

```javascript
bwa index contigs.fasta
```

- Map reads to contigs

```javascript
bwa mem contigs.fasta input_forward_paired.fastq input_reverse_paired.fastq > input.fastq.sam
```

- Format file

```javascript
samtools view -Sbu input.fastq.sam > junk
samtools sort junk input.fastq.sam
```

## **5. Create metagenome-assembled genomes (MAGs)**

The key step in creating MAGs is **binning**. In this step, contigs are grouped into clusters that likely originate from the same organism. Binning relies on two main signals: coverage depth and tetranucleotide frequency (organism ID)

Frequently used binning tools include MetaBAT2 and MaxBin2. In this example we  use MetaBAT2 to bin the assembled contigs into MAGs and then we use CheckM to check the completeness and quality of the MAGs.

```javascript
# Example command to run MetaBAT2
metabat2 -i metaspades_output/contigs.fasta -o metabat2_output/bin \
         -m 1500 --unbinned \
         --saveCls metabat2_output/bin.cls.tsv \
         --saveLog metabat2_output/metabat2.log

# Example command to run CheckM
checkm lineage_wf -t 4 -x fa metabat2_output/bin checkm_output
```


