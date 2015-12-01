INSTAGRESS_project
==================

Instagress = **Insta**ntaneous intro**gress**ion 

Experiments were performed by Soichi Inagaki.

We received nine DNA samples FRAG01448-FRAG01456.

PCR-libraries were made using KAPA Hyper kit and 150PE sequencing performed on HiSeq4000 by QB3.

-----

1. Alignment of reads using bwa
-------------------------------

Raw reads were interleaved, filtered and shortened to 50bp (reason for this is that we get better mapping than 150bp reads)

Aligned by using bwa-do-all on three genomes: TAIR10, pPVL02 and pCAMBIA

        bwa-do-all
        

-----

2. Finding instagressed T-DNA sites
-----------------------------------
