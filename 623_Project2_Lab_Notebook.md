Location:
/projects/bgmp/gmich/bioinfo/Bi623/Project2_QAA/Project-2-Electric-organ-RNA-seq-analysis

Software versions:
cutadapt = ">=5.2,<6"
trimmomatic = ">=0.41,<0.42"
htseq = ">=2.1.2,<3"
fastqc = ">=0.12.1,<0.13"
star = ">=2.7.11b,<3"
samtools = ">=1.23.1,<2"
numpy = ">=2.5.2,<3"
matplotlib = ">=3.11.1,<4"
R 4.6.1

Created pixi environment under "Project2_QAA" folder
  put git repo inside of this folder in order to have access to pixi environment

pixi add <thing to be installed>
<installed thing> -- version

pixi run /usr/bin/time fastqc SRR25630307_1.fastq SRR25630307_2.fastq -o Project-2-Electric-organ-RNA-seq-analysis/
pixi run /usr/bin/time fastqc SRR25630395_1.fastq SRR25630395_2.fastq -o Project-2-Electric-organ-RNA-seq-analysis/

Downloaded and viewed output html files on browser
Commented in Part1_Comments

Part 2:
issue with pixi, had to delete all pixi files and reinstall

pixi add <thing to be installed>
<installed thing> -- version
added packages for part 3 as well

cutadapt and HTSeq had issues with installing; had to delete and reinstall pixi environment

Cutadapt and trimmomatic might be multithreaded, give them 8 cores

cutadapt documentation:
https://cutadapt.readthedocs.io/en/stable/guide.html

Used default parameters and Illumina Adapter sequences; output files as zipped to save space

7. 
Based off of:
https://github.com/usadellab/Trimmomatic/blob/main/README.md#simplified-invocation-v041

java -jar Trimmomatic-0.41.jar PE \
input_forward.fq.gz input_reverse.fq.gz \
output_forward_paired.fq.gz output_forward_unpaired.fq.gz \
output_reverse_paired.fq.gz output_reverse_unpaired.fq.gz \
ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

Changed parameters and removed ILLUMINACLIP parameter, since reads were already trimmed

Error: Unable to access jarfile Trimmomatic-0.41.jar
Command exited with non-zero status 1
	Command being timed: "java -jar Trimmomatic-0.41.jar PE ../cut_SRR25630307_1.fastq.gz ../cut_SRR25630307_2.fastq.gz ../paired_SRR25630307_1.fastq.gz ../unpaired_SRR25630307_1.fastq.gz ../paired_SRR25630307_2.fastq.gz ../unpaired_SRR25630307_2.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:5:15 MINLEN:35"
Tried using "pixi run trimmomatic" after this, which ran successfully:
TrimmomaticPE: Started with arguments:
 ../cut_SRR25630307_1.fastq.gz ../cut_SRR25630307_2.fastq.gz ../paired_SRR25630307_1.fastq.gz ../unpaired_SRR25630307_1.fastq.gz ../paired_SRR25630307_2.fastq.gz ../unpaired_SRR25630307_2.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:5:15 MINLEN:35
Multiple cores found: Using 4 threads
Quality encoding detected as phred33
Input Read Pairs: 11275266 Both Surviving: 11161303 (98.99%) Forward Only Surviving: 81678 (0.72%) Reverse Only Surviving: 28900 (0.26%) Dropped: 3385 (0.03%)
TrimmomaticPE: Completed successfully
	Command being timed: "pixi run trimmomatic PE ../cut_SRR25630307_1.fastq.gz ../cut_SRR25630307_2.fastq.gz ../paired_SRR25630307_1.fastq.gz ../unpaired_SRR25630307_1.fastq.gz ../paired_SRR25630307_2.fastq.gz ../unpaired_SRR25630307_2.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:5:15 MINLEN:35"
	User time (seconds): 638.29
	System time (seconds): 4.94
	Percent of CPU this job got: 389%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 2:45.17


However, cutadapt was not run with paired end setting, so I reran that first. 
Example SLURM output:
Command being timed: "pixi run cutadapt --pair-adapters -a AGATCGGAAGAGCACACGTCTGAACTCCAGTCA -A AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT -o ../cut_SRR25630307_1.fastq.gz -p ../cut_SRR25630307_2.fastq.gz ../../SRR25630307_1.fastq ../../SRR25630307_2.fastq"
	User time (seconds): 81.69
	System time (seconds): 0.64
	Percent of CPU this job got: 99%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 1:22.83
	Average shared text size (kbytes): 0

Followed by trimming :
TrimmomaticPE: Started with arguments:
 ../cut_SRR25630307_1.fastq.gz ../cut_SRR25630307_2.fastq.gz ../paired_SRR25630307_1.fastq.gz ../unpaired_SRR25630307_1.fastq.gz ../paired_SRR25630307_2.fastq.gz ../unpaired_SRR25630307_2.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:5:15 MINLEN:35
Multiple cores found: Using 4 threads
Quality encoding detected as phred33
Input Read Pairs: 11275266 Both Surviving: 11161342 (98.99%) Forward Only Surviving: 81814 (0.73%) Reverse Only Surviving: 28870 (0.26%) Dropped: 3240 (0.03%)
TrimmomaticPE: Completed successfully
	Command being timed: "pixi run trimmomatic PE ../cut_SRR25630307_1.fastq.gz ../cut_SRR25630307_2.fastq.gz ../paired_SRR25630307_1.fastq.gz ../unpaired_SRR25630307_1.fastq.gz ../paired_SRR25630307_2.fastq.gz ../unpaired_SRR25630307_2.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:5:15 MINLEN:35"
	User time (seconds): 742.91
	System time (seconds): 4.83
	Percent of CPU this job got: 418%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 2:58.72
	Average shared text size (kbytes): 0
	Average unshared data size (kbytes): 0


Plotting:
Used R on Talapas:
https://ondemand.talapas.uoregon.edu/pun/sys/dashboard/batch_connect/sys/rstudio/session_contexts/new
With Talapas R module, most recent R, and settled on 2-4h upon resetting


Used bash command adapted from Bi621 ICA4 (repeated for all 4 reads):

/usr/bin/time -v zcat ../paired_SRR25630307_1.fastq.gz | grep -A 1 "^@SRR256303" | grep -v "^--" | grep -v "^@SRR256303" \
| awk '{print length($0)}' | sort -n | uniq -c | sort -n > paired_SRR25630307_1.txt

	Command being timed: "zcat ../paired_SRR25630307_1.fastq.gz"
	User time (seconds): 12.76
	System time (seconds): 0.24
	Percent of CPU this job got: 81%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:16.04

which generated a file with column 1 of frequency and column 2 of read lengths (to be reversed in R for more logical plotting), space delimited

R work:
loaded in deplyr, tidyverse, and ggplot2
	Took ~15 min in total the first time

Initially tried read.delim with space, but column position was inconsistent; read.table automatically dealt with whitespaces and delimiters
Also reorganized and named columns, adding a directionality at the end. 
Example command:
SRR25630307_R1=read.table("paired_SRR25630307_1.txt", header = FALSE, col.names=(c("Frequency","Read.Length")))%>%
  relocate(Read.Length)%>%
  mutate(Directionality="Forward")

Combined forward and reverse reads using rbind(), which appends dataframes 
made barchart, filled color based on directionality, 

made chart log scaled on y axis to make lower frequencies visible

Reran fastQC on trimmed reads and compared results to uncut, untrimmed reads.
Overall patterns were similar, with final files no longer having adapter sequences, ~1% of reads being removed, and overall slightly improved quality metrics. 


Part 3:
Already installed packages at the beginning of part 2

Used code from Bi621 PS8 for STAR alignment and file path formatting

Converted .gff to .gtf
agat_convert_sp_gff2gtf.pl --gff campylomormyrus.gff -o campylomormyrus.gtf
Took ~20 minutes
output looks like a gtf file



For STAR alignment, followed the same commands as Bi621 PS8 

Had to run with 32 CPUs; memory issues otherwise

Output 2 .sam

Mapped_counts.py adapted from PS8, with argparse added
	removed filtering parameter i"f "K" in line"








