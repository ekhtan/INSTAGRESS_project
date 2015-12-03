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

Aligned by using bwa-do-all on TAIR10 that includes the T-DNA insert from pCAMBIA

        bwa-doall-vModules-current.py -d ~ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.fa -O -m pb -t 16
        

Used both 150bp or 50bp for alignment and it appears that the 150bp has better resolution of T-DNA edges, so used that for looking at FarPair junctions.

###FarPairs Analysis

        SegregatePE.py FRAG01451_aln.sam 5000
        
        This is the output from the script:
        
          Total:
          8278497
          TruePairs:
          4104182
          Average distance of TruePairs:
          211.708358206
          FarPairs:
          75078
          OddPairs:
          573765
          BadPairs:
          3525472
          Unpaired:
          0
          CheckTotal:
          8278497
        
        grep -c pCAMBIA FRAG01451_aln.sam*
          FRAG01451_aln.sam:1722
          FRAG01451_aln.sam_FarPairs.txt:7
          FRAG01451_aln.sam_OddPairs.txt:34
          FRAG01451_aln.sam_TruePairs.txt:110

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
        
        Perform PRICE assembly on these extracted reads
        
          

