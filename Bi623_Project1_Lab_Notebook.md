File location:
/projects/bgmp/gmich/bioinfo/Bi623/gmichaels4444-bit-Bi623-Project-1


Software info:
Python version 3.14 (Pixi installed)
bedtools version 2.31.1


Part 1:
phyloP file:
 /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/241-mammalian-2020v2.bigWigToBedGraph.gz
took the first 20 lines using head command for test.tsv

edited to have the following cases:
multiple chromosomes (for ensuring adjacent reads on different chromosomes are not combined)
singlets (to be tossed)
singlets next to roccs (rocc saved, singlet tossed)
roccs separated by 1 bp (to be merged)
Combinations of the above chained together

Scripting strategy:
First loop to make first dictionary (rocc_dict) to combine adjacent high reads 
  does not remove singlets/sequences below a cutoff length or combine reads with 1 low phyloP score between them
Second loop to make second dictionary (merge_dict) removes reads less than 2 bp (singlets) and combines RoCCs separated by 1 bp
third loop for filtering out RoCCs under a certain size and printing


Note: had to convert numbers (base positions) in dictionaries to ints in order to do math
After fixing typos and ordering and data types of dictionary, successfully ran on 25 line test.tsv to output testout.tsv
Had to add loop to deal with niche case of last read on chr n and first read of chr n+1 being constrained

Command being timed: "./Project1_pt1.py -f test.tsv -l 2 -o testout.tsv"
	User time (seconds): 0.03
	System time (seconds): 0.01
	Percent of CPU this job got: 52%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.08

Changed to gzip.open for zipped file and ran on full file with minimum RoCC length of 20:

Command being timed: "./Project1_pt1.py -f /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/241-mammalian-2020v2.bigWigToBedGraph.gz -l 20 -o PhyloP_RoCC_output.txt"
	User time (seconds): 1698.59
	System time (seconds): 7.40
	Percent of CPU this job got: 99%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 28:31.55
	Average shared text size (kbytes): 0
	Average unshared data size (kbytes): 0
	
wc -l PhyloP_RoCC_output.txt 
595077 PhyloP_RoCC_output.txt
Unsorted

Sorted using bash commands:
sorted largest to smallest RoCCs, with a tiebreaker of chromosome (1-22, then X, then Y), and a final tiebreaker of start base number (highest to lowest, per Hope) 

sort -V for version numbers works for chr number, including X and Y
Command being timed: "sort -k4,4gr -k1,1V -k2,2gr PhyloP_RoCC_output.txt"
	User time (seconds): 5.83
	System time (seconds): 0.02
	Percent of CPU this job got: 97%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:05.99
	Average shared text size (kbytes): 0
	Average unshared data size (kbytes): 0
	Average stack size (kbytes): 0
	Average total size (kbytes): 0
	Maximum resident set size (kbytes): 48776

Count slightly off (~500 too few RoCCs), set phylopP >= 2.27 (previously just >)

Command being timed: "./Project1_pt1.py -f /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/241-mammalian-2020v2.bigWigToBedGraph.gz -l 20 -o PhyloP_RoCC_output.txt"
	User time (seconds): 1655.52
	System time (seconds): 7.45
	Percent of CPU this job got: 99%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 27:48.54
	Average shared text size (kbytes): 0

wc -l PhyloP_RoCC_output.txt 
595535 PhyloP_RoCC_output.txt
1 line shorter than classmates' files; but actual file has 1 more line

wc -l PhyloP_RoCC_output_sorted.txt 
595536 PhyloP_RoCC_output_sorted.txt
correct length, properly sorted
	
Part 2

For R on Talapas: https://ondemand.talapas.uoregon.edu/pun/sys/dashboard/batch_connect/sys/rstudio/session_contexts/new With Talapas R module, most recent R, and settled on 2-4h upon resetting

For initial testing, downloaded onto personal computer and ran locally

Downloaded file onto personal computer for R plotting:
scp gmich@login.talapas.uoregon.edu:/projects/bgmp/shared/Bi623/ZoonomiaWorkshop/variant_summary.txt.gz /gmichaels/Downloads

gzcat used for initial file exploration, found file contains headers

Used read_tsv() after read.table() had issues with header and NAs
	consider using fread in the future
Steps for parsing data:
1. Filtered for each condition
2. selected desired columns
3. sorted using arrange()(had to make vector to sort by for chromosome column, likely more efficient way of accomplishing this),
4. mutated chromosome column to match part 1 output using paste0()
5. Output file written out as tsv; 226 lines matched classmates' output.

Initially had ~2x larger output when filtering for "Cranio" and ignoring case, but changed to specifically uppercase first letter to match consensus output

Part 3:
pixi add bedtools

https://bedtools.readthedocs.io/en/latest/content/tools/multiinter.html
used for developing command

Added header, abbreviations for each file for easier header reading
output to "part3_bedtools_output.tsv"

Used initial output for part1, since sorting of file from python before bash sorting matches desired pattern for bedtools multiinter
Removed header from Cranio_variants_sorted.tsv using:
```tail -n +2 Cranio_variants_sorted.tsv > prepped_Cranio_variants_sorted.tsv```

WARN cache for Repodata at /home/gmich/.cache/rattler/cache/repodata is on a network/parallel filesystem (NFS/SMB/FUSE/BeeGFS/Lustre/GPFS/CephFS), redirected to /tmp/pixi-cache-gmich/repodata for this run. Set [cache.repodata] in config.toml or PIXI_CACHE_DIR to override, or [cache.netfs-redirect] = "never" to keep the original path.
	Command being timed: "pixi run bedtools multiinter -names RoCCs, Cranio, GSM86, GSM87, GSM88, GSM89, GSM90 -header -i PhyloP_RoCC_output.txt prepped_Cranio_variants_sorted.tsv /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/GSM7508786_CS18-12676-ATAC_peaks-q1.3.narrowPeak.gz /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/GSM7508787_CS18-12695-ATAC_peaks-q1.3.narrowPeak.gz /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/GSM7508788_CS19-12696-ATAC_peaks-q1.3.narrowPeak.gz /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/GSM7508789_CS22-12498-ATAC_peaks-q1.3.narrowPeak.gz /projects/bgmp/shared/Bi623/ZoonomiaWorkshop/GSM7508790_CS23-12492-ATAC_peaks-q1.3.narrowPeak.gz"
	User time (seconds): 1.87
	System time (seconds): 4.30
	Percent of CPU this job got: 99%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:06.23

Output file is ~50 mb 
wc -l part3_bedtools_output.tsv 
1068590 part3_bedtools_output.tsv
close to predicted output

resorted using sort -V
wc -l test_part3_bedtools_output.tsv 
1068593 test_part3_bedtools_output.tsv
similar number

resorted everything per bedtools' suggestion (no sort -V; so in chromosome chr1, chr 10-19, chr2.... order):
wc -l test_part3_bedtools_output.tsv 
1068593 test_part3_bedtools_output.tsv
still off

Issue due to sort command in R script for part 2; corrected and reran:

wc -l part3_bedtools_output.tsv 
1068691 part3_bedtools_output.tsv

OUTPUT CORRECT

Sites with the most hits in col 4 (num) that have RoCCs and Cranio listed in column 5 (list) are likely of the most interest, since those RoCCs are observed on open chromatin as determined by ATAC-seq, further indicating functional importance. "Cranio" seems to have a lower frequency, which makes sense, given how few regions were in that file.

Challenge:
Downloaded datasets of cleft lip and associated traits' associated SNPs from European Bioinformatics Institute:
https://www.ebi.ac.uk/gwas/efotraits/HP_0000202 (Referred to as orofacial going forward)
https://www.ebi.ac.uk/gwas/efotraits/EFO_0003959 (Referred to as cleft lip going forward)

Steps to prep for prepping for Multiinter:
Selected Chromosome, Start site, Trait columns
Removed NAs 
renamed columns to match conventions of other files
Reordered columns
Added "chr" to the beginning of each chromosome entry
Added Stop column (original files are for SNPs and lack this column)
Order appears correct

Reran Multiinter with these files added:
Command being timed: "pixi run bedtools multiinter -names RoCCs Cranio GSM86 GSM87 GSM88 GSM89 GSM90 Orofacial Cleft -header -i sorted_pt3inputs/RoCCs.tsv sorted_pt3inputs/Cranio.tsv sorted_pt3inputs/GSM86.tsv sorted_pt3inputs/GSM87.tsv sorted_pt3inputs/GSM88.tsv sorted_pt3inputs/GSM89.tsv sorted_pt3inputs/GSM90.tsv sorted_pt3inputs/Orofacial.tsv sorted_pt3inputs/Cleft.tsv"
	User time (seconds): 1.93
	System time (seconds): 4.29
	Percent of CPU this job got: 97%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:06.36
	
wc -l challenge_bedtools_multiinter_out.txt 
1069474 challenge_bedtools_multiinter_out.txt

-Only slightly longer than original file, which makes sense given the relatively small sizes of the added files and hopefully overlap with regions noted in other steps. 


Part 4: plotgardener
Data filtering and Plotting packages used:
library(BiocManager)
library(plotgardener)
library(plyranges)
library(grid)
library("grid")
library("tidyverse")
library("dplyr")

Note: "Cranio" data set is clinical variants, also known as "ClinVars" in the assignment. Both are used here. 

Downloaded multiinter input files to desktop for plotting
Filtered Multiinter output for sites where ClinVars and Orofacial or Cleft sites overlapped
	No results

Filtered for sites where "RoCCs" and "Orofacial" GWAS challenge set overlapped
	Orofacial and Cleft datasets had significant overlap, so only 1 selected for simplicity.

Searched multiinter file visually at the sites returned:

Originally elected the region of chr11 Start site 66049234-66052490 (3256 bp), since this region included ClinVars and much of the epigenetic data.
However, to plot the challenge data, I decided to use the region surrounding chr12 56041522-56042305 (783 bp), since this also included the cleft and orofacial SNPs.
	I decided to add on additional bases on each end to be over 1kb plotted. 
Final choice:chr9 109013383-109015185; 1802 bases (no clinvars present)
	Only region with region in RoCCs and cleft palate/orofacial datasets with significant epigenetics hits
	however, no clinvars in this region

Data manipulation for plotgardener:
For GWAS data, manually edited start and stop sites to convert SNPs to 10 bp to make them show up on the plot. 
Had to cut 4th column from Cranio.tsv file.

Plotgardener instructions page for gene track:
https://phanstiellab.github.io/plotgardener/reference/plotGenes.html


Read RoCCs, Cranio, Orofacial, Cleft datasets in as bed files after reformatting due to them matching this format. 
Read epigenetics files in as read_narrowpeaks()

Used hg38 genome, as this was used as the genome in all of the datasets. 
Selected purple color gradient for epigenetics datasets and red for challenge datasets for cleft palate SNPs.
RoCCs were blue to differentiate from other datasets. 

Within the region, epigenetics datasets overlapped on CTNNAL1 gene.
RoCCs and cleft/orofacial overlapped separately on a different gene ~1.5 kb downstream of CTNNAL1 gene.






