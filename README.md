Genome assembly
===============
## Simulation of raw reads using Art Illumina (optional)
https://www.niehs.nih.gov/research/resources/software/biostatistics/art/index.cfm

## De novo sequence assembly using ABySS 
https://github.com/bcgsc/abyss 

Abyss script sent to UMN’s supercomputer for Gator assembly with a kmer of 80 that yielded the best results 

    #!/bin/bash -l
    #PBS -l walltime=96:00:00,nodes=1:ppn=16,mem=1000gb
    #PBS -q amd2tb 
    module load conda
    conda activate wormlab
    abyss-pe name=gator k=80 np=16 in='../shared/paired_end_reads/171138*'


## Evaluation of assembly using Quast 
https://github.com/ablab/quast

Quast script sent to UMN’s supercomputer for evaluation of the Gator (k=80) assembly compared with the NCBI’s assembly of the same species

    #!/bin/bash -l
    #PBS -l walltime=12:00:00,nodes=1:ppn=10,mem=500gb
    #PBS -q amd2tb
    module load conda
    conda activate wormlab
    quast.py -o ~/Gator/Gator80/Quast80 --large -t 10 ~/Gator/Gator80/gator-scaffolds.fa -r ~/Gator/Gator80/gator_ref.fna           


## Reference-guided scaffolding of genome using RagTag to near chromosomal-scale assembly
https://github.com/malonge/RagTag/wiki/Scaffolding 

(on wormlab) Running RagTag on caiman(80) with gator as reference:

    ~/ragtag$ ragtag.py scaffold ../Gator/Gator/Gator80/gator_ref.fna caiman-scaffolds.fa

Running RagTag on caiman(80) with croc as reference, later naming it croc_vs_caiman: 

    ragtag.py scaffold ../Gator/croc/GCA_001723895.1_CroPor_comp1_genomic.fna  caiman-scaffolds.fa -o ragtag_output_croc

Running ragtag again with this generated file (croc_vs_caiman) with gator as reference: 

    ragtag.py scaffold ~/Gator/Gator/Gator80/gator_ref.fna croc_vs_caiman.fasta -o ragtag_output_crocVgator

This final file, ~/ragtag/ragtag_output_croc/ragtag_output_crocVgator/ragtag.scaffolds.fasta, is close to the final draft. The same process should be repeated with the other 2 individuals (then all 3 drafts will be used in the gap closing step?)


## Using the generated files for each of the three individuals to fill in the gaps?

## Repeat masking with RepeatMasker  
http://www.repeatmasker.org/ https://www.biostars.org/p/169981/#170029

    #!/bin/bash -l
    #PBS -l walltime=72:00:00,nodes=1:ppn=12,mem=500gb
    #PBS -q amd2tb
    module load conda
    conda activate wormlab                                                                               RepeatMasker -pa 12 -species alligatoridae /home/okam0009/dopki010/Caiman/ragtag/ragtag.scaffolds80.fasta


## For re-running with the IDBA assembly:
 https://www.biostars.org/p/93688/

    RepeatMasker -e hmmer -pa 12 -species alligator /scratch.global/okam_gator/ragtag.scaffolds.fasta
    

## Genome annotation with liftoff 
https://github.com/agshumate/Liftoff

Using gator protein file as reference (https://www.ncbi.nlm.nih.gov/assembly/GCF_000281125.3/)


## Quality assessment with BUSCO 
https://busco.ezlab.org/busco_userguide.html#running-busco

