INSTAGRESS_project
==================

Instagress = **Insta**ntaneous intro**gress**ion 

Experiments were performed by Soichi Inagaki.

We received nine DNA samples with unique IDs FRAG01448-FRAG01456.

PCR-libraries were made using KAPA Hyper kit and 150PE sequencing performed on HiSeq4000 by QB3.

-----

1. Alignment of reads using bwa
-------------------------------

Raw reads were interleaved, filtered and shortened to 50bp (reason for this is that we get better mapping than 150bp reads).

Aligned by using bwa-do-all on a database that has the TAIR10 reference genome as well as the T-DNA insert sequence from pCAMBIA. 

*Pairs were mapped as single reads 50bp. This is important because bwa's paired-end function with salvage does not consider mis-mapping due to 'Far Pairs' and in our experience, longer reads that span unique junctions prevent proper mapping resulting in fewer informative reads.

        bwa-doall-vModules-current.py -d ~ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.fa -O -t 16
        


###FarPairs Analysis

This is a cursory look at the unique breakpoint junctions for T-DNA insertion sites. This step facilitates the de novo assembly of reads from target regions.

        for i in *.sam
        do
          SegregatePE.py $i 5000
        done
        
        This is an example output from the script:
        
          Total:
          8278497
          TruePairs:
          4915803
          Average distance of TruePairs:
          284.656289929
          FarPairs:
          80677
          OddPairs:
          415329
          BadPairs:
          2866688
          Unpaired:
          0
          CheckTotal:
          8278497
        


Because we know that the cut sites were on Chr2 this is an example on how this data is useful:

        
        grep pCAMBIA *_FarPairs.txt | grep Chr2
          K00188:47:H3NV2BBXX:3:1108:9211:41088   16      Chr2    75666   16      pCAMBIA_fwa     73      No      NA      TCGCTGGGGCCTCCGCCCACATCTACTCTTCCCAGCCCTTCCACGATGTC   GTACTGAATTAACGCCGAATTAATTCGGGGGATCTGGATTTTAGTACTGG
          K00188:47:H3NV2BBXX:3:1108:6025:48896   0       Chr2    75278   16      pCAMBIA_fwa     220     No      NA      GTGTTACAGACTAGTTTACTGAGTCCGGTGGGTTACAGGGCGGGCGGCGA   CCCTATAGGAACCCTAATTCCCTTATCTGGGAACTACTCACACATTATTA
          K00188:47:H3NV2BBXX:3:1111:29061:14361  16      pCAMBIA_fwa     90      16      Chr2    75459   No      NA      AATTAATTCGGGGGATCTGGATTTTAGTACTGGATTTTGGTTTTAGGAAT   ATTCTAAGGCCCAAACCATTATATTAAGTTGGTTGGTCCACCAAAGTCAG
          K00188:47:H3NV2BBXX:3:1113:28726:10229  16      pCAMBIA_fwa     69      0       Chr2    75216   No      NA      TAATGTACTGAATTAACGCCGAATTAATTCGGGGGATCTGGATTTTAGTA   GCCCCTCCATTTTCGGAACTTGCTCTTCTCCAAGCTAAATCTTATC
          K00188:47:H3NV2BBXX:3:1123:20851:6501   16      pCAMBIA_fwa     63      16      Chr2    75627   No      NA      CGTTTTTAATGTACTGAATTAACGCCGAATTAATTCGGGGGATCTGGATT   CGCTGCTGTGCCCATTTCTTCTATCATTACACTTTAGAATCGCTGGGGCC
          K00188:47:H3NV2BBXX:3:1126:19116:46328  0       Chr2    75259   16      pCAMBIA_fwa     235     No      NA      ATCTGAAATAGAAGATATTGTGTTACAGACTAGTTTACTGAGTCCGGTGG   AATTCCCTTATCTGGGAACTACTCACACATTATTATGGAGAAACTCGAGT
          K00188:47:H3NV2BBXX:3:1206:8136:27408   0       Chr2    75180   16      pCAMBIA_fwa     95      No      NA      ATCTACGAACCTGTGGGTGGTGGATCTATACACGACGCCCCTCCATTTTC   ATTCGGGGGATCTGGATTTTAGTACTGGATTTTGGTTTTAGGAAT
          K00188:47:H3NV2BBXX:3:1208:2767:23557   16      pCAMBIA_fwa     224     16      Chr2    75450   No      NA      ATAGGAACCCTAATTCCCTTATCTGGGAACTACTCACACATTATTATGGA   GCCCAAACCATTCTAAGGCCCAAACCATTATATTAAGTTGGTTGGTCCAC

-----

2. Finding instagressed T-DNA sites
-----------------------------------

Taking the LB and RB of the T-DNA insert, we will start assembling breakpoint junctions from fq reads.

        Make a bins_pCAMBIA.txt file
        
          Chrom BinStart  BinEnd
          pCAMBIA_fwa     0       500 
          pCAMBIA_fwa     6368    6868
        
        Run binsearch algorithm
          
          batch-specific-junction-bin-search.py -b bins_pCAMBIA.txt -Q
        
        Perform PRICE assembly on these extracted reads, followed by BLASTn
        
          batch-uninterleaver-PRICE-Blaster.py ~ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.blastn
        



