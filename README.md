INSTAGRESS_project
==================

Instagress = **Insta**ntaneous intro**gress**ion 

Nine DNA samples with unique IDs FRAG01448-FRAG01456 were processed.

PCR-libraries were made using KAPA Hyper kit and 150PE sequencing performed on HiSeq4000 by QB3.

-----

1. Alignment of reads using bwa
-------------------------------

Raw reads were interleaved, filtered and shortened to 50bp.

Aligned by using bwa-do-all on a database that has the TAIR10 reference genome as well as the T-DNA insert sequence from pCAMBIA. 

*Pairs were mapped as single reads for this analysis. This is important because bwa's paired-end function with salvage does not consider mis-mapping due to 'Far Pairs' and in our experience, and using longer reads that span unique junctions prevent proper mapping resulting in fewer informative reads (see data below).

        bwa-doall-vModules-current.py -d ~ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.fa -O -t 16
        

Hits to pCAMBIA based on mapping using 50bp vs 150bp is shown here.

        
        Hits to pCAMBIA using 50bp: grep -c pCAM *.sam
        FRAG01448_aln.sam:174
        FRAG01449_aln.sam:28
        FRAG01450_aln.sam:106
        FRAG01451_aln.sam:1771
        FRAG01452_aln.sam:593
        FRAG01453_aln.sam:1170
        FRAG01454_aln.sam:315
        FRAG01455_aln.sam:578
        FRAG01456_aln.sam:227
        
        Hits to pCAMBIA using 150bp: grep -c pCAM *.sam
        FRAG01448_aln.sam:147
        FRAG01449_aln.sam:24
        FRAG01450_aln.sam:99
        FRAG01451_aln.sam:1722
        FRAG01452_aln.sam:522
        FRAG01453_aln.sam:1127
        FRAG01454_aln.sam:289
        FRAG01455_aln.sam:520
        FRAG01456_aln.sam:212
        

###FarPairs Analysis

This is a cursory look at the unique breakpoint junctions for T-DNA insertion sites. This step facilitates the de novo assembly of reads from target regions.

        for i in *.sam
        do
          SegregatePE.py $i 5000
        done
        

This is an example output from the script for FRAG01451:

        
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

Taking the LB and RB of the T-DNA insert, we will start assembling breakpoint junctions from the fq reads from 500bp from the LB and RB of the pCAMBIA T-DNA sequence.

Make a bins_pCAMBIA.txt file that look like this:

        
        Chrom BinStart  BinEnd
        pCAMBIA_fwa     0       500 
        pCAMBIA_fwa     6368    6868
        

Run binsearch algorithm. Note that the full 150bp PE fq files are used in conjunction with sam files that were mapped based on 50bp reads.

        
        batch-specific-junction-bin-search.py -b bins_pCAMBIA.txt -Q
        

Perform PRICE assembly on these extracted reads, followed by BLASTn.

        
        batch-uninterleaver-PRICE-Blaster.py ~ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.blastn
        

Example of Blastn ouput that shows the potential integration sites, which matches the original T-DNA integration sites

        # BLASTN 2.2.31+
        # Query: contig_2 (841nt) unchanged
        # Database: /home/ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.blastn
        # Fields: query id, subject id, % identity, alignment length, mismatches, gap opens, q. start, q. end, s. start, s. end, evalue, bit score
        # 3 hits found
        contig_2        pCAMBIA_fwa     100.00  422     0       0       420     841     6597    6176    0.0     780
        contig_2        pCAMBIA_fwa     99.13   231     2       0       349     579     47      277     4e-115  416
        contig_2        Chr2    100.00  324     0       0       1       324     75715   75392   3e-170  599
        # BLASTN 2.2.31+
        # Query: contig_3 (621nt) unchanged
        # Database: /home/ekhtan/INSTAGRESS/genomes/combined_pCAMBIA_TAIR10.blastn
        # Fields: query id, subject id, % identity, alignment length, mismatches, gap opens, q. start, q. end, s. start, s. end, evalue, bit score
        # 3 hits found
        contig_3        pCAMBIA_fwa     100.00  488     0       0       134     621     21      508     0.0     902
        contig_3        pCAMBIA_fwa     98.75   160     2       0       231     390     6597    6438    8e-76   285
        contig_3        Chr2    100.00  119     0       0       1       119     75259   75377   2e-56   220
        

Original insertion site is at Chr2:752,377 and Chr2:752,392 at to LB of pCAMBIA, suggesting the T-DNA is inserted as a tandem inverted repeat.

If real, that means the introgression is via the HR pathway/gene conversion (?), and not through NHEJ-associated cut and paste.

Two out of the nine lines sequenced (FRAG01451 - plant #12 and FRAG01453 - plant #18) have junction support for an instagress-event on the targeted site on Chr2.

-------

3. Caveats
----------

The sgRNA cut sites also contain ~100bp of Chr2 as part of 'donor' pCAMBIA T-DNA from GFP-tailswap parent. Therefore, reconstruction using 150 bp PE reads and PRICE assembly did not yield a clear picture although the majority shows the event as described above.

Solution: PCR oligos 500bp outside the PAM sites for Sanger sequencing has been ordered.

