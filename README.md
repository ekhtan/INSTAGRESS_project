INSTAGRESS_project
==================

Instagress = **Insta**ntaneous intro**gress**ion 

Experiments were performed by Soichi Inagaki.

We received nine DNA samples FRAG01448-FRAG01456.

PCR-libraries were made using KAPA Hyper kit and 150PE sequencing performed on HiSeq4000 by QB3.

-----

1. Alignment of reads using bwa
-------------------------------

Raw reads were interleaved, filtered and shortened to 50bp (reason for this is that we get better mapping than 150bp reads).

Using example for FRAG01451 which looks to be a good candidate for instagression (based on prior analysis).

Aligned by using bwa-do-all on TAIR10 that includes the T-DNA insert from pCAMBIA

        bwa-doall-vModules-current.py -d combined_pCAMBIA_TAIR10.fa -O -m pb -t 16
        

Used both 150bp or 50bp for alignment and it appears that the 150bp has better resolution of T-DNA edges, so used that for looking at FarPair junctions.

###FarPairs Analysis

-----

2. Finding instagressed T-DNA sites
-----------------------------------
