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
Second loop to make second dictionary (merge_dict) removes reads less than a certain length (initially just singlets) and combines RoCCs separated by 1 bp
third loop for printing


Note: had to convert numbers (base positions) in dictionaries to ints in order to do math
After fixing typos and ordering and data types of dictionary, successfully ran on test.tsv to output testout.tsv










Part 2;
