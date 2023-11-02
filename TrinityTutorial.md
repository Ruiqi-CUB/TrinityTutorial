# Trinity Assembly Tutorial
Ruiqi Li
Last Update: 10/01/2023

Trinity is one of the most popular tool to assemble transcripts from RNA-Seq short reads. In this tutorial, we will cover the basic usage of Trinity, best practice and common problems.  


Reference:

Grabherr MG, Haas BJ, Yassour M, Levin JZ, Thompson DA, Amit I, Adiconis X, Fan L, Raychowdhury R, Zeng Q, Chen Z, Mauceli E, Hacohen N, Gnirke A, Rhind N, di Palma F, Birren BW, Nusbaum C, Lindblad-Toh K, Friedman N, Regev A. Full-length transcriptome assembly from RNA-seq data without a reference genome. Nat Biotechnol. 2011 May 15;29(7):644-52. doi: 10.1038/nbt.1883. PubMed PMID: 21572440.

## 1. Installation

If you have conda and bioconda set up in your system, you can simply install Trinity by a single command: `conda install -c bioconda trinity`

Alternatively, you can run Trinity through docker `docker pull trinityrnaseq/trinityrnaseq`.

or you can download Trinity natively and install it manually:

```bash
# You probably want to change the link to the latest version by checking https://github.com/trinityrnaseq/trinityrnaseq/releases
wget https://github.com/trinityrnaseq/trinityrnaseq/archive/Trinity-v2.3.2.tar.gz \
    -O trinity.tar.gz
make
# build the additional plugin components
make plugins
#  install Trinity in a central location
make install
# Add path
export TRINITY_HOME=/path/to/trinity/installation/dir
```

## 2. Data Preparation

Skip this step if you are only running Trinity from one sample.

If you have multiple samples, in the directory where your sequencing files are, use `ls *_1.fq.gz | sed 'N;s/\n/,/'`  and `ls *_2.fq.gz | sed 'N;s/\n/,/'` to get the file names for `--left` and `--right`.

## 3. Transcriptome Assembly

Please refer to the [Trinity documentation](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Running-Trinity) for the complete list of parameters.

Here are some common parameters to set up:

Data type:

`--seqType`: for fasta files, use `fa`; for Fastq files, use `fq`

`--left <filenames>` and `--right <filenames>` for paired reads, `--single` for unpaired reads

Computation: You probably need to check your research computing facility regulations or the spec of your work station for the following:

`--max_memory`: max memory to use by Trinity

`--CPU <int>`: number of CPUs to use


Other:

`--trimmomatic`: run Trimmomatic to quality trim reads

`--output <OutPut>`: name of directory for output (will be created if it doesn't already exist)

## 3.1 Single Transcriptome Assembly

Run a single Transcriptome Assembly is straightforward, you can modify the command based on your file name, computer specs, etc.:

```bash
Trinity --seqType fq --trimmomatic --max_memory 50G \
         --left reads_1.fq.gz  --right reads_2.fq.gz --CPU 6
```  


## 3.2. Meta-Transcriptome Assembly

Sometimes a meta-transcriptome is needed for downstream analyses, you will need to assemble the meta-transcriptome from multiple samples. This will usually require a lot for computational power. Please contact your University research computing facility to request the access.

For a small number of samples, you may be able to get it done on your computer:
```bash
Trinity --seqType fq --trimmomatic --max_memory 50G  \
         --left condA_1.fq.gz,condB_1.fq.gz,condC_1.fq.gz \
         --right condA_2.fq.gz,condB_2.fq.gz,condC_2.fq.gz \
         --CPU 6
```

If you would like to do it in a research computing facility, usually a script is needed. To run the script, you will need to use `sbatch script.sh` Here is an example:

```bash
##### This script is for meta-transcriptome de novo assembly on Research Computing Facilities using Trinity.
##### Ruiqi Li
##### 2020-07
##### Activate python 3 and Trinity environments before running
##### source /curc/sw/anaconda3/latest
##### conda activate Trinity
##### Run script
##### sbatch scriptname

#SBATCH --nodes=1
#SBATCH --time=7-00:00
#SBATCH --partition=smem
#SBATCH --ntasks=48
#SBATCH --job-name=test_ruiqi
#SBATCH --output=test_trinity.%j.out

Trinity --seqType fq --trimmomatic --max_memory 1024G  \
         --left condA_1.fq.gz,condB_1.fq.gz,condC_1.fq.gz \
         --right condA_2.fq.gz,condB_2.fq.gz,condC_2.fq.gz \
         --CPU 48
```

## 4. Common Questions

1. Interruption while running Trinity

If the program is interrupted, usually you can just run the same command one more time, and the program will resume from where it stopped.

2. Error messages

The best way to troubleshoot the error messages is to google it, you can also post it on the [BioStars](BioStars.org) or open an issue on the [Trinity Github](https://github.com/trinityrnaseq/trinityrnaseq/issues).
