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

Sites with the most hits in col 4 (num) that have RoCCs and Cranio listed in column 5 (list) are likely of the most interest, since those ROCCs are observed on open chromatin as determined by ATAC-seq, further indicating functional importance. "Cranio" seems to have a lower frequency, which makes sense, given how few regions were in that file



