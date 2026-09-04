File location:
/projects/bgmp/gmich/bioinfo/Bi623/gmichaels4444-bit-Bi623-Project-1


Software info:
Python version 3.14 (Pixi installed)

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







Part 2

Plotting: Used R on Talapas: https://ondemand.talapas.uoregon.edu/pun/sys/dashboard/batch_connect/sys/rstudio/session_contexts/new With Talapas R module, most recent R, and settled on 2-4h upon resetting
