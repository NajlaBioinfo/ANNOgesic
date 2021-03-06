ANNOgesic's subcommands
===============

In general, the subcommands need at least one argument - the analysis
folder. If it is not given, ANNOgesic assumes the current
folder as the analysis folder.

The format of filename
--------------------
In order to recognize file types as well as relation of strain name and features, 
please use following principle of filename designation:

The genome filenames should be the same as the annotation file designation, i.e.
``NC_007795.fa, NC_007795.gff, NC_007795.ptt, NC_007795.rnt, NC_007705.gbk``.

For all input gff files for ANNOgesic please use following format:
``$STRAINNAME_$FEATURE.gff``. For example, ``NC_007795_TSS.gff, NC_007795_transcript.gff``.

Possible feature names are:

===============  ===========================
Feature name     meaning
---------------  --------------------------- 
TSS              transcription starting site
processing       processing site
transcript       transcript assembly
sRNA             small RNA
sORF             small open reading frame
term             terminator
5UTR             5'UTR
3UTR             3'UTR
circRNA          circular RNA
riboswitch       riboswitch
RNA_thermometer  RNA_thermometer
CRISPR           CRISPR
===============  ===========================

Please avoid ``|`` in the filename, strain name of Gff3 files or fasta file.

The input format of libraries for running ANNOgesic
-----------------------------

Some ``ANNOgesic`` modules require certain library information. Please use following format:

``$LIBRARY_FILENAME:$LIBRARY_TYPE:$CONDITION:$REPLICATE:$STRAND``

``$LIBRARY_FILENAME`` means the ``.wig`` file.

``$LIBRARY_TYPE`` can be ``tex`` (TEX+) or ``notex`` (TEX-) or ``frag`` (fragmented).

``$CONDITION`` is the index of conditions. Please use 1, 2, 3, ... to represent different conditions.

``$REPLICATE`` is the index of replicates. Please use a, b, c, ... to represent different replicates.

``$STRAND`` is the strand of wiggle file. Please use + or -.

All libraries should be seperated by colons and listed in one line, like shown in the example above, i.e.

for TEX +/- treated libraries:

::

  TSB_OD_0.2_TEX_reverse.wig:tex:1:a:-,\
  TSB_OD_0.5_TEX_reverse.wig:tex:2:a:-,\
  TSB_OD_0.2_TEX_forward.wig:tex:1:a:+,\
  TSB_OD_0.5_TEX_forward.wig:tex:2:a:+,\
  TSB_OD_0.2_reverse.wig:notex:1:a:-,\
  TSB_OD_0.5_reverse.wig:notex:2:a:-,\
  TSB_OD_0.2_forward.wig:notex:1:a:+,\
  TSB_OD_0.5_forward.wig:notex:2:a:+,

or for fragmented libraries:

::

  fragmented_forward.wig:frag:1:a:+,fragmented_reverse.wig:frag:1:a:-

If only conventional RNA-seq data without fragementation or TEX treated can be provided, 
it can still be assigned to fragmented libraries and used by ANNOgesic.
However, it may influnece the results.

The format of sRNA database
-----------------------------
If sRNA database is used for running ``srna``, please follow the format otherwise the output will become
nonsense. The format is 

::

  $ID|$STRAIN|$SRNANAME

for example:

::

  srn_4840|S._aureus_NCTC8325|RsaOV

You can download sRNA database `BSRD <http://www.bac-srna.org/BSRD/index.jsp>`_ from our
`Github <https://github.com/Sung-Huan/ANNOgesic/tree/master/database>`_ easily.

Definition of reference strain and target strain
------------------------------
"target strain" represents the strain which the user want to annotate.
"reference strain" represents the strain which is similar to the "target strain".
If the user neither has fasta nor genome annotation file of "target strain", 
ANNOgesic can generate them if "reference strain" and mutation information 
are provided by the user.

Riboswitch and RNA thermometer dataset of Rfam
----------------------------
For riboswitch and RNA thermometer detection, the information of riboswitch and RNA thermometer in Rfam are required
The input format is following.

======== ==== ==========================
#Rfam_ID Name Description
-------- ---- --------------------------
RF00162  SAM  SAM riboswitch box leader
RF00059  TPP  TPP riboswitch THI element
======== ===  ==========================

All columns are separated by ``tab``. You can download the data from our 
`Github <https://github.com/Sung-Huan/ANNOgesic/tree/master/database>`_.

create
-----

``create`` generates the folders for analysis. Once created, please move the required files 
into the corresponding folders.

The folders are following:

**BAMs:** For ``.bam`` files. ``BAMs_map_reference`` 
is for the ``.bam`` files which mapped on "reference strain".
``BAMs_map_target`` is for the ``.bam`` files which are mapped on "target strain".

**database:** For all databases.

**manual_TSS:** If the manual detected transcription starting sites (TSSs) can be provided,
it can be stored here for running ``TSS_optimization`` or merging 
the automatic predicted ones and manual detected ones. Please use gff3 format.

**manual_processing_site:** It is similar to ``manual_TSS``, it is for 
processing sites.

**mutation_table:** If the mutation table between "reference strain" and 
"target strain" is provided, please put the file here. Please check 
the section of ``get_target_fasta`` for the format of 
mutation table.

**reads:** For running ``circrna`` with mapping reads by ANNOgesic,
please put the reads here. ``.bzip2`` and ``.gzip`` as input is accepted.
       
**reference:** For annotation files and fasta files of "reference strain". 
If they can be downloaded from NCBI, the files can also be obtained via running ``get_input_files``.

**riboswitch_ID:** For storing the file which contains all the Rfam IDs of riboswitch.
For format details, please check the section of 
``Riboswitch and RNA thermometer dataset of Rfam``.

**RNA_thermometer_ID:** For storing the file which contains all the Rfam IDs of RNA thermometer.
For format details, please check the section of
``Riboswitch and RNA thermometer dataset of Rfam``.

**wigs:** For wiggle files. Based on the methods of RNA-Seq, wiggle files can be stored in  
``fragment`` (fragmented libraries) or ``tex_notex`` (TEX +/- treated libraries).


- **Arguments**

::

  usage: ANNOgesic.py create [-h] project_path
  
  positional arguments:
    project_path  Name/path of the project.
  
  optional arguments:
    -h, --help    show this help message and exit

get_input_files
--------------

``get_input_files`` is the subcommand for downloading required files (fasta, annotation files) from NCBI. 
Therefore, the web address of the reference genome in NCBI needs to be assigned. For example,
ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF_000013425.1_ASM1342v1
Then, the user can assign the file type for download.


- **Reqired information**

**FTP source:** The IP of NCBI.

- **Arguments**


::

    usage: annogesic get_input_files [-h] [--FTP_path FTP_PATH] [--ref_fasta]
                                     [--ref_gff] [--ref_ptt] [--ref_rnt]
                                     [--ref_gbk] [--convert_embl] [--for_target]
                                     [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --FTP_path FTP_PATH, -F FTP_PATH
                            Path of NCBI FTP where can download the required
                            files.
      --ref_fasta, -f       Download fasta files of the reference. Default is
                            False.
      --ref_gff, -g         Download gff files of the reference. Default is False.
      --ref_ptt, -p         Download ptt files of the reference. Default is False.
      --ref_rnt, -r         Download rnt files of the reference. Default is False.
      --ref_gbk, -k         Download genbank files of the reference. Default is
                            False.
      --convert_embl, -e    Convert gbk to embl files of the reference. Default is
                            False.
      --for_target, -t      If the required files of the query strain can be
                            downloaded from NCBI (you won't modify the genome),
                            The files can be stored in target folder in stead of
                            the reference folder.

- **Output files**

Output files will be stored in ``$ANNOgesic_folder/input/reference`` if ``--for_target`` is False.
Output files will be stored in ``$ANNOgesic_folder/output/target`` if ``--for_target`` is True.

Output folder names are following:

**fasta:** Fasta files.

**annotation:** Annotation files.

get_target_fasta
--------------

``get_target_fasta`` is the subcommand for generating fasta files of "target strain" from 
"reference strain". The format of mutation table is as following:

==============  =========  ============  ========  =========  ====================  =============  ====  ============
 #reference_id  target_id  reference_nt  position  target_nt  impact_of_correction  locus_tag      gene  Description 
--------------  ---------  ------------  --------  ---------  --------------------  -------------  ----  ------------
 NC_007795.1     HG003     a             333       c                                SAOUHSC_00002  dnaA  XXXXXX      
 NC_007795.1     HG003     t             543       \-          deletion                                  YYYYYY      
 NC_007795.1     HG003     \-            600       g           insertion            SAOUHSC_00132                    
==============  =========  ============  ========  =========  ====================  =============  ====  ============

If the titles of columns is presented on the top, they need to start with ``#``. 
Each column is separated by ``tab``. If the mutation type is deletion or insertion, 
the user can type ``-`` to represent them. The information of ``target_id``, ``reference_id``,
``reference_nt``, ``position``, ``target_nt`` is required. The other columns can be blank. 
Please use ``tab`` to separate all columns including blank ones.

If no mutation information is provided, ``SNP_calling`` can be used for detecting mutations. 
(one module of ``ANNOgesic``). Please check the section of ``SNP_calling``.

- **Required files**

**Fasta files of reference genome**

**Mutation table:** Contains the information of mutations between reference and target strain.

- **Arguments**

::

    usage: annogesic get_target_fasta [-h] [--ref_fasta_folder REF_FASTA_FOLDER]
                                      [--mutation_table MUTATION_TABLE]
                                      [--output_format OUTPUT_FORMAT]
                                      [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --ref_fasta_folder REF_FASTA_FOLDER, -r REF_FASTA_FOLDER
                            Path of the fasta folder.
      --mutation_table MUTATION_TABLE, -m MUTATION_TABLE
                            Path of the mutation table which stores the mutation
                            information between the target strain and reference
                            strain.
      --output_format OUTPUT_FORMAT, -o OUTPUT_FORMAT
                            Please assign the filename and the strain name which
                            should be included in output files. For example:
                            FILE1:strain1_and_strain2,FILE2:strain3. FILE1 is a
                            output fasta file which include the information of
                            strain1 and strain2 (import multi-strains to one file
                            should be separated by "_and_".) And FILE2 is for
                            strain3. The multiple output files are splitted by
                            comma.

- **Output files**

**Fasta files of target genome**: This files are stored in ``$ANNOgesic_folder/output/target/fasta``.

annotation_transfer
-----------

``annotation transfer`` is the subcommand for transfering the annotation from "reference strain" 
to "target strain". To achieve this, `RATT <http://www.sanger.ac.uk/resources/software/pagit/>`_ 
is integrated in ANNOgesic. The higher similarity between "reference strain" and "target strain" are, 
the more precise the perfomance is. Before running ``annotation transfer``, 
please run ``source $PAGIT_HOME/sourceme.pagit`` first. it will modify the path for executing RATT. 
If you use Dockerfile to execute ANNOgesic, the path modification can be skipped.

- **Required tools**

`RATT <http://www.sanger.ac.uk/resources/software/pagit/>`_.

- **Required files**

**Annotation files of the reference strain**: Genbank files of the reference genome.

**Fasta files of the reference strain**

**Fasta files of the target strain**

- **Arguments**

::

    usage: annogesic annotation_transfer [-h] [--RATT_path RATT_PATH]
                                         [--compare_pair COMPARE_PAIR]
                                         [--element ELEMENT]
                                         [--transfer_type TRANSFER_TYPE]
                                         [--ref_embl REF_EMBL] [--ref_gbk REF_GBK]
                                         [--ref_fasta REF_FASTA]
                                         [--target_fasta TARGET_FASTA]
                                         [--convert_to_gff_rnt_ptt]
                                         [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --RATT_path RATT_PATH
                            Path of the start.ratt.sh file of RATT folder. Default
                            is start.ratt.sh.
      --compare_pair COMPARE_PAIR, -p COMPARE_PAIR
                            Please assign the name of strain pairs. ex.
                            NC_007795:NEW_NC_007795. The reference strain is
                            NC_007795 and the target strain is NEW_NC_007795. the
                            assigned names are the strain names in the fasta file
                            (start with ">"), not the filename of fasta file. If
                            multiple strains need to be assigned, please use comma
                            to separate the strains.
      --element ELEMENT, -e ELEMENT
                            --element will become the prefix of all output file.
      --transfer_type TRANSFER_TYPE, -t TRANSFER_TYPE
                            The transfer type for running RATT. (For the details,
                            please refer to the manual of RATT.) Default is
                            Strain.
      --ref_embl REF_EMBL, -re REF_EMBL
                            The folder of embl files.
      --ref_gbk REF_GBK, -rg REF_GBK
                            If you have no embl file, you can assign the folder of
                            genbank files. The genbank can be ended by .gbk, .gbff
                            or .gb
      --ref_fasta REF_FASTA, -rf REF_FASTA
                            The folder of reference fasta files.
      --target_fasta TARGET_FASTA, -tf TARGET_FASTA
                            The folder of target fasta files.
      --convert_to_gff_rnt_ptt, -g
                            Convert the annotation to gff, rnt and ptt. Default is
                            False.

- **Output files**

Output files from `RATT <http://www.sanger.ac.uk/resources/software/pagit/>`_
will be stored in ``$ANNOgesic_folder/output/annotation_transfer``.

**Annotation files** (``.gff``, ``.ptt``, ``.rnt``) will be stored in ``$ANNOgesic_folder/output/target/annotation``.

snp
-------

``snp`` can analyze the alignment files and fasta files to detect mutations by running 
`Samtools <https://github.com/samtools>`_ and `Bcftools <https://github.com/samtools>`_. 
There are multiple programs which can be applied to detect mutations 
(with BAQ, without BAQ and extend BAQ) and there are multiple flag options to set filters
(QUAL, DP, DP4, etc.). Moreover, ``snp`` can also be used for generating the fasta file of 
"target strain".

- **Required files**

`Samtools <https://github.com/samtools>`_.

`Bcftools <https://github.com/samtools>`_.

- **Required tools**

**BAM files:** BAM files from fragmented libraries or TEX +/- treated libraries both can be accepted.

**Fasta files of the reference strain** or **Fasta files of the target strain**

- **Arguments**

::

    usage: annogesic snp [-h] [--samtools_path SAMTOOLS_PATH]
                         [--bcftools_path BCFTOOLS_PATH] [--bam_type BAM_TYPE]
                         [--program PROGRAM] [--fasta_path FASTA_PATH]
                         [--tex_bam_path TEX_BAM_PATH]
                         [--frag_bam_path FRAG_BAM_PATH] [--quality QUALITY]
                         [--read_depth_range READ_DEPTH_RANGE] [--ploidy PLOIDY]
                         [--RG_tag] [--sample_number SAMPLE_NUMBER]
                         [--caller CALLER] [--DP4_cutoff DP4_CUTOFF]
                         [--indel_fraction INDEL_FRACTION]
                         [--filter_tag_info FILTER_TAG_INFO]
                         [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --samtools_path SAMTOOLS_PATH
                            If you want to assign the path of samtools, please
                            assign here.
      --bcftools_path BCFTOOLS_PATH
                            If you want to assign the path of bcftools, please
                            assign here.
      --bam_type BAM_TYPE, -t BAM_TYPE
                            Please assign the type of BAM files. If the BAM files
                            are produced by mapping to the reference strain,
                            please keyin "reference". It is for detecting the
                            mutations between refenece strain and target strain,
                            and generate potential target fasta file. If the BAM
                            files are produced by mapping to the target strain,
                            please keyin 'target'. It is for detecting the real
                            mutations of target genome sequence.
      --program PROGRAM, -p PROGRAM
                            Please assign the program for detecting SNP of
                            transcript: 1: calculate with BAQ, 2: calculate
                            without BAQ, 3: calculate with extend BAQ. Multi-
                            programs can be executed at the same time. For
                            example: 1,2,3. Default is 1,2,3.
      --fasta_path FASTA_PATH, -f FASTA_PATH
                            Path of the genome fasta folder.
      --tex_bam_path TEX_BAM_PATH, -tw TEX_BAM_PATH
                            If the TEX+/- libraries can be provided, please assign
                            the path of the tex+/- BAM folder.
      --frag_bam_path FRAG_BAM_PATH, -fw FRAG_BAM_PATH
                            If the TEX+/- libraries can be provided, please assign
                            the path of the fragmented BAM folder.
      --quality QUALITY, -q QUALITY
                            The minimum quality of a real snp. Default is 40.
      --read_depth_range READ_DEPTH_RANGE, -d READ_DEPTH_RANGE
                            The range of read depth. If the read depth is higher
                            or lower than this range, the SNPs will be excluded in
                            output table. The format is $MIN,$MAX.
                            --read_depth_range can be assigned by different types:
                            1. real number (r), 2. the read depth based on the
                            number of samples (n) or 3. times of average read
                            depth (a). For example, n_10,a_2 means the range of
                            read depth should be higher than 10 * --sample_number
                            (if --sample_number is 2, DP value in output will be
                            higher than 20) and lower than 2 * average read
                            depth.If r_10,a_2 is assigned, it means that the
                            minimum read depth becomes exactly 10. Default is
                            n_10,a_2.
      --ploidy PLOIDY, -pl PLOIDY
                            Running for haploid or diploid genome. Default is
                            haploid.
      --RG_tag, -R          It is opposite of --ignore-RG in samtools (one BAM
                            file includes multi samples). Default is False.
      --sample_number SAMPLE_NUMBER, -sn SAMPLE_NUMBER
                            The numbers of samples. It will affect
                            --read_depth_range, --DP4_cutoff and --indel_fraction.
      --caller CALLER, -c CALLER
                            The types of caller - consensus-caller or
                            multiallelic-caller. For details, please check
                            bcftools. "c" represents consensus-caller. "m"
                            represents multiallelic-caller. Default is m.
      --DP4_cutoff DP4_CUTOFF, -D DP4_CUTOFF
                            The cutoff of DP4. DP4 is compose of four numbers:
                            high-quality reference forward bases (number 1),
                            reference reverse bases (number 2), alternate forward
                            bases (number 3) and alternative reverse bases (number
                            4). Two cutoff values can be assigned, ex: n_10,0.8.
                            First cutoff is for (number 3 + number 4). It can be
                            assigned based on 1. real number (r), 2. the read
                            depth based on the number of samples (n) or 3. the
                            times of average read depth (a). The second cutoff is
                            for (number 3 + number 4) / (number 1 + number 2 +
                            number 3 + number 4). These two cutoff is splited by
                            comma. For example, n_10,0.8 means the first cutoff
                            should be higher than 10 * --sample_number (if
                            --sample_number is 2, DP value in output will be
                            higher than 20). And the second cutoff should be
                            higher than 0.8. If r_10,0.8 is assigned, it means
                            that the first cutoff becomes exactly 10. Default is
                            n_10,0.8.
      --indel_fraction INDEL_FRACTION, -if INDEL_FRACTION
                            The cutoff of the IMF and IDV, which supports
                            insertion of deletion. For the details, please check
                            the vcf format.IDV can be assigned by three types of
                            input: 1. real number (r), 2. the read depth based on
                            the number of samples (n) or 3. the times of average
                            read depth (a). For example, n_10,0.8 means that the
                            IDV should be higher than 10 * --sample_number (if
                            --sample_number, DP value in output will be higher
                            than 20). And IMF should be higher than 0.8. If
                            r_10,0.8 is assigned, it means that IDV become exactly
                            10. Default is n_10,0.8.
      --filter_tag_info FILTER_TAG_INFO, -ft FILTER_TAG_INFO
                            Please assign the tag of output data which can be used
                            as filters. You need to assign three information: 1.
                            the tag (tag name), 2. bigger or samller (b or s) and
                            3. value for filters (integer or float). If multiple
                            tags need to be assigned, please use comma to separate
                            them. For example, "RPB_b0.1,MQ0F_s0" means that RPB
                            should be bigger than 0.1 and MQ0F should be smaller
                            than 0. Default is
                            RPB_b0.1,MQSB_b0.1,MQB_b0.1,BQB_b0.1.

- **Output files**

If ``bam_type`` is ``reference``, 
the results will be stored in ``$ANNOgesic/output/SNP_calling/compare_reference``. 
If ``bam_type`` is ``target``, the results are stored in ``$ANNOgesic/output/SNP_calling/validate_target``.

The output folders and results are following:

**SNP_raw_output:** Stores output tables which be only considered read depth and QUAL.

	**VCF Table (only consider read depth and QUAL):** Filename is ``$STRAIN_$PROGRAM.vcf``.

**SNP_table:** Stores two types of output tables

        **VCF Table (consider all filters):** Filename is ``$STRAIN_$PROGRAM_best.vcf``.

        **Index of fasta files:**: Filename is ``$STRAIN_$PROGRAM_seq_reference.csv``.
        The meaning of this file is like following example:

::

  Staphylococcus_aureus_HG003     1632629 .       AaA     AA      57      .
  Staphylococcus_aureus_HG003     1632630 .       aA      a       57      .
  Staphylococcus_aureus_HG003     1499572 .       T       TT,TTTTT        43.8525 .

The example contains "position conflict" and "mutation conflict".
as a result, the conflicts will affect the other mutation's positions.
Therefore, it will generate four different fasta files. The first two lines are "position conflict", and 
the last line is "mutation conflict".
``$STRAIN_$PROGRAM_seq_reference.csv`` is the index for these four fasta files.

::

   1       1632629 1       1499572:TT      Staphylococcus_aureus_HG003
   1       1632629 2       1499572:TTTTT   Staphylococcus_aureus_HG003
   2       1632630 1       1499572:TT      Staphylococcus_aureus_HG003
   2       1632630 2       1499572:TTTTT   Staphylococcus_aureus_HG003

The first column is the index of the "position conflict". 
The second column is the selected position.
The third one is the index of the "mutations conflict". 
The fourth one is the selected position and nucleotides. 
The last column is the strain name.

**Potential fasta files**: Filename is ``$FILENAME_$STRIANNAME_$INDEXofPOSITIONCONNFLICT_$INDEXofMUTATIONCONFLICT.fa``, 
and it is stored in ``$ANNOgesic/output/SNP_calling/$BAM_TYPE/seqs``.
Based on the example in **Index of fasta files**, ``Staphylococcus_aureus_HG003_Staphylococcus_aureus_HG003_1_1.fa``
will be generated based on the first line of ``$STRAIN_$PROGRAM_seq_reference.csv``.
``Staphylococcus_aureus_HG003_Staphylococcus_aureus_HG003_1_2.fa`` and ll be generated based on the first line of 
``$STRAIN_$PROGRAM_seq_reference.csv`` and so forth.

**statistics**: Stores the statistic files, ex: the distribution of SNPs based on QUAL.

tsspredator(TSS and processing site prediction)
--------------

``tsspredator`` can generate the TSS and processing sites via running  
`TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_. Since the parameters can affect the 
results strongly, ``optimize_tsspredator`` can obtain the optimized parameters of 
`TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_. please check the section 
``optimize_tsspredator`` for details.

- **Required tools**

`TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_.

- **Required files**

**Wiggle files of TEX +/-:** Please check the section ``The format of libraries for import to ANNOgesic`` for assigning correct format.

**Fasta file of the reference genome**

**GFF file of the reference genome**

- **Optional input files**

**Gff file of the manual detected TSS:** If gff file of the manual detected TSSs can be provided, ``tsspredator`` can merge the manual detected TSSs
and TSSpredator predicted ones.

**Gff file of transcript:** If comparing TSSs with transcripts is required, gff files of the transcripts need to be assigned.
For the transcripts, please check the section ``transcript_assembly``.

- **Arguments**

::

    usage: annogesic tsspredator [-h] [--TSSpredator_path TSSPREDATOR_PATH]
                                 [--fasta_folder FASTA_FOLDER]
                                 [--annotation_folder ANNOTATION_FOLDER]
                                 [--wig_folder WIG_FOLDER] [--height HEIGHT]
                                 [--height_reduction HEIGHT_REDUCTION]
                                 [--factor FACTOR]
                                 [--factor_reduction FACTOR_REDUCTION]
                                 [--enrichment_factor ENRICHMENT_FACTOR]
                                 [--processing_factor PROCESSING_FACTOR]
                                 [--base_height BASE_HEIGHT]
                                 [--replicate_match REPLICATE_MATCH]
                                 [--utr_length UTR_LENGTH] [--lib LIB]
                                 [--output_prefix OUTPUT_PREFIX]
                                 [--merge_manual MERGE_MANUAL] [--statistics]
                                 [--validate_gene]
                                 [--compute_program COMPUTE_PROGRAM]
                                 [--compare_transcript_assembly COMPARE_TRANSCRIPT_ASSEMBLY]
                                 [--fuzzy FUZZY] [--cluster CLUSTER]
                                 [--partial_length PARTIAL_LENGTH]
                                 [--re_check_orphan]
                                 [--overlap_feature OVERLAP_FEATURE]
                                 [--reference_gff_folder REFERENCE_GFF_FOLDER]
                                 [--remove_low_expression REMOVE_LOW_EXPRESSION]
                                 [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --TSSpredator_path TSSPREDATOR_PATH
                            If you want to assign the path of TSSpredator, please
                            assign here. Default is /usr/local/bin/TSSpredator.jar
      --fasta_folder FASTA_FOLDER, -f FASTA_FOLDER
                            Path of the target genome fasta folder.
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            Path of the target genome gff folder.
      --wig_folder WIG_FOLDER, -w WIG_FOLDER
                            The folder of the TEX+/- wig folder.
      --height HEIGHT, -he HEIGHT
                            This value relates to the minimal number of read
                            starts at a certain genomic position to be considered
                            as a TSS candidate. Default is 0.3.
      --height_reduction HEIGHT_REDUCTION, -rh HEIGHT_REDUCTION
                            When comparing different strains/conditions and the
                            step height threshold is reached in at least one
                            strain/condition, the threshold is reduced for the
                            other strains/conditions by the value set here. This
                            value must be smaller than the step height threshold.
                            Default is 0.2.
      --factor FACTOR, -fa FACTOR
                            This is the minimal factor by which the TSS height has
                            to exceed the local expression background. Default is
                            2.0.
      --factor_reduction FACTOR_REDUCTION, -rf FACTOR_REDUCTION
                            When comparing different strains/conditions and the
                            step factor threshold is reached in at least one
                            strain/condition, the threshold is reduced for the
                            other strains/conditions by the value set here. This
                            value must be smaller than the step factor threshold.
                            Default is 0.5.
      --enrichment_factor ENRICHMENT_FACTOR, -ef ENRICHMENT_FACTOR
                            This is the minimal enrichment factor. Default is 2.0.
      --processing_factor PROCESSING_FACTOR, -pf PROCESSING_FACTOR
                            This is the minimal processing factor. If untreated
                            library is higher than the treated library and above
                            which the TSS candidate is considered as a processing
                            site and not annotated as detected. Default is 1.5.
      --base_height BASE_HEIGHT, -bh BASE_HEIGHT
                            This is the minimal number of reads should be mapped
                            on TSS. Default is 0.0.
      --replicate_match REPLICATE_MATCH, -rm REPLICATE_MATCH
                            This value is the minimal number of replicates that a
                            TSS has to be detected. The format is
                            $NUMBERofCONDITION_$NUMBERofREPLICATED. If different
                            --replicate_match values need to be assigned to
                            different conditions, please use comma to separate
                            them. For example, 1_2,2_2,3_3. It means that
                            --replicate_match is 2 in number 1 and number 2
                            conditions. In number 3 condition, --replcate_match is
                            3. For assigning the same --replicate_match to all
                            conditions, just use like all_1 (--replicate_match is
                            1 in all conditions). Default is all_1.
      --utr_length UTR_LENGTH, -u UTR_LENGTH
                            The length of UTR. It is for Primary and Secondary
                            TSSs. Default is 300.
      --lib LIB, -l LIB     The libraries of TEX+/- wig files. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --output_prefix OUTPUT_PREFIX, -p OUTPUT_PREFIX
                            The output prefix of all conditions. If multiple
                            conditions need to be assigned, please use comma to
                            separate them. For example,
                            prefix_condition1,prefix_condition2.
      --merge_manual MERGE_MANUAL, -m MERGE_MANUAL
                            If gff file of the manual checked TSS is provided,
                            this function will merge manual checked ones and
                            TSSpredator predicted ones. please assign the path of
                            manual-checked TSS gff file.
      --statistics, -s      Doing statistics for TSS candidates. it will be stored
                            in statistics folder. Default is False.
      --validate_gene, -v   Using TSS candidates to validate genes in annotation
                            file. it will be store in statistics folder. Default
                            is False.
      --compute_program COMPUTE_PROGRAM, -t COMPUTE_PROGRAM
                            Which feature you want to predict, please assign "TSS"
                            or "processing_site". Default is TSS.
      --compare_transcript_assembly COMPARE_TRANSCRIPT_ASSEMBLY, -ta COMPARE_TRANSCRIPT_ASSEMBLY
                            If the folder of transcript gff file is provided, this
                            function will compare TSS and transcript to obtain the
                            overlap information. Default is False.
      --fuzzy FUZZY, -fu FUZZY
                            If --compare_transcript_assembly is provided, please
                            assign the fuzzy for comparing TSS and transcript
                            assembly. Default is 5.
      --cluster CLUSTER, -c CLUSTER
                            This value defines the maximal distance (nucleotides)
                            between TSS candidates have to be clustered together.
                            If the distance between these multiple TSSs is smaller
                            or equal to this value, only one of them will be
                            printed out. Default is 2.
      --partial_length PARTIAL_LENGTH, -le PARTIAL_LENGTH
                            The genome length for comparing between predicted TSSs
                            and manual checked TSSs. Please assign the genome
                            length of your manual detected gff file. If you want
                            to compare whole genome, please don't use this
                            function (Default). The default is comparing whole
                            genome.
      --re_check_orphan, -ro
                            If there is no information of gene or locus_tag in
                            genome annotation gff file, all TSSs will be assigned
                            to orphan TSSs by TSSpredator. The function can
                            compare TSSs with CDSs to classify the TSS correctly.
                            Default is False.
      --overlap_feature OVERLAP_FEATURE, -of OVERLAP_FEATURE
                            If processing site and TSS are overlap, you can keep
                            "TSS" or "processing_site" or "both". Default is both.
      --reference_gff_folder REFERENCE_GFF_FOLDER, -rg REFERENCE_GFF_FOLDER
                            If --overlap_feature is "TSS" or "processing_site",
                            --reference_gff_folder need to be assigned. For TSS,
                            please assign the folder of processing site. For
                            processing_site, please assign the folder of TSS. If
                            --overlap_feature is "both", please don't use this
                            function (Default). Default is None (keep both).
      --remove_low_expression REMOVE_LOW_EXPRESSION, -rl REMOVE_LOW_EXPRESSION
                            If you want to remove low expressed TSS/processing
                            site, please assign the file of manual-checked gff
                            file here. This function will remove the low expressed
                            ones based on comparison of manual-checked ones and
                            predicted ones. BE CRAEFUL: This function may remove
                            some True positives as sell. Please make sure you want
                            to do it.

- **Output files**

The results of TSS are stored in ``$ANNOgesic/output/TSS``, and the rsults of processing site 
are stored in ``$ANNOgesic/output/processing_site``.

The output folder is following:

**MasterTables:** MasterTable from `TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_.

**statistics:** Statistic files.

	**Venn Figure of TSS types:** Filename is ``TSS_venn_$STRAINNAME.png``.

	**TSS types with corresponding amounts:** Table is ``stat_TSS_class_$STRAINNAME.csv``, and Figure is ``TSS_class_$STRAINNAME.png``.

	**Conditions with corresponding amounts:** ``stat_TSS_libs_$STRAINNAME.csv`` stores all combination of Conditions with corresponding amounts.
	``TSSstatistics.tsv`` stores the number of TSS which can be detected or missing in each condition.

	**Comparing TSS with other features:** ``stat_compare_TSS_transcript_$STRAINNAME.csv`` is for comparing TSSs with transcripts.
	``stat_gene_vali_$STRAINNAME.csv`` is for comparing TSS with genome annotations.

	**Comparing manual detected TSS and predicted TSS:** In ``stat_compare_TSSpredator_manual_$STRAINNAME.csv``, the accuracy of TSS prediction can be found.

**configs**: Configuration files for running TSSpredator.

**gffs**: Output gff files of TSSs. Some useful information can be found in the tags of the attributes within the TSS gff file. 
Based on this information, we can know the details of the specific TSS. The tags are as following:

	**method:** Stores the information that this TSS is detected by manual detection or `TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_.
	
	**type:** TSS type of this TSS. It could be Primary, Secondary, Internal, Antisense or Orphan.
	
	**utr_length:** UTR length of this TSS.
	
	**associated_gene**: Which genes are associated with this TSS.
	
	**Parent:** Presents the parent transcripts of this TSS, if the user has compared TSS with the transcript.
	
	**libs:** Shows in which libraries the TSS can be detected.

Once the comparison between TSSs and genome annotation files is done, a tag will also be generated in the genome annotation gff file.

	**start_TSS:** Represents which TSSs are associated with this CDS/tRNA/rRNA.

As soon as the comparison between TSSs and transcripts is done, a tag will also be generated in the transcript gff file.

	**associated_tss:** Shows which TSSs are located on the transcript.

transcript_assembly
-------------------

``transcript_assembly`` can detect transcripts based on the coverage. Most of the transcript assembly tools are
cfocus on the assembly of eukaryotic transcript. Due to this, we compiled several proved
heuristics to detect transcripts based on the nucleotide coverage data, 
given gene annotations and several parameters that can be set by the user.

For importing the information about libraries, please refer to section of 
``The format of libraries for import to ANNOgesic``.

- **Required files**

**Wiggle files of fragmented libraries or TEX+/- treated libraries:** For importing the information about libraries, please check the section 
``The format of libraries for import to ANNOgesic``.

- **Optional input files**

**TSS gff file:** If the user wants to compare transcripts with TSSs, TSS gff file is required.

**Genome anntation gff file:** If the user wants to compare transcripts with genome anntation, genome annotation gff file is required. 
Based on the comparison, the performance of ``transcript_assembly`` can be improved.

- **Arguments**

::

    usage: annogesic transcript_assembly [-h]
                                         [--annotation_folder ANNOTATION_FOLDER]
                                         [--length LENGTH]
                                         [--tex_wig_path TEX_WIG_PATH]
                                         [--frag_wig_path FRAG_WIG_PATH]
                                         [--height HEIGHT] [--width WIDTH]
                                         [--tolerance TOLERANCE]
                                         [--tolerance_coverage TOLERANCE_COVERAGE]
                                         [-rt REPLICATES_TEX]
                                         [--replicates_frag REPLICATES_FRAG]
                                         [--tex_notex TEX_NOTEX]
                                         [--compare_TSS COMPARE_TSS]
                                         [--compare_genome_annotation COMPARE_GENOME_ANNOTATION]
                                         [--compare_feature_genome COMPARE_FEATURE_GENOME]
                                         [--TSS_fuzzy TSS_FUZZY]
                                         [--Tex_treated_libs TEX_TREATED_LIBS]
                                         [--fragmented_libs FRAGMENTED_LIBS]
                                         [--table_best]
                                         [--terminator_folder TERMINATOR_FOLDER]
                                         [--fuzzy_term FUZZY_TERM]
                                         [--max_length_distribution MAX_LENGTH_DISTRIBUTION]
                                         [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            If the genome annotation gff folder is provided, this
                            function can compare transcript with genome
                            annotation. If multiple transcipts overlap the same
                            gene, this function will merge these transcript into a
                            long one.
      --length LENGTH, -l LENGTH
                            The minimum length of the transcript after modifying
                            by genome annotation. If --annotation_folder is
                            assigned, this value will be for the final output.
                            Otherwise, --width will be the minimum length for the
                            final output. The default is 20.
      --tex_wig_path TEX_WIG_PATH, -tw TEX_WIG_PATH
                            If the TEX+/- libraries need to be assigned, please
                            assign the path of TEX+/- wig folder.
      --frag_wig_path FRAG_WIG_PATH, -fw FRAG_WIG_PATH
                            If the fragmented libraries need to be assigned,
                            please assign the path of fragmented wig folder.
      --height HEIGHT, -he HEIGHT
                            The minimum coverage of the transcript. The default is
                            10.
      --width WIDTH, -w WIDTH
                            The minimum length of the transcript without modifying
                            by genome annotation. This value will be for the final
                            output if --annotation_folder is not provided.
                            Otherwise, --length would be the minimum length of the
                            transcript for the final output. The default is 20.
      --tolerance TOLERANCE, -t TOLERANCE
                            This value defines the number of nucleotides that
                            coverages drop below --height can be ignore in one
                            transcript. The default is 5.
      --tolerance_coverage TOLERANCE_COVERAGE, -tc TOLERANCE_COVERAGE
                            If the coverage is lower than tolerance_coverage, even
                            the length is within --tolerance, the algorithm will
                            still devide the current transcript to two parts. The
                            default is 0.
      -rt REPLICATES_TEX, --replicates_tex REPLICATES_TEX
                            This value (for TEX+/- libraries) is the minimal
                            number of replicates that a transcript has to be
                            detected. The format is
                            $NUMBERofCONDITION_$NUMBERofREPLICATED. If different
                            --replicate_tex values need to be assigned to
                            different conditions, please use comma to separate
                            them. For example, 1_2,2_2,3_3. It means that
                            --replicate_tex is 2 in number 1 and number 2
                            conditions. In number 3 condition, --replcate_tex is
                            3. For assigning the same --replicate_tex to all
                            conditions, just use like all_1 (--replicate_tex is 1
                            in all conditions). Default is all_1.
      --replicates_frag REPLICATES_FRAG, -rf REPLICATES_FRAG
                            The meaning and input type is the same to
                            --replicates_tex. This value is for fragmented
                            libraries.
      --tex_notex TEX_NOTEX, -te TEX_NOTEX
                            If the libraries of TEX+/- need to be provided, please
                            assign this value as well.This value is that a
                            transcript should be detected in both (TEX+ and TEX-)
                            or can be detected in only one library (TEX+ or TEX-).
                            Please assign 1 or 2. Default is 1.
      --compare_TSS COMPARE_TSS, -ct COMPARE_TSS
                            If the path of TSS folder is assigned here, this
                            function will compare transcripts with TSSs to detect
                            the overlap.
      --compare_genome_annotation COMPARE_GENOME_ANNOTATION, -cg COMPARE_GENOME_ANNOTATION
                            If the paht of annotation folder is assigned here,
                            this function will compare transcripts with genome
                            annotation to detect the parent transcripts of genome
                            annotation.
      --compare_feature_genome COMPARE_FEATURE_GENOME, -cf COMPARE_FEATURE_GENOME
                            If --compare_genome_annotation is provided, please
                            assign the feature which you want to compare. Default
                            is gene,CDS. If multiple features need to be assigned,
                            just insert comma between each feature, such as
                            gene,CDS.
      --TSS_fuzzy TSS_FUZZY, -fu TSS_FUZZY
                            If --compare_TSS is assigned. please type the fuzzy
                            for comparing TSS with transcript assembly here.
                            Default is 5.
      --Tex_treated_libs TEX_TREATED_LIBS, -tl TEX_TREATED_LIBS
                            If the TEX+/- libraries can be provided, please assign
                            the name of TEX+/- library. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --fragmented_libs FRAGMENTED_LIBS, -fl FRAGMENTED_LIBS
                            If the fragmented libraries can be provided, please
                            assign the name of fragmented library. The format is: 
                            wig_file_name:fragmented(frag):condition_id(integer):r
                            eplicate_id(alphabet):strand(+ or -). If multiple wig
                            files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:frag:1:a:+,wig2:frag:1:a:-.
      --table_best, -tb     The output table only includes the information of the
                            highest expressed library. Default is False.
      --terminator_folder TERMINATOR_FOLDER, -tr TERMINATOR_FOLDER
                            If the folder of terminator gff file is assigned here,
                            this function will compare transcripts with
                            terminators to detect the parent transcript of
                            terminator. Default is None.
      --fuzzy_term FUZZY_TERM, -fz FUZZY_TERM
                            If --terminator_folder is assigned, please assign the
                            fuzzy here. Default is 30.
      --max_length_distribution MAX_LENGTH_DISTRIBUTION, -mb MAX_LENGTH_DISTRIBUTION
                            For generating the figure of distribution of
                            transcript length, please assign the maximum length
                            that you want to include. Default is 2000.

- **Output files**

Output files are stored in ``$ANNOgesic/output/transcriptome_assembly``.

The generated output folders are as following:

**tables:** Table of transcript with more details. The meaning of the columns in the table is following:

	**strain:** Strain name.

	**Name:** Name of this transcript in the gff file.

	**start:** Starting point of this transcript.

	**end:** End point of this transcript.

	**strand:** Strand of this transcript.

	**detect_lib_type:** This transcript can be detected in fragmented or TEX+/- libraries.

	**associated_gene:** Which genes are associated with this transcript.

	**associated_tss:** Which TSSs are located on this transcript.

	**associated_term:** Which terminators are associated with this transcript.

	**coverage_details:** Stores the average coverage information of all libraries about this transcript.

**statistics:** Stores statistic files.

	**Comparing transcript with other features:** ``stat_compare_transcript_genome_$STRAINNAME.csv`` is 
	for comparing transcript with genome annotation, ``stat_compare_transcript_TSS_$STRAINNAME.csv`` is for comparing 
	transcript with TSS, and ``stat_compare_transcript_terminator_$STRAINNAME.csv`` is for comparing
        transcript with terminator.

	**Figure of the distribution of transcript length:** ``$STRAINNAME_length_all.png`` is for analyzing of all transcript length. 
	``$STRAINNAME_length_less_$LENGTH.png`` is for the analyzing of the assigned length.

**gffs:** Stores gff files of transcript. Some useful information can be found in the tags of the attributes within the transcript gff file.
Based on this information, we can know the details of the specific transcript. The tags are as following:

	**compare_$FEATURE:** State of overlap between transcripts and features (--compare_feature_genome).
	(If --compare_genome_annotation is assigned.) The value may be "cover", "right_shift", "left_shift", "within" or "no_related".

	**associated_tss:** Shows which TSSs are located on which transcripts. 
	(If --compare_TSS is assigned.)

	**associated_term:** Shows which terminators are located on which transcripts.
	(If --terminator_folder is assigned.)

	**associated_$FEATURE:** Shows that the feature (--compare_feature_genome) are located on which transcripts.
	(If --compare_genome_annotation is assigned.) 

	**detect_lib:** This transcript is detected by tex-treated libraries or fragmented libraries.

	**best_avg_coverage:** The average coverage of the highest expressed library within this transcript.

Once the comparison between transcript and genome annotation files is done, a tag will also be generated in the genome annotation gff file.

	**Parent:** Shows the parent transcript of the genome annotation.

As soon as the comparison between transcript and TSS files is done, a tag will also be generated in the TSS gff file.

	**Parent:** Shows the parent transcript of the TSS.

terminator
-----------

``terminator`` will predict the rho-independent terminators. ``ANNOgesic`` combines the results of 
two methods in order to get more reliable candidates. The first method is using `TranstermHP <http://transterm.cbcb.umd.edu/>`_.
The other one detects the specific secondary structure between converging pairs  
of transcripts and CDSs. ``ANNOgesic`` can check the coverages in order to generate the terminators 
which have coverage significant decrease.

- **Required tools**

`TranstermHP <http://transterm.cbcb.umd.edu/>`_

**RNAfold** of `ViennaRNA <http://www.tbi.univie.ac.at/RNA/>`_.

- **Required files**

**Gff files of the genome annotation**

**Fasta files of the genome sequence**

**Wiggle files of TEX +/- treated libraries or fragmented libraries**

**Gff files of the transcript**

- **Arguments**

::

     usage: annogesic terminator [-h] [--TransTermHP_path TRANSTERMHP_PATH]
                                 [--expterm_path EXPTERM_PATH]
                                 [--RNAfold_path RNAFOLD_PATH]
                                 [--fasta_folder FASTA_FOLDER]
                                 [--annotation_folder ANNOTATION_FOLDER]
                                 [--transcript_folder TRANSCRIPT_FOLDER]
                                 [--sRNA SRNA] [--statistics]
                                 [--tex_wig_folder TEX_WIG_FOLDER]
                                 [--frag_wig_folder FRAG_WIG_FOLDER]
                                 [--decrease DECREASE]
                                 [--fuzzy_detect_coverage FUZZY_DETECT_COVERAGE]
                                 [--fuzzy_within_transcript FUZZY_WITHIN_TRANSCRIPT]
                                 [--fuzzy_downstream_transcript FUZZY_DOWNSTREAM_TRANSCRIPT]
                                 [--fuzzy_within_gene FUZZY_WITHIN_GENE]
                                 [--fuzzy_downstream_gene FUZZY_DOWNSTREAM_GENE]
                                 [--highest_coverage HIGHEST_COVERAGE]
                                 [-tl TEX_NOTEX_LIBS] [-fl FRAG_LIBS]
                                 [-te TEX_NOTEX] [-rt REPLICATES_TEX]
                                 [-rf REPLICATES_FRAG] [-tb] [-ml MIN_LOOP_LENGTH]
                                 [-Ml MAX_LOOP_LENGTH] [-ms MIN_STEM_LENGTH]
                                 [-Ms MAX_STEM_LENGTH] [-mr MISS_RATE]
                                 [-mu MIN_U_TAIL_LENGTH] [-ru RANGE_U_TAIL] [-kp]
                                 [project_path]
     
     positional arguments:
       project_path          Path of the project folder. If none is given, the
                             current directory is used.
     
     optional arguments:
       -h, --help            show this help message and exit
       --TransTermHP_path TRANSTERMHP_PATH
                             Please assign the path of "transterm" in TransTermHP.
       --expterm_path EXPTERM_PATH
                             Please assign the path of expterm.dat for TransTermHP.
                             Default is /usr/local/bin/expterm.dat
       --RNAfold_path RNAFOLD_PATH
                             If you want to assign the path of "RNAfold" of Vienna
                             package, please assign here.
       --fasta_folder FASTA_FOLDER, -f FASTA_FOLDER
                             Path of the genome fasta folder.
       --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                             Path of the genome annotation gff folder.
       --transcript_folder TRANSCRIPT_FOLDER, -a TRANSCRIPT_FOLDER
                             Path of the transcript assembly gff folder.
       --sRNA SRNA, -sr SRNA
                             If you want to include sRNA information to detect
                             terminator, please assign the folder of sRNA gff
                             files.
       --statistics, -s      Doing statistics for terminator. The name of
                             statistics file is - stat_terminator_$STRAIN_NAME.csv.
                             Default is False.
       --tex_wig_folder TEX_WIG_FOLDER, -tw TEX_WIG_FOLDER
                             If TEX+/- libraries can be provided, please assign
                             TEX+/- wig folder.
       --frag_wig_folder FRAG_WIG_FOLDER, -fw FRAG_WIG_FOLDER
                             If fragmented libraries can be provided, please assign
                             fragmented wig folder.
       --decrease DECREASE, -d DECREASE
                             This value is maximum ratio -- (lowest coverage /
                             highest coverage) within (or nearby) the terminator.
                             If the ratio is smaller than --decrease, the candidate
                             will be considered as the terminator which has
                             coverage dramatic decreasing. Default is 0.5.
       --fuzzy_detect_coverage FUZZY_DETECT_COVERAGE, -fc FUZZY_DETECT_COVERAGE
                             This value is the extended region (nucleotides) of the
                             terminators for detecting coverage significant
                             decreasing. Ex: the location of terminator is 300-400,
                             and --fuzzy_detect_coverage is 30. If the coverage
                             decrease is detected within 270-430, this candidate
                             will be still considered as the terminator which have
                             coverage dramatic decrease. Default is 30.
       --fuzzy_within_transcript FUZZY_WITHIN_TRANSCRIPT, -fut FUZZY_WITHIN_TRANSCRIPT
                             If the candidates are within transcript and the
                             distance (nucleotides) between the end of
                             gene/transcript and terminator is within this value,
                             the candidate will be consider as a terminator.
                             Otherwise, it will be removed. Default is 30.
       --fuzzy_downstream_transcript FUZZY_DOWNSTREAM_TRANSCRIPT, -fdt FUZZY_DOWNSTREAM_TRANSCRIPT
                             The meaning is similar to --fuzzy_within_transcript.
                             This value is for the candidates which are downstream
                             of transcript. Default is 30.
       --fuzzy_within_gene FUZZY_WITHIN_GENE, -fuc FUZZY_WITHIN_GENE
                             The meaning is similar to --fuzzy_within_transcript.
                             This value is for gene in stead of transcript. Default
                             is 10.
       --fuzzy_downstream_gene FUZZY_DOWNSTREAM_GENE, -fdg FUZZY_DOWNSTREAM_GENE
                             The meaning is similar to
                             --fuzzy_downstream_transcript. This value is for gene
                             in stead of transcript. Default is 310.
       --highest_coverage HIGHEST_COVERAGE, -hc HIGHEST_COVERAGE
                             The highest coverage of terminator must be higher than
                             this value. The low expressed terminator will not be
                             included in "best" results, but still in
                             "all_candidates". Default is 10.
       -tl TEX_NOTEX_LIBS, --tex_notex_libs TEX_NOTEX_LIBS
                             If the libraries of TEX+/- can be provided, please
                             assign the name of TEX+/- library. The format is:
                             wig_file_name:TEX+/-(tex or notex):condition_id(intege
                             r):replicate_id(alphabet):strand(+ or -). If multiple
                             wig files need to be assigned, please use comma to
                             separate the wig files. For example,
                             wig1:tex:1:a:+,wig2:tex:1:a:-.
       -fl FRAG_LIBS, --frag_libs FRAG_LIBS
                             If the fragmented libraries can be provided, please
                             assign the name of fragmented library. The format is: 
                             wig_file_name:fragmented(frag):condition_id(integer):r
                             eplicate_id(alphabet):strand(+ or -). If multiple wig
                             files need to be assigned, please use comma to
                             separate the wig files. For example,
                             wig1:frag:1:a:+,wig2:frag:1:a:-.
       -te TEX_NOTEX, --tex_notex TEX_NOTEX
                             If the libraries of TEX+/- can be provided, please
                             assign this value as well. This value is that the
                             terminator should be detected in both (TEX+ and TEX-)
                             or can be detected in only one library (TEX+ or TEX-).
                             Please assign 1 or 2. Default is 1.
       -rt REPLICATES_TEX, --replicates_tex REPLICATES_TEX
                             This value (for TEX+/- libraries) is the minimal
                             number of replicates that a terminator has to be
                             detected. The format is
                             $NUMBERofCONDITION_$NUMBERofREPLICATED. If different
                             --replicate_tex values need to be assigned to
                             different conditions, please use comma to separate
                             them. For example, 1_2,2_2,3_3. It means that
                             --replicate_tex is 2 in number 1 and number 2
                             conditions. In number 3 condition, --replcate_tex is
                             3. For assigning the same --replicate_tex to all
                             conditions, just use like all_1 (--replicate_tex is 1
                             in all conditions). Default is all_1.
       -rf REPLICATES_FRAG, --replicates_frag REPLICATES_FRAG
                             The meaning and input type is the same as
                             --replicates_tex. This value is for fragmented
                             libraries.
       -tb, --table_best     Output table only contains the information of the
                             library which has most significant coverage decrease.
                             Default is False.
       -ml MIN_LOOP_LENGTH, --min_loop_length MIN_LOOP_LENGTH
                             The minimum loop length of terminator. Default is 3
                             nts.
       -Ml MAX_LOOP_LENGTH, --max_loop_length MAX_LOOP_LENGTH
                             The maximum loop length of terminator. Default is 10
                             nts.
       -ms MIN_STEM_LENGTH, --min_stem_length MIN_STEM_LENGTH
                             The minimum stem length of terminator. Default is 4
                             nts.
       -Ms MAX_STEM_LENGTH, --max_stem_length MAX_STEM_LENGTH
                             The maximum stem length of terminator. Default is 20
                             nts.
       -mr MISS_RATE, --miss_rate MISS_RATE
                             The percentage of nucleotides which can be no pair in
                             the stem. Default is 0.25.
       -mu MIN_U_TAIL_LENGTH, --min_U_tail_length MIN_U_TAIL_LENGTH
                             The minimum U-tail length of terminator. Default is 3
                             nts.
       -ru RANGE_U_TAIL, --range_U_tail RANGE_U_TAIL
                             The range (nucleotides) for detection of U-tail. For
                             example, if --range_U_tail is 6 and
                             --min_U_tail_length is 3, and there are 3 Us within 6
                             nts, This candidate will be assigned as the terminator
                             which has poly U-tail. Default is 6.
       -kp, --keep_multi_term
                             Sometimes, one gene is associated with more terminator
                             candidates. In default, it will only keep the high
                             confident one. This function can keep all terminators
                             which associated with the same gene. Default is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/terminator``. 

The output folders are as following:

**statistics:** Stores statistic files.

	**Terminator detection method with corresponding amounts:** Filename is ``stat_$STRAINNAME.csv``.

	**Comparing terminator with transcript:** Based on different types of terminators, 
	the files are ``stat_compare_terminator_transcript_$STRAINNAME_all_candidates.csv``, 
	``stat_comparison_terminator_transcript_$STRAINNAME_best.csv`` and ``stat_comparison_terminator_transcript_$STRAINNAME_express.csv``

**transtermhp:** Store any output of `TranstermHP <http://transterm.cbcb.umd.edu/>`_.

**gffs:** Store gff files of the terminator.

There are four different sub-folders to store terminators.

	**all_candidate:** Stores all terminators which ``ANNOgesic`` can detect.

	**express:** Stores the terminators revealing gene expression.

	**best:** Stores the terminators which reveal gene expression and show dramatic decrease of its coverage.

	**non_express:** Stores the terminators which has no gene expression.

Some useful information can be found in the tags of the attributes within the terminator gff file.
Based on this information, we can know the details of the specific terminator. The tags are as following:

	**method:** By which method the terminator is detected.

	**coverage_decrease:** The terminators coverage reveals dramatic decrease or not.

	**express:** The terminator reveals gene expression or not.

	**diff_coverage:** This value shows the library which reveals strongest coverage decreasing.

	**associated_gene:** Which genes are associated with this terminator.

	**Parent:** This tag presents the parent transcript of the terminator.

**tables:** Stores tables of terminators with more details.

There are four different sub-folders to store terminators.

	**all_candidate:** Stores all terminators which ``ANNOgesic`` can detect.

        **express:** Stores the terminators revealing gene expression.

        **best:** Stores the terminators which reveal gene expression and show dramatic decrease of its coverage.

        **non_express:** Stores the terminators which has no gene expression.

The meanings of the columns are as following:

	strain  name    start   end     strand  detect  associated_gene associated_transcript   coverage_decrease       coverage_detail

	**strain:** Strain name.

	**name:** Name of this terminator in the gff file.

	**start:** Staring point of this terminator.

	**end:** End point of this terminator.

	**strand:** Strand of this terminator.

	**detect:** This terminator is detected by which method.

	**associated_gene:** Which genes are associated with this terminator.

	**associated_transcript:** The parent transcript of this terinator.

	**coverage_decrease:** This terminator shows dramatic decrease of its coverage or not.

	**coverage_detail:** Shows the coverage information of the libraries about this terminator. "high" means the highest cooverage of the library, 
	"low" means the lowest coverage of the library, and "diff" represents the difference between "high" and "low". If "No_coverage_decreasing" is showed, 
	it means this terminator reveal gene expression but no coverage decrease. If "NA" is showed, it means that this terminator has no gene expression.

utr
-----

``utr`` can compare TSSs, CDSs/tRNAs/sRNAs, transcripts and terminators
to generate proper UTRs. 5'UTRs are based on detecting the regions between TSSs and CDSs/tRNAs/sRNAs. 
3'UTRs are based on detecting the 
regions between the end of the transcripts and CDSs/tRNAs/sRNAs. If the gff files of TSSs are not computed by 
ANNOgesic, please use --TSS_source. ``utr`` will classify TSSs for the analysis.

- **Required files**

**Gff file of the genome annotation**

**Gff file of the TSS**

**Gff file of the transcript**

- **Optional input files**

**Gff file of the terminator:** If the information of terminators is needed, the gff files of terminators are required.

- **Arguments**

::

    usage: annogesic utr [-h] [--annotation_folder ANNOTATION_FOLDER]
                         [--TSS_folder TSS_FOLDER]
                         [--transcript_assembly_folder TRANSCRIPT_ASSEMBLY_FOLDER]
                         [--terminator_folder TERMINATOR_FOLDER] [--TSS_source]
                         [--base_5UTR BASE_5UTR] [--UTR_length UTR_LENGTH]
                         [--base_3UTR BASE_3UTR]
                         [--terminator_fuzzy TERMINATOR_FUZZY]
                         [--fuzzy_3utr FUZZY_3UTR] [--fuzzy_5utr FUZZY_5UTR]
                         [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            Path of the genome annotation gff folder.
      --TSS_folder TSS_FOLDER, -t TSS_FOLDER
                            Path of the TSS folder.
      --transcript_assembly_folder TRANSCRIPT_ASSEMBLY_FOLDER, -a TRANSCRIPT_ASSEMBLY_FOLDER
                            Path of the transcriptome assembly folder.
      --terminator_folder TERMINATOR_FOLDER, -e TERMINATOR_FOLDER
                            If the path of terminator folder is assigned here,
                            this function will also apply terminator to detect
                            3'UTR.
      --TSS_source, -s      The TSS gff file is generated by ANNOgesic or not. If
                            the TSS file is not generated by ANNOgesic, this
                            function will classify the TSSs for detecting UTRs.
                            Default is True (from ANNOgesic).
      --base_5UTR BASE_5UTR, -b5 BASE_5UTR
                            Please assign the information for detection of 5'UTR.
                            It can be "TSS" or "transcript" or "both". Default is
                            both.
      --UTR_length UTR_LENGTH, -l UTR_LENGTH
                            The maximum UTR length. Default is 300.
      --base_3UTR BASE_3UTR, -b3 BASE_3UTR
                            please assign the information for detection of 3'UTR.
                            It can be "transcript" or "terminator" or "both".
                            Default is transcript.
      --terminator_fuzzy TERMINATOR_FUZZY, -f TERMINATOR_FUZZY
                            This is only for --base_3UTR which is assigned by
                            "transcript" or "both", and terminator file are
                            provided. If the distance (nucleotides) between
                            terminator and the end of transcript is lower than
                            this value, the terminator is consider to be
                            associated with the 3'UTR. Default is 30.
      --fuzzy_3utr FUZZY_3UTR, -f3 FUZZY_3UTR
                            If --base_3UTR includes transcript, please assign the
                            fuzzy of 3'UTR. Default is 10 nucleotides.
      --fuzzy_5utr FUZZY_5UTR, -f5 FUZZY_5UTR
                            If --base_5UTR includes transcript, please assign the
                            fuzzy of 5'UTR. Default is 5 nucleotides.

- **Output files**

Output of 5'UTRs are stored in ``$ANNOgesic/output/UTR/5UTR``.

Output of 3'UTRs are stored in ``$ANNOgesic/output/UTR/3UTR``.

The output folders are as following:

**gffs:** Stores gff files of the 5'UTR/3'UTR. 
Some useful information can be found in the tags of the attributes within the UTR gff file. 
Based on this information, we can know the details of the specific UTR. The tags are as following:

	**length:** UTR length.
	
	**associated_cds:** Which CDSs/rRNAs/tRNAs are associated with this UTR.
	
	**associated_gene:** Which genes are associated with this UTR.
	
	**Parent:** Shows the parent transcript of this UTR.
	
	**associated_tss:** Which TSSs are associated with this 5'UTR.
	
	**tss_type:** What types of TSSs are associated with this 5'UTR.
	
	**associated_term:** Which terminators are associated with this 3'UTR.

**statiatics:** ``$GFFNAME_$STRAINNAME_$UTRTYPE_length.png`` is the distribution of the UTR length.

srna
-----
``srna`` can predict different types of sRNAs. For intergenic and antisense sRNA, it 
is detected via comparison of the transcripts and annotation profile. 
For UTR-derived sRNA, the detection is based on the TSSs and processing sites, 
transcript and genome annotation.

- **Required files**

**Gff files of the genome annotation**

**Gff files of the transcript**

**Wiggle files of the fragmented or TEX+/- libraries:** Please check the section ``The format of libraries for import to ANNOgesic``.

- **Optional input files**

**Gff files of the TSS:** If you want to detect the UTR-derived sRNAs, it is necessary to input
TSS information. It is for the detection of 5'UTR-derived sRNA and interCDS-derived sRNA. 
If you don't want to detect UTR-derived sRNAs,
TSS information still can be provided as a filter.

**Gff files of processing site:** For checking the sRNAs which reveal ends with processing sites. Moreover,
Some 3'UTR-derived and interCDS-derived sRNA candidates start
from processing sites not TSSs. If you don't want to detect UTR-derived sRNAs,
This information still can be provided to increase the accuracy, especially for some
long non-coding regions.

**Promoter table:** Information of the promoter motifs can be used for prioritizing sRNA candidates via comparing promoters with
sRNA and sRNA coverage. The format should be as following:

===========  ============  ==========  =======
strain       TSS_position  TSS_strand  Motif
-----------  ------------  ----------  -------
NC_000915.1  237118        \-          MOTIF_1
NC_000915.1  729009        \-          MOTIF_1
===========  ============  ==========  =======

First row is header of the table, the last column is the name of motif/promoter.
If subcommand ``promoter`` was used for detecting promoter, the table will be generated automatically.
Please refer to the section ``promoter``.

- **Filers with the corresponding input files and tools**

There are some filters which can improve the prediction. Following is the filter name with the required files and tools.

**Secondary structure:** Remove the false positives by checking the folding energy change of secondary structure.

	**Required tools:**

		`ViennaRNA <http://www.tbi.univie.ac.at/RNA/>`_

		`Ps2pdf14 <http://pages.cs.wisc.edu/~ghost/doc/AFPL/6.50/Ps2pdf.htm>`_

	**Required files:**

		**Fasta files of genome sequence**

**TSS:** Remove the candidates which are not associated with TSSs.

	**Required files:**

		**Gff file of TSS**

**Searching sRNA candidate in sRNA database:** If homologs of this sRNA candidates can be found in sRNA database, 
this candidates will be included to the result without considering other filters.

	**Required tools:**

		`Blast+ <ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/>`_

	**Required files:**

		**sRNA database:** Such as `BSRD <http://www.bac-srna.org/BSRD/index.jsp>`_. 
		The format of header should be ``$ID|$STRAIN|$SRNANAME``. The ID is saci403.1; 
		the strain of this sRNA is Acinetobacter sp. ADP1 and the name of sRNA is Aar.
		If the format of the header is not correct, an error will occur when the user runs this subcommand with 
		``--sRNA_blast_stat, -sb``.

**Searching sRNA candidate in nr database:** If homologs of this sRNA candidates can be found in nr database and the hits numbers are more than ``--cutoff_nr_hit``,
this candidates will be removed.

	**Required tools:**

		`Blast+ <ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/>`_

	**Required files:**

		**nr database:** The file can be download from `nr database <ftp://ftp.ncbi.nih.gov/blast/db/FASTA/>`_.
	
**Terminator:** Remove the candidates which are not associated with terminators.

	**Required files:**

		**Gff file of the terminators**

**sORF:** Remove the candidates which overlap sORF.

	**Required files:**

		**Gff file of the sORF**


**Promoter:** Remove the candidates which are not associated with promoter motif.

	**Required files:**

		**Table of the promoter:** Please check the "Optional input files" of this section.

- **Arguments**

::

    usage: annogesic srna [-h] [--Vienna_folder VIENNA_FOLDER]
                          [--Vienna_utils VIENNA_UTILS]
                          [--blast_plus_folder BLAST_PLUS_FOLDER]
                          [--ps2pdf14_path PS2PDF14_PATH] [--UTR_derived_sRNA]
                          [--filter_info FILTER_INFO]
                          [--transcript_assembly_folder TRANSCRIPT_ASSEMBLY_FOLDER]
                          [--annotation_folder ANNOTATION_FOLDER]
                          [--TSS_folder TSS_FOLDER]
                          [--processing_site_folder PROCESSING_SITE_FOLDER]
                          [--promoter_table PROMOTER_TABLE]
                          [--promoter_name PROMOTER_NAME] [--TSS_source]
                          [--TSS_intergenic_fuzzy TSS_INTERGENIC_FUZZY]
                          [--TSS_5UTR_fuzzy TSS_5UTR_FUZZY]
                          [--TSS_3UTR_fuzzy TSS_3UTR_FUZZY]
                          [--TSS_interCDS_fuzzy TSS_INTERCDS_FUZZY]
                          [--terminator_folder TERMINATOR_FOLDER]
                          [--terminator_fuzzy_in_sRNA TERMINATOR_FUZZY_IN_SRNA]
                          [--terminator_fuzzy_out_sRNA TERMINATOR_FUZZY_OUT_SRNA]
                          [--min_length MIN_LENGTH] [--max_length MAX_LENGTH]
                          [--tex_wig_folder TEX_WIG_FOLDER]
                          [--frag_wig_folder FRAG_WIG_FOLDER]
                          [--run_intergenic_TEX_coverage RUN_INTERGENIC_TEX_COVERAGE]
                          [--run_intergenic_noTEX_coverage RUN_INTERGENIC_NOTEX_COVERAGE]
                          [--run_intergenic_fragmented_coverage RUN_INTERGENIC_FRAGMENTED_COVERAGE]
                          [--run_break_transcript RUN_BREAK_TRANSCRIPT]
                          [--run_antisense_TEX_coverage RUN_ANTISENSE_TEX_COVERAGE]
                          [--run_antisense_noTEX_coverage RUN_ANTISENSE_NOTEX_COVERAGE]
                          [--run_antisense_fragmented_coverage RUN_ANTISENSE_FRAGMENTED_COVERAGE]
                          [--run_utr_TEX_coverage RUN_UTR_TEX_COVERAGE]
                          [--run_utr_noTEX_coverage RUN_UTR_NOTEX_COVERAGE]
                          [--run_utr_fragmented_coverage RUN_UTR_FRAGMENTED_COVERAGE]
                          [--min_utr_coverage MIN_UTR_COVERAGE]
                          [--fasta_folder FASTA_FOLDER]
                          [--cutoff_energy CUTOFF_ENERGY] [--mountain_plot]
                          [--nr_format] [--srna_format]
                          [--sRNA_database_path SRNA_DATABASE_PATH]
                          [--nr_database_path NR_DATABASE_PATH]
                          [--tex_notex_libs TEX_NOTEX_LIBS]
                          [--frag_libs FRAG_LIBS] [--tex_notex TEX_NOTEX]
                          [-rt REPLICATES_TEX] [--replicates_frag REPLICATES_FRAG]
                          [--table_best]
                          [--decrease_intergenic_antisense DECREASE_INTERGENIC_ANTISENSE]
                          [--decrease_utr DECREASE_UTR]
                          [--fuzzy_intergenic_antisense FUZZY_INTERGENIC_ANTISENSE]
                          [--fuzzy_utr FUZZY_UTR] [--cutoff_nr_hit CUTOFF_NR_HIT]
                          [--blast_e_nr BLAST_E_NR] [--blast_e_srna BLAST_E_SRNA]
                          [--sORF SORF] [--detect_sRNA_in_CDS]
                          [--overlap_percent_CDS OVERLAP_PERCENT_CDS]
                          [--ignore_hypothetical_protein]
                          [--ranking_time_promoter RANKING_TIME_PROMOTER]
                          [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --Vienna_folder VIENNA_FOLDER
                            Please assign the folder of Vienna package. It should
                            include RNAfold.
      --Vienna_utils VIENNA_UTILS
                            Please assign the folder of Utils of Vienna package.
                            It should include relplot.pl and mountain.pl.
      --blast_plus_folder BLAST_PLUS_FOLDER
                            Please assign the folder of blast+ which include
                            blastn, blastx, makeblastdb.
      --ps2pdf14_path PS2PDF14_PATH
                            Please assign the path of ps2pdf14.
      --UTR_derived_sRNA, -u
                            The function is for detecting UTR-derived sRNA.
                            Default is False.
      --filter_info FILTER_INFO, -d FILTER_INFO
                            There are several filters that you can use to imporve
                            sRNA detection: 1. tss (sRNA has to start with TSS),
                            2. sec_str (free energy change of secondary structure
                            (normalized by length) has to be smaller than
                            --cutoff_energy), 3. blast_nr (the number of the
                            homologs can not be found more than --cutoff_nr_hit in
                            the non-redundant database), 4. blast_srna (as long as
                            the homologs can be found in sRNA database, the
                            candidates will be included to best result without
                            considering other filters), 5. sorf (sRNA can not
                            overlap sORF), 6. term (sRNA has to be associated with
                            a terminator), 7. promoter (sRNA has to be associated
                            with a promoter motif). ATTENTION: without importing
                            any information, the results may include many false
                            positives. If multiple filters needs to be assigned,
                            please use comma to separated them. ex:
                            tss,sec_str,blast_nr - means it used 1. TSS, 2. free
                            energy change of secondary structure and 3. blast to
                            nr database to detect sRNA. If you want to use
                            blast_srna as a filter, please follow the format:
                            $ID|$STRAIN|$SRNANAME.
                            "tss,sec_str,blast_nr,blast_srna" is recommanded to be
                            assigned. Default is None.
      --transcript_assembly_folder TRANSCRIPT_ASSEMBLY_FOLDER, -a TRANSCRIPT_ASSEMBLY_FOLDER
                            Path of the transcriptome assembly folder.
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            Path of the genome annotation gff folder.
      --TSS_folder TSS_FOLDER, -t TSS_FOLDER
                            If the path of TSS gff folder is assigned here, TSS
                            information will be used for detecting sRNA. For
                            detection of UTR-derived sRNA, TSS information MUST be
                            provided.
      --processing_site_folder PROCESSING_SITE_FOLDER, -p PROCESSING_SITE_FOLDER
                            If the path of processing site gff folder is assigned
                            here, processing site information will be used for
                            detecting sRNA. For detection of UTR-derived sRNA,
                            processing site information can improve the results.
      --promoter_table PROMOTER_TABLE, -pt PROMOTER_TABLE
                            If the path of promoter table is assigned here, the
                            promoter information will be used for detecting of
                            sRNA. The format of table is $STRAIN $TSS_POSITION
                            $TSS_STRAND $PROMOTER_NAME. TSS information is also
                            required.
      --promoter_name PROMOTER_NAME, -pn PROMOTER_NAME
                            If --promoter_table is provided, please assign the
                            promoter name (the last column of promoter table)
                            which you want to compare. If multiple promoters need
                            to be assigned, please put comma between the
                            promoters. Default is None.
      --TSS_source, -ts     If the TSS gff file is not generated by ANNOgesic,
                            please use this function to classify TSSs and generate
                            the proper format for sRNA prediction. Default is True
                            (from ANNOgesic).
      --TSS_intergenic_fuzzy TSS_INTERGENIC_FUZZY, -ft TSS_INTERGENIC_FUZZY
                            If --TSS_folder is provided, please assign the fuzzy
                            for comparing TSS with transcript. It is for
                            intergenic sRNA. Default is 3.
      --TSS_5UTR_fuzzy TSS_5UTR_FUZZY, -f5 TSS_5UTR_FUZZY
                            If --TSS_folder is provided, please assign the fuzzy
                            for comparing TSS with transcript. It is for 5'UTR-
                            derived sRNA.The input type can be percentage ("p") or
                            the real amount of reads ("n"). Ex: p_0.05 means the
                            fuzzy is 5 percent of the length of 5'UTR. n_10 means
                            the fuzzy is 10 base pair. Default is n_3.
      --TSS_3UTR_fuzzy TSS_3UTR_FUZZY, -f3 TSS_3UTR_FUZZY
                            The meaning is similar to --TSS_5UTR_fuzzy. This value
                            is for 3'UTR-derived sRNA instead of 5'UTR-derived
                            sRNA. Default is p_0.04.
      --TSS_interCDS_fuzzy TSS_INTERCDS_FUZZY, -fc TSS_INTERCDS_FUZZY
                            The meaning is similar to --TSS_5UTR_fuzzy. This value
                            is for interCDS-derived sRNA instead of 5'UTR-derived
                            sRNA. Default is p_0.04.
      --terminator_folder TERMINATOR_FOLDER, -tf TERMINATOR_FOLDER
                            If terminator information is required, please assign
                            the path of gff folder of terminator.
      --terminator_fuzzy_in_sRNA TERMINATOR_FUZZY_IN_SRNA, -tfi TERMINATOR_FUZZY_IN_SRNA
                            If --terminator_folder is provided, please assign the
                            fuzzy for comparing terminator with transcript. This
                            value is for the terminator which is within sRNA.
                            Default is 30.
      --terminator_fuzzy_out_sRNA TERMINATOR_FUZZY_OUT_SRNA, -tfo TERMINATOR_FUZZY_OUT_SRNA
                            The meaning is the same as --terminator_fuzzy_in_sRNA.
                            This value is for the terminator which is outside of
                            sRNA. Default is 30.
      --min_length MIN_LENGTH, -lm MIN_LENGTH
                            Please assign the minimum sRNA length. Default is 30.
      --max_length MAX_LENGTH, -lM MAX_LENGTH
                            Please assign the maximum sRNA length. Default is 500.
      --tex_wig_folder TEX_WIG_FOLDER, -tw TEX_WIG_FOLDER
                            If TEX+/- libraries can be provided, please assign the
                            path of TEX+/- wig folder.
      --frag_wig_folder FRAG_WIG_FOLDER, -fw FRAG_WIG_FOLDER
                            If fragmented libraries can be provided, please assign
                            the path of fragment wig folder.
      --run_intergenic_TEX_coverage RUN_INTERGENIC_TEX_COVERAGE, -it RUN_INTERGENIC_TEX_COVERAGE
                            The minimum average coverage of intergenic sRNA
                            candidates in TEX+ libraries. This value is based on
                            different types of TSSs. The order of numbers is
                            "Primary,Secondary,Internal,Antisense,Orphan". Ex: The
                            input is 0,0,0,50,10. It means that antisense TSS
                            (minimum coverage is 50) and orphan TSS (minimum
                            coverage is 10) are used for sRNA prediction. The
                            other types of TSSs will not be used for sRNA
                            detection (assign to 0). If TSS information is not
                            provided, it will choose the lowest one as a general
                            cutoff for prediction. Ex: if the input is 0,0,0,50,10
                            and --TSS_folder is not provided, 10 will be the
                            general cutoff for prediction. Default is 0,0,0,40,20.
      --run_intergenic_noTEX_coverage RUN_INTERGENIC_NOTEX_COVERAGE, -in RUN_INTERGENIC_NOTEX_COVERAGE
                            The meaning is the same as
                            --run_intergenic_TEX_coverage. This value is for TEX-
                            libraries. Default is 0,0,0,30,10.
      --run_intergenic_fragmented_coverage RUN_INTERGENIC_FRAGMENTED_COVERAGE, -if RUN_INTERGENIC_FRAGMENTED_COVERAGE
                            The meaning is the same as
                            --run_intergenic_TEX_coverage. This value is for
                            fragmented libraries. Default is 400,200,0,50,20.
      --run_break_transcript RUN_BREAK_TRANSCRIPT, -ib RUN_BREAK_TRANSCRIPT
                            Several primary/secondary TSSs are associated with
                            transcripts which has no CDSs/tRNA/rRNA inside
                            (perhaps associated with ncRNA). In order to detect
                            the sRNA candidates in these transcripts, please
                            assign the minimum average coverage of the sRNA
                            candidates. The format is $TEX,$NOTEX,$FRAG. ex:
                            200,100,100 is means that the minimum average coverage
                            is 200 for TEX+ libraries, 100 for TEX- and fragmented
                            libraries. Default is 50,40,60
      --run_antisense_TEX_coverage RUN_ANTISENSE_TEX_COVERAGE, -at RUN_ANTISENSE_TEX_COVERAGE
                            The meaning is the same as
                            --run_intergenic_TEX_coverage. This value is for
                            antisense in stead of intergenic. Default is
                            0,0,0,40,20.
      --run_antisense_noTEX_coverage RUN_ANTISENSE_NOTEX_COVERAGE, -an RUN_ANTISENSE_NOTEX_COVERAGE
                            The meaning is the same as
                            --run_intergenic_noTEX_coverage. This value is for
                            antisense in stead of intergenic. Default is
                            0,0,0,30,10.
      --run_antisense_fragmented_coverage RUN_ANTISENSE_FRAGMENTED_COVERAGE, -af RUN_ANTISENSE_FRAGMENTED_COVERAGE
                            The meaning is the same as
                            --run_intergenic_fragmented_coverage. This value is
                            for antisense in stead of intergenic. Default is
                            400,200,0,50,20.
      --run_utr_TEX_coverage RUN_UTR_TEX_COVERAGE, -ut RUN_UTR_TEX_COVERAGE
                            The minimum average coverage of UTR-derived sRNA
                            candidates in TEX+ libraries. The input can be
                            assigned by the percentile ("p") or real number of
                            coverage ("n"). The order of numbers is
                            "5'UTR,3'UTR,interCDS". Ex: if the input is
                            "p_0.7,p_0.5,p_0.5", it will use 70 percentile of
                            5'UTR coverage as minimum coverage for 5'UTR-derived
                            sRNA, median of 3'UTR and interCDS coverage as minimum
                            coverage for 3'UTR and interCDS-derived sRNA. Ex: if
                            the input is "n_30,n_10,n_20 " it will use 30 as
                            minimum coverage for 5'UTR-derived sRNA, 10 as minimum
                            coverage for 3'UTR-derived sRNA and 20 as minimum
                            coverage for interCDS-derived sRNA. Default is
                            p_0.8,p_0.6,p_0.7.
      --run_utr_noTEX_coverage RUN_UTR_NOTEX_COVERAGE, -un RUN_UTR_NOTEX_COVERAGE
                            The meaning is the same as --run_utr_TEX_coverage.
                            This value is for TEX- libraries. Default is
                            p_0.7,p_0.5,p_0.6.
      --run_utr_fragmented_coverage RUN_UTR_FRAGMENTED_COVERAGE, -uf RUN_UTR_FRAGMENTED_COVERAGE
                            The meaning is the same as --run_utr_TEX_coverage.
                            This value is for fragmented libraries. Default is
                            p_0.7,p_0.5,p_0.6.
      --min_utr_coverage MIN_UTR_COVERAGE, -mu MIN_UTR_COVERAGE
                            The minimum general coverage of UTR-derived sRNA. The
                            coverage of UTR-derived sRNA should not only fit the
                            --run_utr_TEX_coverage, --run_utr_noTEX_coverage and
                            --run_utr_fragmented_coverage, but also this value.
                            Defaul is 50.
      --fasta_folder FASTA_FOLDER, -f FASTA_FOLDER
                            If "sec_str" or "blast_nr" or "blast_srna" is assigned
                            to --import_info, please assign the path of genome
                            fasta folder here.
      --cutoff_energy CUTOFF_ENERGY, -e CUTOFF_ENERGY
                            If "sec_str" is included in --import_info, please
                            assign the maximum folding energy change (normalized
                            by length of gene). Default is -0.05.
      --mountain_plot, -m   This function will generate mountain plot of sRNA
                            candidate. Default is False.
      --nr_format, -nf      This function will format nr database. If the nr
                            database has formatted, the step can be skip. Default
                            is False.
      --srna_format, -sf    The meaning is the same as --nr_format. It is for sRNA
                            database in stead of nr database. Default is False.
      --sRNA_database_path SRNA_DATABASE_PATH, -sd SRNA_DATABASE_PATH
                            If "blast_srna" is included in --import_info, please
                            assign the path of sRNA database.
      --nr_database_path NR_DATABASE_PATH, -nd NR_DATABASE_PATH
                            If "blast_nr" is included in --import_info, please
                            assign the path of nr database.
      --tex_notex_libs TEX_NOTEX_LIBS, -tl TEX_NOTEX_LIBS
                            If TEX+/- libraries can be provided, please assign the
                            name of TEX+/- libraries here. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --frag_libs FRAG_LIBS, -fl FRAG_LIBS
                            If fragmented libraries can be provided, please assign
                            the name of fragmented libraries here. The format is: 
                            wig_file_name:fragmented(frag):condition_id(integer):r
                            eplicate_id(alphabet):strand(+ or -). If multiple wig
                            files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:frag:1:a:+,wig2:frag:1:a:-.
      --tex_notex TEX_NOTEX, -te TEX_NOTEX
                            If TEX+/- libraries is assigned, this value is that a
                            sRNA should be detected in both (TEX+ and TEX-) or can
                            be detected in only one library (TEX+ or TEX-). Please
                            assign 1 or 2. Default is 2.
      -rt REPLICATES_TEX, --replicates_tex REPLICATES_TEX
                            This value (for TEX+/- libraries) is the minimal
                            number of replicates that a sRNA has to be detected.
                            The format is $NUMBERofCONDITION_$NUMBERofREPLICATED.
                            If different --replicate_tex values need to be
                            assigned to different conditions, please use comma to
                            separate them. For example, 1_2,2_2,3_3. It means that
                            --replicate_tex is 2 in number 1 and number 2
                            conditions. In number 3 condition, --replcate_tex is
                            3. For assigning the same --replicate_tex to all
                            conditions, just use like all_1 (--replicate_tex is 1
                            in all conditions). Default is all_1.
      --replicates_frag REPLICATES_FRAG, -rf REPLICATES_FRAG
                            The meaning and input type is the same as
                            --replicates_tex. This value is for fragmented
                            libraries.
      --table_best, -tb     The output table of sRNA candidates only contains the
                            information of the highest expressed library. Default
                            is False.
      --decrease_intergenic_antisense DECREASE_INTERGENIC_ANTISENSE, -di DECREASE_INTERGENIC_ANTISENSE
                            This value is for detecting the coverage decrease in
                            intergenic/antisense transcript. If the length of
                            intergenic transcript is longer than the max_length,
                            it will check the sRNA candidates based on coverage.
                            If the ratio -- (the lowest coverage / the highest
                            coverage) of the sRNA region is smaller than this
                            value, the spot of lowest coverage will be considered
                            as the end point of the sRNA. If the new sRNA length
                            is suitable for a sRNA candidate, this candidate will
                            be included in output. Default is 0.1.
      --decrease_utr DECREASE_UTR, -du DECREASE_UTR
                            This value is for detecting the coverage decrease in
                            UTR region. The end point of UTR-derived sRNA is
                            defined by processing site or the end of transcript
                            (3'UTR-derived sRNA). If there is no processing sites
                            in 5'UTR or interCDS, the algorithm will check the
                            coverage to detect the end point of sRNA. If the ratio
                            -- (the lowest coverage / the highest coverage) of the
                            sRNA region is smaller than this value, the spot of
                            lowest coverage will be considered as the end point of
                            the sRNA. If the new sRNA length is suitable for a
                            sRNA candidate, this candidate will be included in
                            output. Default is 0.05.
      --fuzzy_intergenic_antisense FUZZY_INTERGENIC_ANTISENSE, -fi FUZZY_INTERGENIC_ANTISENSE
                            If the situation is like
                            --decrease_intergenic_antisense mentioned, This value
                            is the fuzzy nucleotides for detecting the coverage
                            decrease. Ex: the location of intergenic sRNA is
                            300-400, and --fuzzy_intergenic_antisense is 30. The
                            algorithm will search the coverage decrease within
                            270-430 Default is 10.
      --fuzzy_utr FUZZY_UTR, -fu FUZZY_UTR
                            It is simliar with --fuzzy_intergenic_antisense. This
                            is for UTR-derived sRNAs. Default is 10.
      --cutoff_nr_hit CUTOFF_NR_HIT, -cn CUTOFF_NR_HIT
                            The maximum hits number in nr database. Default is 0.
      --blast_e_nr BLAST_E_NR, -en BLAST_E_NR
                            The maximum e value for blast in nr database. Default
                            is 0.0001.
      --blast_e_srna BLAST_E_SRNA, -es BLAST_E_SRNA
                            The maximum e value for blast in sRNA database.
                            Default is 0.0001.
      --sORF SORF, -O SORF  If the path of sORF gff folder is assigned here, this
                            function will include the sORF information for
                            detecting sRNA.
      --detect_sRNA_in_CDS, -ds
                            This function will search sRNA in CDS (ex: the genome
                            annotation is not correct). More sRNA candidates which
                            overlap with CDS will be detected. Default is False.
      --overlap_percent_CDS OVERLAP_PERCENT_CDS, -oc OVERLAP_PERCENT_CDS
                            If --detect_sRNA_in_CDS is True, please assign the
                            maximum ratio of overlap between CDS and sRNA
                            candidates. Default is 0.5
      --ignore_hypothetical_protein, -ih
                            This function is for ignoring the hypothetical
                            proteins in genome annotation file. Default is False.
      --ranking_time_promoter RANKING_TIME_PROMOTER, -rp RANKING_TIME_PROMOTER
                            If --promoter_table is provided, the information of
                            promoter can be use for ranking sRNA candidates as
                            well. The ranking score is --ranking_time_promoter *
                            average coverage. For example, a sRNA candidate which
                            is associated with promoter and its average coverage
                            is 10. If --ranking_time_promoter is 2, the ranking
                            score will be 20 (2*10). For the candidate which are
                            not associated with promoter, the
                            --ranking_time_promoter will be 1. Therefore,
                            --ranking_time_promoter can not be smaller than 1.
                            Default is 2.

- **Output files**

Output files are stored in ``$ANNOgesic/output/sRNA``.Name of the output folders and files are following:

**sRNA_2d_$STRAINNAME:** The secondary structure of all sRNA candidates.

**sRNA_seq_$STRAINNAME:** The sequence of all sRNA candidates.

**blast_result_and_misc:** Stores the results of blast.

	**nr_blast_$STRAINNAME.txt:** Blast output of the nr database.

	**sRNA_blast_$STRAINNAME.txt:** Blast output of the sRNA database.

**mountain_plot:** Stores mountain plots of the sRNA candidates. Filename is as ``srna10_NC_009839.1_335339_335435_+_mountain.pdf``.
"srna10", "NC_009839.1", "335339", "335435", "+" are ID of sRNA gff file, strain name, starting point, end point and strand respecitvely.

**sec_structure:** Stores the dot plots and secondary structure plots of sRNA candidates. 
Filename of dot plot is as ``srna10_NC_009839.1_335339_335435_+_dp.pdf``, and filename of secondary structure is as ``srna10_NC_009839.1_335339_335435_+_rss.pdf``.
"srna10", "NC_009839.1", "335339", "335435", "+" are ID of sRNA gff file, strain name, starting point, end point and strand respecitvely.

**statistics:** Stores statistics files. ``stat_$STRAIN_NAME_sRNA_blast.csv`` is the analysis result of blast sRNA databases.
``stat_sRNA_class_Staphylococcus_aureus_HG003.csv`` is the classification of sRNA candidates.

**TSS_class:** If the TSSs are not computed by ANNOgesic, ``TSS_class`` will be generated for classification of TSS.
TSS gff files with TSS types will be stored here.

**tables:** Stores sRNA tables with more details. There are also some sub-folders:

	**for class:** Stores the results of different sRNA classes.

	**best:** Stores the best results of sRNAs after filtering.

	**all_candidates:** Stores all candidates without filtering.

The meanings of the columns are as following:

	**rank:** Ranking number of this sRNA. 

	**strain:** Strain name.

	**name:** sRNA Name which are used in gff file.

	**start:** Starting point of this sRNA.

	**end:** End point of this sRNA.

	**strand:** Strand of this sRNA.

	**start_with_TSS/Cleavage_site:** This sRNA starts with Which TSS or cleavage site.

	**end_with_cleavage:** If the sRNA ends with a cleavage site, the information of this cleavage site will be showed here.

	**candidates:** Position of this sRNA.

	**lib_type:** This sRNA is detected by TEX+/- or fragmented library.

	**best_avg_coverage:** Based on coverage of all libraries, The best average coverage of this sRNA will be showed here.

	**best_highest_coverage:** Based on coverage of all libraries, The highest average coverage of this sRNA will be showed here.

	**best_lowest_coverage:** Based on coverage of all libraries, The lowest average coverage of this sRNA will be showed here.

	**track/coverage:** Shows the coverage information of the libraries about this sRNA. "high" means the highest cooverage of the library,
        "low" means the lowest coverage of the library, and "avg" represents the average coverage of this sRNA.

	**normalized_secondary_energy_change(by_length):** Secondary folding change (normalized by length) of this sRNA.

	**sRNA_types:** Shows the sRNA type.

	**conflict_sORF:** If this sRNA overlaps sORF, the overlapped sORF will be showed here.

	**nr_hit_number:** The hits number of this sRNA in nr database.

	**sRNA_hit_number:** The hits number of this sRNA in sRNA database.

	**nr_hit_top3|ID|e-value:** The top 3 hits of this sRNA in nr database will be showed here. The information includes protein name, ID and e-value.

	**sRNA_hit|e-value:** If the homologs of this sRNA can be found in sRNA database, the information will be showed here.

	**overlap_CDS:** If the sRNA overlaps CDS, the information of CDS will be showed here.

	**overlap_percent:** If the sRNA overlaps CDS, the percentage of overlap between sRNA and CDS will be showed here.

	**end_with_terminator:** The terminator which is associated with this sRNA.

	**associated_promoter:** The promoter which is associated with this sRNA.

	**sRNA_length:** sRNA length.

**gffs:** Stores gff files of the sRNA. There are also some sub-folders:

	**for class:** Stores the results of different sRNA classes.

	**best:** Stores the best results of sRNAs after filtering.

	**all_candidates:** Stores all candidates without filtering.

Some useful information can be found in the tags of the attributes within the sRNA gff file.
Based on this information, we can know the details of the specific sRNA. The tags are as following:

	**sRNA_type:** This sRNA is from 5'UTR, 3'UTR, interCDS, intergenic, antisense or within CDS.

	**with_TSS:** Which TSSs are related to this sRNA.

	**sORF:** Which sORFs are overlap with this sRNA.

	**sRNA_hit:** Blast hits number of the sRNA database.

	**nr_hit:** Blast hits number of the nr database.

	**2d_energy**: Normalized (by the length of sRNA) free energy change of the sRNA secondary structure.

	**with_term:** Terminators which are associated with the sRNA candidate.

	**end_cleavage:** If this sRNA ends with a cleavage site, information of the cleavage site will be showed here.

	**overlap_cds:** This sRNA overlaps CDS or not.

	**overlap_percentage:** If this sRNA overlap CDS. The percentage of the overlap between CDS and sRNA will be showed here.

	**promoter:** Promoters which are associated with the sRNA.

sorf
----------
``sorf`` can detect sORF based on searching ribosome binding sites, start codons and stop codons within the non-annotated transcripts.
Since non-annotated region may be sRNAs or sORFs, Comparison between sORFs and sRNAs can be done by this subcommand. 
If multiple sORFs are overlapped with each other, this subcommand will merge them to be one sORF. Therefore, one region may contain more than one sORF. 
Position of the start codon which listed in output table is assigned by the first nucleotide. The position of stop codon is assigned by the last nucleotide. 
Moreover, one region may contain different frame shifts. 
Ex: (200, 202, 203) are the positions of three start codons and (241, 243) are two stop codons in 
a small transcript. Therefore, there are three possible ORFs(200-241, 203-241 and 202-243).
Please be aware this point for using the results.

- **Required files**

**Gff files of the genome annotation**
**Gff files of the transcript**

**Wiggle files of TEX+/- or fragmented libraries:** Please refer to the section ``The format of libraries for import to ANNOgesic``.

**fasta files of the genome sequence**

- **Optional input files**

**Gff files of the TSSs:** For checking the sORFs start from TSS or not. 

**Gff files of sRNAs:** For checking the overlap of sRNAs and sORFs.

- **Arguments**

::

    usage: annogesic sorf [-h] [--UTR_derived_sORF]
                          [--transcript_assembly_folder TRANSCRIPT_ASSEMBLY_FOLDER]
                          [--annotation_folder ANNOTATION_FOLDER]
                          [--TSS_folder TSS_FOLDER] [--utr_length UTR_LENGTH]
                          [--min_length MIN_LENGTH] [--max_length MAX_LENGTH]
                          [--tex_wig_folder TEX_WIG_FOLDER]
                          [--frag_wig_folder FRAG_WIG_FOLDER]
                          [--cutoff_intergenic_coverage CUTOFF_INTERGENIC_COVERAGE]
                          [--cutoff_antisense_coverage CUTOFF_ANTISENSE_COVERAGE]
                          [--cutoff_5utr_coverage CUTOFF_5UTR_COVERAGE]
                          [--cutoff_3utr_coverage CUTOFF_3UTR_COVERAGE]
                          [--cutoff_interCDS_coverage CUTOFF_INTERCDS_COVERAGE]
                          [--cutoff_background CUTOFF_BACKGROUND]
                          [--fasta_folder FASTA_FOLDER]
                          [--tex_notex_libs TEX_NOTEX_LIBS]
                          [--frag_libs FRAG_LIBS] [--tex_notex TEX_NOTEX]
                          [-rt REPLICATES_TEX] [--replicates_frag REPLICATES_FRAG]
                          [--table_best] [--sRNA_folder SRNA_FOLDER]
                          [--start_codon START_CODON] [--stop_codon STOP_CODON]
                          [--min_rbs_distance MIN_RBS_DISTANCE]
                          [--max_rbs_distance MAX_RBS_DISTANCE]
                          [--rbs_not_after_TSS] [--fuzzy_rbs FUZZY_RBS]
                          [--print_all_combination] [--best_no_sRNA]
                          [--best_no_TSS]
                          [--ignore_hypothetical_protein IGNORE_HYPOTHETICAL_PROTEIN]
                          [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --UTR_derived_sORF, -u
                            This function will detect UTR-derived sORF. Default is
                            False.
      --transcript_assembly_folder TRANSCRIPT_ASSEMBLY_FOLDER, -a TRANSCRIPT_ASSEMBLY_FOLDER
                            Path of the transcriptome assembly folder.
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            Path of the genome annotation gff folder.
      --TSS_folder TSS_FOLDER, -t TSS_FOLDER
                            If the path of TSS gff folder is assigned here, this
                            function will use the TSS information to detect sORF.
      --utr_length UTR_LENGTH, -ul UTR_LENGTH
                            If --TSS_folder is provided, please assign the utr
                            length for comparing TSS with sORF. The default number
                            is 300.
      --min_length MIN_LENGTH, -lm MIN_LENGTH
                            Please assign the minimum residue length of sORF.
                            Default is 30.
      --max_length MAX_LENGTH, -lM MAX_LENGTH
                            Please assign the maximum residue length of sORF.
                            Default is 150.
      --tex_wig_folder TEX_WIG_FOLDER, -tw TEX_WIG_FOLDER
                            If TEX+/- libraries can be provided, please assign the
                            path of TEX+/- wig folder here.
      --frag_wig_folder FRAG_WIG_FOLDER, -fw FRAG_WIG_FOLDER
                            If fragmented libraries can be provided, please assign
                            the path of fragmented wig folder here.
      --cutoff_intergenic_coverage CUTOFF_INTERGENIC_COVERAGE, -ci CUTOFF_INTERGENIC_COVERAGE
                            The minimum coverage of intergenic sORF candidates.
      --cutoff_antisense_coverage CUTOFF_ANTISENSE_COVERAGE, -ai CUTOFF_ANTISENSE_COVERAGE
                            The minimum coverage of antisense sORF candidates.
      --cutoff_5utr_coverage CUTOFF_5UTR_COVERAGE, -cu5 CUTOFF_5UTR_COVERAGE
                            The minimum coverage of 5'UTR derived sORF candidates.
                            This value can be assigned by percentage ("p") or the
                            amount of reads ("n"). Ex: p_0.05 means that the
                            coverage of sORF candidates should be higher than 5
                            percentile of all 5'UTR transcripts. n_10 means that
                            the coverage of sORF candidates should be higher than
                            10. Default is p_0.5.
      --cutoff_3utr_coverage CUTOFF_3UTR_COVERAGE, -cu3 CUTOFF_3UTR_COVERAGE
                            The meaning is the same as --cutoff_5utr_coverage.
                            This value is for 3'UTR. Default is p_0.5.
      --cutoff_interCDS_coverage CUTOFF_INTERCDS_COVERAGE, -cuf CUTOFF_INTERCDS_COVERAGE
                            The meaning is the same as --cutoff_5utr_coverage.
                            This value is for interCDS. Default is p_0.5.
      --cutoff_background CUTOFF_BACKGROUND, -cub CUTOFF_BACKGROUND
                            The general minimum coverage of all sORF candidates.
                            All candidates should fit this condition as well.
                            Default is 10.
      --fasta_folder FASTA_FOLDER, -f FASTA_FOLDER
                            The folder of genome fasta file.
      --tex_notex_libs TEX_NOTEX_LIBS, -tl TEX_NOTEX_LIBS
                            If the TEX+/- libraries can be provided, please assign
                            the name of TEX+/- library here. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --frag_libs FRAG_LIBS, -fl FRAG_LIBS
                            If the fragmented libraries can be provided, please
                            assign the name of fragmented library here. The format
                            is: wig_file_name:fragmented(frag):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:frag:1:a:+,wig2:frag:1:a:-.
      --tex_notex TEX_NOTEX, -te TEX_NOTEX
                            If the TEX+/- libraries is provided, this value is
                            that a sORF should be detected in both (TEX+ and TEX-)
                            or can be detected in only one library (TEX+ or TEX-).
                            Please assign 1 or 2. Default is 2.
      -rt REPLICATES_TEX, --replicates_tex REPLICATES_TEX
                            This value (for TEX+/- libraries) is the minimal
                            number of replicates that a sORF has to be detected.
                            The format is $NUMBERofCONDITION_$NUMBERofREPLICATED.
                            If different --replicate_tex values need to be
                            assigned to different conditions, please use comma to
                            separate them. For example, 1_2,2_2,3_3. It means that
                            --replicate_tex is 2 in number 1 and number 2
                            conditions. In number 3 condition, --replcate_tex is
                            3. For assigning the same --replicate_tex to all
                            conditions, just use like all_1 (--replicate_tex is 1
                            in all conditions). Default is all_1.
      --replicates_frag REPLICATES_FRAG, -rf REPLICATES_FRAG
                            The meaning and input type is the same as
                            --replicates_tex. This value is for fragmented
                            libraries.
      --table_best, -tb     The output table of sORF candidates only includes
                            information of the highest expressed library. Default
                            is False.
      --sRNA_folder SRNA_FOLDER, -s SRNA_FOLDER
                            If the path of sRNA gff folder is assigned here, this
                            function will compare sORF and sRNA to detect the
                            overlap.
      --start_codon START_CODON, -ac START_CODON
                            The types of start coden. If multiple types of start
                            codon need to be assigned, please use comma to
                            separate them. Default is ATG.
      --stop_codon STOP_CODON, -oc STOP_CODON
                            The types of stop coden. If multiple types of stop
                            codon need to be assigned, please use comma to
                            separate them. Default is TTA,TAG,TGA.
      --min_rbs_distance MIN_RBS_DISTANCE, -mr MIN_RBS_DISTANCE
                            The minimum distance (nucleotides) between the
                            ribosome binding site and start codon. Default is 3.
      --max_rbs_distance MAX_RBS_DISTANCE, -Mr MAX_RBS_DISTANCE
                            The maximum distance (nucleotides) between the
                            ribosome binding site and start codon. Default is 15.
      --rbs_not_after_TSS, -at
                            For generating best results, if the ribosome binding
                            site of sORF is not associted with TSS, this function
                            will include this candidate as well. Default is False.
      --fuzzy_rbs FUZZY_RBS, -zr FUZZY_RBS
                            The number of nucleotides of ribosome binding site can
                            be different with AGGAGG. Default is 2.
      --print_all_combination, -pa
                            Non-annotated transcript may has many start codons and
                            stop codons. This function can print all combinations
                            of start codons and stop codons. Default is False.
      --best_no_sRNA, -bs   For generating best results, this function can exclude
                            the sORFs which overlap with sRNA. Default is False.
      --best_no_TSS, -bt    For generating best results, this function can include
                            the sORFs which are not associated with TSS. Default
                            is False.
      --ignore_hypothetical_protein IGNORE_HYPOTHETICAL_PROTEIN, -ih IGNORE_HYPOTHETICAL_PROTEIN
                            This function is for ignoring hypothetical protein in
                            genome annotation file. Default is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/sORF``.

**statistics**: Stores statistic files.

**tables:** Stores tables of the sORFs with more details. There are also some sub-folders:

        **best:** Stores the best results of sORFs after filtering.

        **all_candidates:** Stores all candidates without filtering.

The meaning of each column is as following:

train  Name    start   end     strand  type    TSS     RBS     all_start_points        all_stop_points sRNA_confliction        frame_shift     lib_type        best_avg_coverage       best_highest_coverage   best_lowest_coverage    track_detail    seq

	**strain:** Strain name.

	**Name:** Name of this sORF which is also used in gff file.

	**start:** Starting point of this sORF.

	**end:** End point of this sORF.

	**strand:** Strand of this sORF.

	**type:** sORF type.

	**TSS:** TSSs which are associated with this sORF.

	**RBS:** Ribosome binding site of this sORF.

	**all_start_points:** Positions of all start codons which can be found in the region of this sORF.

	**all_stop_points:** Positions of all stop codons which can be found in the region of this sORF.

	**sRNA_conflict:** If this sORF overlaps sRNA, the overlapped sRNA will be showed here.

	**frame_shift:** One residue is formed by three nucleotides. Therefore, there may be three different ORFs in one region. 
	If there are sORF candidates which can be found by frame shift, the number of frame shift will be showed here. "1" means there 
	are some candidates can be found by frame shift once. "2" means there are some candidates can be found by frame shift twice. 
	The number won't larger than 2 since frame shift three times, the amino acid codon will be the same.

	**lib_type:** This sORF can be detected in TEX+/- or fragmented libraries.

	**best_avg_coverage:** Based on coverage of all libraries, The best average coverage of this sORF will be showed here.

        **best_highest_coverage:** Based on coverage of all libraries, The highest average coverage of this sORF will be showed here.

        **best_lowest_coverage:** Based on coverage of all libraries, The lowest average coverage of this sORF will be showed here.

        **track_detail:** Shows the coverage information of the libraries about this sORF. "high" means the highest cooverage of the library,
        "low" means the lowest coverage of the library, and "avg" represents the average coverage of this sORF.

	**seq:** Sequence of this sORF.

**gffs:** Stores gff files of the sORFs. There are also some sub-folders:

        **best:** Stores the best results of sORFs after filtering.

        **all_candidates:** Stores all candidates without filtering.

Some useful information can be found in the tags of the attributes within the sORF gff file.
Based on this information, we can know the details of the specific sORF. The tags are as following:

**start_TSS:** Shows this sORF starts with which TSS.

**with_TSS:** Which TSSs are associated with this sORFs.

**sORF_type:** Type of the sORF (5'UTR, 3'UTR, interCDS, intergenic, antisense or within CDS).

**sRNA:** Which sRNAs are overlap with this sORFs.

**rbs:** Ribosome binding sites of this sORFs.

**frame_shift:** The number of frame shifts in the regions.

promoter
-----------

``promoter`` can scan the upstream of TSSs to discover the promoter motifs.
We integrated `MEME <http://meme-suite.org/tools/meme>`_ to compute the promoters.
Based on the tool, visulization HTML files can be generated. If TSS gff file is not computed by 
ANNOgesic, please use ``--TSS_source``, then ``promoter`` will classify the TSSs for computing 
promoter motifs.

- **Required tools**

`MEME <http://meme-suite.org/tools/meme>`_.

`MPICH <https://http://www.mpich.org/>`_ (if parallel runs are required)

- **Required files**

**Fasta files of the genome sequence**

**Gff files of the genome annotation**

**Gff files of the TSSs:** If the TSS gff file is not computed by ANNOgesic, the libraries and wiggle files are necessary.
Please refer to the ``The format of libraries for import to ANNOgesic`` in order to assign the correct format.

- **Arguments**

::

    usage: annogesic promoter [-h] [--MEME_path MEME_PATH]
                              [--fasta_folder FASTA_FOLDER]
                              [--TSS_folder TSS_FOLDER] [--num_motif NUM_MOTIF]
                              [--nt_before_TSS NT_BEFORE_TSS] [--e_value E_VALUE]
                              [--motif_width MOTIF_WIDTH] [--parallel PARALLEL]
                              [--TSS_source] [--tex_libs TEX_LIBS]
                              [--tex_wig_path TEX_WIG_PATH]
                              [--annotation_folder ANNOTATION_FOLDER]
                              [--combine_all]
                              [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --MEME_path MEME_PATH
                            path of MEME.
      --fasta_folder FASTA_FOLDER, -f FASTA_FOLDER
                            Path of genome fasta folder.
      --TSS_folder TSS_FOLDER, -t TSS_FOLDER
                            Path of TSS gff folder.
      --num_motif NUM_MOTIF, -n NUM_MOTIF
                            The number of motifs that you want to detect. Default
                            is 10.
      --nt_before_TSS NT_BEFORE_TSS, -b NT_BEFORE_TSS
                            The number of upstream nucleotides of TSS for promoter
                            prediction. Default is 50.
      --e_value E_VALUE, -e E_VALUE
                            The maximum e value. Default is 0.05.
      --motif_width MOTIF_WIDTH, -w MOTIF_WIDTH
                            The length for motif detection. For detecting a range
                            of length, please insert "-" between two values.
                            Moreover, if multiple motif length need to be
                            assigned, please use comma to separate them. for
                            example, 50,2-10. It means that the length of motif
                            for detection is 50 and within 2 to 10. The number
                            should be less or equal than --nt_before_TSS. Default
                            is 50.
      --parallel PARALLEL, -pl PARALLEL
                            This function is for running parallel. Please input
                            the number of parallel runs.
      --TSS_source, -s      If the TSS gff file is not generated by ANNOgesic,
                            this function can classify TSS and generate the proper
                            format for promoter detection. Default is True (from
                            ANNOgesic)
      --tex_libs TEX_LIBS, -tl TEX_LIBS
                            If --TSS_source is False, please assign the name of
                            TEX+/- library. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --tex_wig_path TEX_WIG_PATH, -tw TEX_WIG_PATH
                            If --TSS_source is False, please assign the path of
                            TEX+/- wig folder.
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            If --TSS_source is False, please assign the path of
                            genome annotation gff file.
      --combine_all, -c     This function will combine all TSS files in
                            "TSS_folder" to generate global promoter motifs.
                            Default is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/promoter_analysis``. The output folder is following:

**allfasta:** If ``--combine_all`` is True, it will combine all TSS files in ``--TSS_folder`` 
to generate promoter motifs. The results will be stored in this folder.

**fasta_class:** The fasta files of different TSS types.

**$STRAINNAME:** Store output of MEME based on different TSS types. The sub-folder format is ``promoter_motifs_$FILENAME_$STRAINNAME_$TSSTYPE_$PROMOTERLEGNTH``.
ex: ``promoter_motifs_NC_000915.1_allstrain_internal_45_nt``.
"NC_000915.1", "allstrain", "primary" and "45_nt" are gff filename, strain name, TSS type and upstream nucleotides of TSS resepectively.
If strain name is "allstrain", it means that if there are multiple strains in the gff file, 
the result is generateed by the information of all strains without considering the difference of strains. 
If there is only one strain in the gff file, the strain name will be assigned as "allstrain". Several files are stored in the sub-folder:

	**Figures of the promoter motif:** Contains EPS and PNG files.

	**Details of the promoter motif:** Contains HTML file, XML file and TXT file. These files include the TSS information.

	**Promoter table:** ``meme.csv`` is the promoter table which also includes the TSS information. 
	Moreover, it can used as an input for sRNA detectionn (``srna``). Please check the section ``srna``.

**TSS_class:** If the TSSs are not computed by ANNOgesic, ``TSS_class`` will be generated for classification of TSS.
TSS gff files with TSS types will be stored here.

operon
----------

``operon`` will group TSSs, genes/CDSs/tRNAs/rRNAs, transcripts, terminators and UTRs to operons and 
sub-operons.

- **Required files**

**Gff files of the TSSs**

**Gff files of the genome annotations**

**Gff files of the transcripts**

**Gff files of the 5'UTRs**

**Gff files of the 3'UTRs**

- **Optional input files**

**Gff files of the terminator**

- **Arguments**

::

    usage: annogesic operon [-h] [--TSS_folder TSS_FOLDER]
                            [--annotation_folder ANNOTATION_FOLDER]
                            [--transcript_folder TRANSCRIPT_FOLDER]
                            [--UTR5_folder UTR5_FOLDER]
                            [--UTR3_folder UTR3_FOLDER]
                            [--term_folder TERM_FOLDER] [--TSS_fuzzy TSS_FUZZY]
                            [--term_fuzzy TERM_FUZZY] [--min_length MIN_LENGTH]
                            [--statistics] [--combine_gff]
                            [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --TSS_folder TSS_FOLDER, -t TSS_FOLDER
                            Path of the TSS gff folder.
      --annotation_folder ANNOTATION_FOLDER, -g ANNOTATION_FOLDER
                            Path of the genome annotation gff folder.
      --transcript_folder TRANSCRIPT_FOLDER, -a TRANSCRIPT_FOLDER
                            Path of the transcript gff folder.
      --UTR5_folder UTR5_FOLDER, -u5 UTR5_FOLDER
                            Path of the 5'UTR gff folder.
      --UTR3_folder UTR3_FOLDER, -u3 UTR3_FOLDER
                            Path of the 3'UTR gff folder.
      --term_folder TERM_FOLDER, -e TERM_FOLDER
                            If terminator information can be provided, please
                            assign the path of terminator gff folder here.
      --TSS_fuzzy TSS_FUZZY, -tf TSS_FUZZY
                            The fuzzy for comparing between TSS and transcript
                            assembly. Default is 5.
      --term_fuzzy TERM_FUZZY, -ef TERM_FUZZY
                            The fuzzy for comparing bewteen terminator and
                            transcript assembly. Default is 30.
      --min_length MIN_LENGTH, -l MIN_LENGTH
                            The minimum length of operon. Default is 20.
      --statistics, -s      Doing statistics for operon analysis. Default is
                            False. The name of statistics file is -
                            stat_operon_$STRAIN_NAME.csv.
      --combine_gff, -c     Combine all assigned features to one gff file. Default
                            is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/operon``. The output folders are as followinig:

**gffs:** Stores gff files which are integrated the information of TSSs, annotations, 
transcripts, 5'UTRs, and 3'UTRs and assign parent transcript to all features (presented by 
**Parent** in attributes of gff files).

**tables:** The tables of operons which store all information of operons and sub-operons.

The meanings of each column is following:

	**Operon_ID:** Operon ID.

	**strain:** Strain name.

	**Operon_position:** Starting point and end point of the operon.

	**Strand:** Strand of the operon.

	**Number_of_suboperon:** The amount of sub-operons in this operon region.

	**Position_of_suboperon:** Starting point and end point of the sub-operons.

	**Start_with_TSS:** This operon starts with TSS or not.

	**Number_of_TSS:** The number of the TSSs which are located on this operon.

	**Terminated_with_terminator:** This operon ends with TSS or not.

	**Number_of_terminator:** The number of the terminators which are associated with this operon.

	**Number_of_gene_associated_suboperon:** The number of the genes which are associated with the sub-operon.

	**Number_of_gene_associated_operon:** The number of the genes which are associated with the operon.

	**Associated_genes_with_suboperon:** Locus tag of the genes which are associated with the sub-operon.

	**Associated_genes_with_whole_operon:** Locus tag of the genes which are associated with the operon.

**statistics:** Stores statistics file which includes the number of sub-operons, monocistronic operon, polycistronic operon, etc.

circrna
--------------

``circrna`` can detect the potential circular RNAs via `Segemehl <http://www.bioinf.uni-leipzig.de/Software/segemehl/>`_. 
Moreover, the false positive can be removed by checking genome annotation files and quality of splicing site detection. 
The user can assign reads for mapping and detecting circular RNAs or assign alignment files to skip mapping.
BE CAREFUL, the alignment files must be mapped by `Segemehl <http://www.bioinf.uni-leipzig.de/Software/segemehl/>`_ 
with ``-S`` or ``circrna`` can't find the proper candidates.

- **Required tools**

`Segemehl <http://www.bioinf.uni-leipzig.de/Software/segemehl/>`_.

- **Required files**

**Fasta files of reads or alignment files (BAM or SAM file):** If you want to use alignment files directly, they should be 
mapped by `Segemehl <http://www.bioinf.uni-leipzig.de/Software/segemehl/>`_ with ``-S``.

**Fasta files of the genome annotation**

**Gff files of the genome annotation**

- **Arguments**

::

    usage: annogesic circrna [-h] [--segemehl_folder SEGEMEHL_FOLDER]
                             [--samtools_path SAMTOOLS_PATH] [--align]
                             [--tex_bam_path TEX_BAM_PATH]
                             [--fragmented_bam_path FRAGMENTED_BAM_PATH]
                             [--read_path READ_PATH] [--process PROCESS]
                             [--fasta_path FASTA_PATH]
                             [--annotation_path ANNOTATION_PATH]
                             [--support_reads SUPPORT_READS]
                             [--start_ratio START_RATIO] [--end_ratio END_RATIO]
                             [--ignore_hypothetical_protein IGNORE_HYPOTHETICAL_PROTEIN]
                             [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --segemehl_folder SEGEMEHL_FOLDER, -sg SEGEMEHL_FOLDER
                            Please assign the folder of segemehl.
      --samtools_path SAMTOOLS_PATH, -st SAMTOOLS_PATH
                            Please assign the path of samtools.
      --align, -a           This function will map the reads (included splice
                            detection) by segemehl. If you already used segemehl
                            with -S to map the reads, you can skip this step. BE
                            CAREFUL, this function only use default parameters of
                            segemehl to map the reads. Moreover, all read files in
                            ANNOgesic/input/reads will be mapped. If some specific
                            functions of segemehl need to be implemented, please
                            directly run segemehl (MUST run with -S). Default is
                            False.
      --tex_bam_path TEX_BAM_PATH, -tb TEX_BAM_PATH
                            If Bam files of TEX+/- libraries can be provided,
                            Please assign the path of TEX+/- Bam folder.
      --fragmented_bam_path FRAGMENTED_BAM_PATH, -fb FRAGMENTED_BAM_PATH
                            If Bam files of fragmented libraries can be provided,
                            Please assign the path of fragmented Bam folder.
      --read_path READ_PATH, -rp READ_PATH
                            If --align is True, please assign the path of reads.
      --process PROCESS, -p PROCESS
                            The number of parallels processes for --align. Default
                            is 10.
      --fasta_path FASTA_PATH, -f FASTA_PATH
                            Path of the genome fasta folder.
      --annotation_path ANNOTATION_PATH, -g ANNOTATION_PATH
                            Path of the genome annotation gff folder.
      --support_reads SUPPORT_READS, -s SUPPORT_READS
                            The minimum supported reads of circular RNA. Default
                            is 10.
      --start_ratio START_RATIO, -sr START_RATIO
                            The minimum ratio -- (supported reads of circRNA / all
                            reads) at starting point of candidate. Default is 0.5.
      --end_ratio END_RATIO, -er END_RATIO
                            The meaning is similar to --start_ratio. This value is
                            for end point of candidate. Default is 0.5.
      --ignore_hypothetical_protein IGNORE_HYPOTHETICAL_PROTEIN, -ih IGNORE_HYPOTHETICAL_PROTEIN
                            This function is for ignoring hypothetical protein in
                            genome annotation file. Default is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/circRNA``. The output folders are following:

**segemehl_align:** If ``circrna`` starts from read mapping, the folder is for results of mapping.

**segemehl_splice:** The results of splicing detection. The information of the splicing tables, please 
refer to `Segemehl <http://www.bioinf.uni-leipzig.de/Software/segemehl/>`_.

**gffs:** Stores gff files of the circular RNAs. ``$STRAINNAME_best.gff`` is gff files for the best result after checking 
genome annotation and quality of splicing. ``$STRAINNAME_all.gff`` is for all candidates without filering.
Some useful information can be found in the tags of the attributes within the circular RNA gff file.
Based on this information, we can know the details of the specific circular RNA. The tags are as following:

support_reads=41;read_at_start=1.0;read_at_end=1.0;confliction=NA;method=segemehl

	**support_reads:** The number of reads which support the circular RNA.

	**read_at_start:** (supported reads / total reads) at starting point of the circular RNA.

	**read_at_end:** (supported reads / total reads) at end point of the circular RNA.

	**conflict:** The circular RNA overlap genome annotation or not.

	**method:** The circular RNA is detected by which method.

**circRNA_tables:** Stores tables of the circular RNAs with more details. The meaning of each column is as following:

	**ID:** ID of this circular RNA.

	**strain:** Strain name.

	**strand:** Strand of the circular RNA.

	**start:** Starting point of the circular RNA.

	**end:** End point of the circular RNA.

	**annotation_overlap:** If there is genome annotation which overlap this circular RNA, the overlapped feature will be showed here.

	**supported_reads:** The number of reads which support the circular RNA.

	**supported_reads/reads_at_start:** (supported reads / total reads) at starting point of the circular RNA.

	**supported_reads/reads_at_end:** (supported reads / total reads) at end point of the circular RNA.	

go_term
----------

``go_term`` can retreive the information of Gene Ontology from Uniprot.
Some analysis of Go terms can be done as well.

- **Required files**

**Uniprot mapping table:** `idmapping_selected.tab from Uniprot <http://www.uniprot.org/downloads>`_.

**GOslim file:** `goslim.obo <http://geneontology.org/page/go-slim-and-subset-guide>`_.

**GO file:** `go.obo <http://geneontology.org/page/download-ontology>`_.

**Gff files of the genome annotation**

- **Optional input files**

**Gff files of the transcript:** For detecting the GO terms only based on expressed CDSs.

- **Arguments**

::

    usage: annogesic go_term [-h] [--annotation_path ANNOTATION_PATH]
                             [--transcript_path TRANSCRIPT_PATH]
                             [--UniProt_id UNIPROT_ID] [--go_obo GO_OBO]
                             [--goslim_obo GOSLIM_OBO]
                             [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --annotation_path ANNOTATION_PATH, -g ANNOTATION_PATH
                            Path of the genome annotation gff folder.
      --transcript_path TRANSCRIPT_PATH, -a TRANSCRIPT_PATH
                            If the path of transcript gff folder is provided, GO
                            term can be retreived based on expressed CDS and all
                            CDS.
      --UniProt_id UNIPROT_ID, -u UNIPROT_ID
                            Path of the UniProt ID mapping database. Default is
                            ANNOgesic/input/database/idmapping_selected.tab.
      --go_obo GO_OBO, -go GO_OBO
                            The path of go.obo. Default is
                            ANNOgesic/input/database/go.obo.
      --goslim_obo GOSLIM_OBO, -gs GOSLIM_OBO
                            The path of goslim.obo. Default is
                            ANNOgesic/input/database/goslim_generic.obo.

- **Output files**

Output files are stored in ``ANNOgesic/output/Go_term``. If gff files of the transcript is assigned, 
two sub-folders will be generated. Results of the expressed CDSs are stored in ``ANNOgesic/output/Go_term/expressed_CDS`` and 
results of all CDS are stored in ``ANNOgesic/output/Go_term/all_CDS``.

**Go_term_results:** Stores tables of the Go terms information. The meaning of each column is as following:

	**strain:** Strain name.

	**strand:** Strand of the CDS.

	**start:** Starting point of this CDS.

	**end:** End point of this CDS.

	**protein_id** Protein ID of this CDS.

	**Go_term** Go term of This CDS.

**statistics:** Stores statistic files and figures.

	**GO term with corresponding amount:** Format of the filename is ``stat_$STRAINNAME.csv``.

	**Figures of the three GO term classes:** All figures are stored in the sub-folder - ``figs``. 
	``$STRAINNAME_biological_process.png``, ``$STRAINNAME_cellular_component.png``, 
	``$STRAINNAME_molecular_function.png`` and ``$STRAINNAME_three_roots.png`` are the figures of 
	biological process, cellular component, molecular_function and three roots of GO term classes respectively.

srna_target
---------------

``srna_target`` can search potential targets of the sRNA via different programs 
(RNAup or RNAplex or both). We recommand running with both 
programs. ``srna_target`` can also compare the both results and provide the best ones.

- **Required tools**

`ViennaRNA <http://www.tbi.univie.ac.at/RNA/>`_ .

- **Required files**

**Gff files of the genome annotation**

**Gff files of the sRNAs**

**Fasta files of the genome**

- Arguments

::

    usage: annogesic srna_target [-h] [--Vienna_folder VIENNA_FOLDER]
                                 [--annotation_path ANNOTATION_PATH]
                                 [--fasta_path FASTA_PATH] [--sRNA_path SRNA_PATH]
                                 [--query_sRNA QUERY_SRNA] [--program PROGRAM]
                                 [--interaction_length INTERACTION_LENGTH]
                                 [--window_size_target WINDOW_SIZE_TARGET]
                                 [--span_target SPAN_TARGET]
                                 [--window_size_srna WINDOW_SIZE_SRNA]
                                 [--span_srna SPAN_SRNA]
                                 [--unstructured_region_RNAplex_target UNSTRUCTURED_REGION_RNAPLEX_TARGET]
                                 [--unstructured_region_RNAplex_srna UNSTRUCTURED_REGION_RNAPLEX_SRNA]
                                 [--unstructured_region_RNAup UNSTRUCTURED_REGION_RNAUP]
                                 [--energy_threshold ENERGY_THRESHOLD]
                                 [--duplex_distance DUPLEX_DISTANCE] [--top TOP]
                                 [--process_rnaplex PROCESS_RNAPLEX]
                                 [--process_rnaup PROCESS_RNAUP]
                                 [--continue_rnaup]
                                 [--potential_target_start POTENTIAL_TARGET_START]
                                 [--potential_target_end POTENTIAL_TARGET_END]
                                 [--target_feature TARGET_FEATURE]
                                 [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --Vienna_folder VIENNA_FOLDER
                            Please assign the folder of Vienna package. It should
                            include RNAplfold, RNAup and RNAplex.
      --annotation_path ANNOTATION_PATH, -g ANNOTATION_PATH
                            Path of the genome annotation gff folder.
      --fasta_path FASTA_PATH, -f FASTA_PATH
                            Path of the genome fasta folder.
      --sRNA_path SRNA_PATH, -r SRNA_PATH
                            Path of the sRNA gff folder.
      --query_sRNA QUERY_SRNA, -q QUERY_SRNA
                            Please assign the query sRNA. The input format is
                            $STRAIN:$STRAND:$START:$END. If multiple sRNAs need to
                            be assigned, please use comma to separate them.For
                            example,
                            NC_007795.1:+:200:534,NC_007795.1:-:6767:6900. If you
                            want to detect all sRNAs in gff file, please assign
                            "all". Default is all.
      --program PROGRAM, -p PROGRAM
                            The program for detecting sRNA-mRNA interaction.
                            Please assign "RNAplex" or "RNAup" or "both". Default
                            is both.
      --interaction_length INTERACTION_LENGTH, -i INTERACTION_LENGTH
                            Maximum length of an interaction. Default is 30.
      --window_size_target WINDOW_SIZE_TARGET, -wt WINDOW_SIZE_TARGET
                            This value is the average of the pair probabilities
                            over windows for mRNA target. This function only works
                            for "RNAplex". Default is 240.
      --span_target SPAN_TARGET, -st SPAN_TARGET
                            This value is the maximum allowed separation of a base
                            pair to span for mRNA target. This function only works
                            for "RNAplex". Default is 160.
      --window_size_srna WINDOW_SIZE_SRNA, -ws WINDOW_SIZE_SRNA
                            The meaning is similar to --window_size_target. This
                            value is for sRNA. Default is 30.
      --span_srna SPAN_SRNA, -ss SPAN_SRNA
                            The meaning is similar to --span_target. This value is
                            for sRNA. Default is 30.
      --unstructured_region_RNAplex_target UNSTRUCTURED_REGION_RNAPLEX_TARGET, -ut UNSTRUCTURED_REGION_RNAPLEX_TARGET
                            Calculate the mean probability that from length 1 to
                            this value are unpaired for mRNA target. This function
                            only works for "RNAplex". Default is 30.
      --unstructured_region_RNAplex_srna UNSTRUCTURED_REGION_RNAPLEX_SRNA, -us UNSTRUCTURED_REGION_RNAPLEX_SRNA
                            The meaning is similar to
                            --unstructured_region_RNAplex_target. This value is
                            for sRNA. Default is 30.
      --unstructured_region_RNAup UNSTRUCTURED_REGION_RNAUP, -uu UNSTRUCTURED_REGION_RNAUP
                            Compute the mean probability that from length 1 to
                            this value are unpaired. This function only works for
                            "RNAup". Default is 30.
      --energy_threshold ENERGY_THRESHOLD, -e ENERGY_THRESHOLD
                            The minimum energy for a duplex. This function only
                            works for "RNAplex". Default is -8.
      --duplex_distance DUPLEX_DISTANCE, -d DUPLEX_DISTANCE
                            Distance between target 3' ends of two consecutive
                            duplexes. This function only works for "RNAplex".
                            Default is 20.
      --top TOP, -t TOP     The minimum ranking number of targets which will be
                            included to final output. Default is 20.
      --process_rnaplex PROCESS_RNAPLEX, -pp PROCESS_RNAPLEX
                            The number of parallel processes for running RNAplex.
                            Default is 5.
      --process_rnaup PROCESS_RNAUP, -pu PROCESS_RNAUP
                            The number of parallel processes for running RNAup.
                            Default is 20.
      --continue_rnaup, -cr
                            The running time of RNAup is long if numerous sRNAs
                            are assigned. This function can continue to run RNAup
                            based on the previous intermediate results if the
                            previous process was crushed. Default is False.
      --potential_target_start POTENTIAL_TARGET_START, -ps POTENTIAL_TARGET_START
                            --potential_target_start and --potential_target_end
                            will be applied to extract the potential target. This
                            value indicates the number of nucleotides at the
                            upstream of --target_feature starting point which will
                            be extracted as part of the potential target. Default
                            is 200.
      --potential_target_end POTENTIAL_TARGET_END, -pe POTENTIAL_TARGET_END
                            --potential_target_start and --potential_target_end
                            will be applied to extract the potential target. This
                            value indicates the number of nucleotides at the
                            downstream of --target_feature starting point which
                            will be extracted as part of the potential target.
                            Default is 150.
      --target_feature TARGET_FEATURE, -tf TARGET_FEATURE
                            This is the feature name for extracting as potential
                            targets. If multiple features need to be assigned,
                            please use comma to separate them. Ex: CDS,gene.
                            Default is CDS.

- **Output files**

Output files are stored in ``$ANNOgesic/output/sRNA_targets``.

**RNAplex:** Stores all results of RNAplex. ``$STRAIN_RNAplex.txt`` is raw results of RNAplex.
It includes the information of binding situations. ``$STRAIN_RNAplex_rank.csv`` is the results 
that sorted by binding energy. The meaning of each column in ``$STRAIN_RNAplex_rank.csv`` is following:

	**sRNA:** sRNA name which is used in sRNA gff file.

	**strain:** Strain name.

	**sRNA_position:** Starting point and end point of this sRNA.

	**sRNA_interacted_position_RNAplex:** The interacted region of this sRNA.

	**sRNA_strand:** Strand of this sRNA.

	**target:** Locus tag or gene name of the target mRNA.

	**target_position:** Starting point and end point of this mRNA.

	**target_interacted_position_RNAplex:** The interacted region of this mRNA.

	**target_strand:** Strand of this target mRNA.

	**energy_RNAplex:** Interaction energy change of this interaction.

	**rank_RNAplex:** Ranking of the interaction.

**RNAup:** Stored all results of RNAup. ``$STRAIN_RNAup.txt`` is raw results of RNAup.
It includes the information of binding situations. ``$STRAIN_RNAup_rank.csv`` is the results
that sorted by binding energy. The meaning of each column is similar to the table of RNAplex.

**merge:** Store the results which merged ``RNAplex`` and ``RNAup``. ``$STRAIN_merge.csv`` contains all candidates of the both programs. 
``$STRAIN_overlap.csv`` contains the results which are top 20 (default) in the both methods. 
The meaning of each column is similar to the table of RNAplex.

**sRNA_seqs:** Stores fasta sequences of the sRNAs.

**target_seqs:** Stores fasta sequences of the potential targets.

ppi_network
-------------

``ppi_network`` can retrieve the data from `STRING <http://string-db.org/>`_. 
Then using `PIE <http://www.ncbi.nlm.nih.gov/CBBresearch/Wilbur/IRET/PIE/>`_ to search 
the supported literatures of the protein-protein interaction networks. 

- **Required files**

**Species table of STRING:** `species.vXXXX.txt from STRING <http://string-db.org/cgi/download.pl>`_.

**Gff files of the genome annotation**

- **Arguments**

::

    usage: annogesic ppi_network [-h] [--gff_path GFF_PATH]
                                 [--proteinID_strains PROTEINID_STRAINS]
                                 [--without_strain_pubmed]
                                 [--species_STRING SPECIES_STRING] [--score SCORE]
                                 [--node_size NODE_SIZE] [--query QUERY]
                                 [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --gff_path GFF_PATH, -g GFF_PATH
                            Path of the genome annotation gff folder. BE CAREFUL:
                            The gff files MUST have proper locus_tag item in the
                            attributes. The locus_tag items can be assigned by a
                            real locus tag like locus_tag=SAOUHSC_00003, or a gene
                            name, such as, locus_tag=dnaA.
      --proteinID_strains PROTEINID_STRAINS, -s PROTEINID_STRAINS
                            This is for query strain and filename. In order to
                            retrieve the data from STRING and Pubmed, The strain
                            name must be assigned. If the query strain is not
                            available in database, you can assign the close strain
                            of your query strain. For example, the query strain is
                            Staphylococcus aureus HG003, but there is no
                            Staphylococcus aureus HG003 in STRING database.
                            Therefore, Staphylococcus aureus NCTC 8325 (close
                            strain) can be used as reference. The input format can
                            be: Staphylococcus_aureus_HG003.gff:Staphylococcus_aur
                            eus_HG003:"Staphylococcus aureus 8325":"Staphylococcus
                            aureus". or Staphylococcus_aureus_HG003.gff:Staphyloco
                            ccus_aureus_HG003:"93061":"Staphylococcus aureus". or 
                            Staphylococcus_aureus_HG003.gff:Staphylococcus_aureus_
                            HG003:"Staphylococcus aureus NCTC
                            8325":"Staphylococcus aureus". (gff_filename:gff_strai
                            n_name:STRING_name:Pubmed_name). First one is the gff
                            file name. Second one is the strain name in gff files.
                            Third one is from STRING (please check the
                            species.vXXX.txt of STRING, taxon_id,
                            STRING_name_compact or official_name_NCBI can be
                            assigned here), and the fourth one is for searching
                            Pubmed. If you want to detect for multiple strains,
                            just put comma between these strains. Before running
                            it, please check the species table (species.vXXX.txt)
                            which should be located in ANNOgesic/input/database
                            .If the file was not downloaded before, please
                            download it. BE CAREFUL, if the assigned name is with
                            spaces, please put "" at two ends. For searching
                            Pubmen, if a specific name is assigned, it may not be
                            able to find the related literatures.
      --without_strain_pubmed, -n
                            Retrieving the literatures from Pubmed without
                            assigning strains, Default is False.
      --species_STRING SPECIES_STRING, -d SPECIES_STRING
                            Please assign path of the species table of STRING
                            (species.vXXX.txt).
      --score SCORE, -ps SCORE
                            Please assign minimum PIE score for searching
                            literatures. The value is from -1 (worst) to 1 (best).
                            Default is 0.
      --node_size NODE_SIZE, -ns NODE_SIZE
                            Please assign the size of nodes in figure, default is
                            4000.
      --query QUERY, -q QUERY
                            Please assign the query protein here. The format is
                            $STRAINOFGFF:$START_POINT:$END_POINT:$STRAND.If
                            multiple proteins need to be assigned, please use
                            comma to separate them. For example, Staphylococcus_au
                            reus_HG003:345:456:+,Staphylococcus_aureus_HG003:2000:
                            3211:-. For computing all protein, just type "all".
                            Default is all.

- **Output files**

Output files are stored in ``$ANNOgesic/output/PPI``. The output folders are as following:

**best_results:** Stores the results which the scores of `PIE <http://www.ncbi.nlm.nih.gov/CBBresearch/Wilbur/IRET/PIE/>`_
of supported literature are higher than ``--score``.

**all_results:** Stores the results of all protein-protein interactions
(including the low score(`PIE <http://www.ncbi.nlm.nih.gov/CBBresearch/Wilbur/IRET/PIE/>`_) literatures).

Unter "best_results" and "all_results", several files and folders are generated:

	**Results of searching literatures without assigning a specific strain:** ``$STRAIN_without_strain.csv``. 

	**Results of searching literatures with assigning a specific strain:** ``$STRAIN_with_strain.csv``.
 
	**without_strain:** Stores all interaction information which is searched without assigning a specific strain. 

	**with_strain:** Stores all interaction information which is searched with assigning a specific strain. 

**figures:** Stores the protein-protein networks of the query proteins. Thickness represents how many literatures can be found for the interactions. 
Solid line means that strong supported literature can be found. Dash-dot line 
means that the supported literatures are very weak. Dot line means that no supported literatures can be found. 
Color is the best score of the supported literatures of the interactions.

subcellular_localization
------------------

``subcellular localization`` can predict the subcellular localization of CDSs. 
Some statistics and visualization files are provided as well.

- **Required tools**

`Psortb <http://www.psort.org/psortb/>`_.

- **Required files**

**Gff files of the genome annotation**

**Fasta files of the genome sequence**

- **Optional input files**

**Gff files of the transcript:** For detecting subcellular localization only based on expressed CDSs.

- **Arguments**

::

    usage: annogesic subcellular_localization [-h] [--Psortb_path PSORTB_PATH]
                                              [--gff_path GFF_PATH]
                                              [--fasta_path FASTA_PATH]
                                              [--transcript_path TRANSCRIPT_PATH]
                                              [--bacteria_type BACTERIA_TYPE]
                                              [--difference_multi DIFFERENCE_MULTI]
                                              [--merge_to_gff]
                                              [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --Psortb_path PSORTB_PATH
                            If you want to assign the path of Psortb, please
                            assign here.
      --gff_path GFF_PATH, -g GFF_PATH
                            The path of genome annotation gff folder.
      --fasta_path FASTA_PATH, -f FASTA_PATH
                            The path of genome fasta folder.
      --transcript_path TRANSCRIPT_PATH, -a TRANSCRIPT_PATH
                            If the path of transcript gff folder is provided, it
                            can get the results based on expressed CDS and all
                            CDS.
      --bacteria_type BACTERIA_TYPE, -b BACTERIA_TYPE
                            The type of bacteria (Gram-positive or Gram-negative).
                            Please assign 'positive' or 'negative'.
      --difference_multi DIFFERENCE_MULTI, -d DIFFERENCE_MULTI
                            For the protein which have multiple locations, if the
                            difference of psorb scores is smaller than
                            --difference_multi, it will be printed out as well.
                            Default is 0.5. The maximum value is 10.
      --merge_to_gff, -m    Merging the information to genome annotation gff file.
                            Default is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/subcellular_localization``. If gff files of the transcript is assigned,
two sub-folders will be generated. Results of the expressed CDSs are stored in 
``ANNOgesic/output/subcellular_localization/expressed_CDS`` and
results of all CDS are stored in ``ANNOgesic/output/subcellular_localization/all_CDS``.

``psortb_results``: Stores the results of Psortb:

	**Raw output data of Psortb:** Format of the filename is ``$STRIAN_raw.txt``.

	**Table of subcellular localization:** Format of the filename is ``$STRIAN_table.csv``. 
	The meaning of each column is as following:

		**strain:** Strain name.

		**protein:** Protein ID.

		**strand:** Strand of this protein.

		**start:** Starting point of this protein.

		**end:** End point of this protein.

		**location:** Predicted subcellular localization of this protein.

		**score:** Psortb score.

**statistics:** Stores statistic files and figures.

	**Subcellular localization with corresponding amount:** Format of the filename is ``stat_$STRAIN_sublocal.csv``.

	**Figure of Subcellular localization with corresponding amount:** Format of the filename is ``$FILENAME_$STRAIN_sublocal.png``.

riboswitch_thermometer
--------------

``riboswitch_thermometer`` can search ribosome binding sites between 
TSSs（the starting point of transcript was assigned if no TSS was detected) 
and its downstream CDSs. Then using `Infernal <http://infernal.janelia.org/>`_ to scan 
riboswitch or RNA thermometer in `Rfam <http://rfam.xfam.org/>`_.

- **Required tools**

`Infernal <http://infernal.janelia.org/>`_.

- **Required files**

`Rfam <http://rfam.xfam.org/>`_.

**Gff files of the genome annotation**

**Fasta files of the genome sequence**

**Rfam ID files of the riboswitch or RNA thermometer:** The file should contain Rfam ID, 
Name and Description of riboswitchs or RNA thermometer (refer to 
``Riboswitch and RNA thermometer dataset of Rfam``). You can download the file from our 
`Github <https://github.com/Sung-Huan/ANNOgesic>`_ (Rfam_riboswitch_ID.csv or Rfam_RNA_thermometer_ID.csv).

- **Arguments**

::

    usage: annogesic riboswitch_thermometer [-h] [--program PROGRAM]
                                            [--infernal_path INFERNAL_PATH]
                                            [--riboswitch_ID RIBOSWITCH_ID]
                                            [--RNA_thermometer_ID RNA_THERMOMETER_ID]
                                            [--gff_path GFF_PATH]
                                            [--tss_path TSS_PATH]
                                            [--UTR_length UTR_LENGTH]
                                            [--transcript_path TRANSCRIPT_PATH]
                                            [--fasta_path FASTA_PATH]
                                            [--Rfam RFAM] [--e_value E_VALUE]
                                            [--output_all] [--fuzzy FUZZY]
                                            [--fuzzy_rbs FUZZY_RBS]
                                            [--start_codon START_CODON]
                                            [--max_dist_rbs MAX_DIST_RBS]
                                            [--min_dist_rbs MIN_DIST_RBS]
                                            [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --program PROGRAM, -p PROGRAM
                            Please assign the feature that you want to detect. The
                            options can be "riboswitch", "thermometer", "both".
                            Default is both.
      --infernal_path INFERNAL_PATH, -if INFERNAL_PATH
                            Please assign the path of Infernal folder which
                            include cmscan and cmsearch.
      --riboswitch_ID RIBOSWITCH_ID, -ri RIBOSWITCH_ID
                            If --program is "riboswitch" or "both", please assigh
                            the file path of riboswitch ID in Rfam. The file
                            should include the Accession, ID and Description of
                            riboswitch in Rfam.
      --RNA_thermometer_ID RNA_THERMOMETER_ID, -ti RNA_THERMOMETER_ID
                            If --program is "thermometer" or "both", please assigh
                            the file path of RNA thermometer ID of Rfam. The file
                            should include the Accession, ID and Description of
                            RNA thermometer in Rfam.
      --gff_path GFF_PATH, -g GFF_PATH
                            Path of the annotation gff folder.
      --tss_path TSS_PATH, -t TSS_PATH
                            Path of the TSS gff folder.
      --UTR_length UTR_LENGTH, -u UTR_LENGTH
                            The UTR length. Default is 300.
      --transcript_path TRANSCRIPT_PATH, -a TRANSCRIPT_PATH
                            Path of the transcript gff folder.
      --fasta_path FASTA_PATH, -f FASTA_PATH
                            Path of the genome fasta folder.
      --Rfam RFAM, -R RFAM  Path of the Rfam CM database.
      --e_value E_VALUE, -e E_VALUE
                            The maximum e value. Default is 0.001.
      --output_all, -o      One query sequence may fit multiple riboswitches or
                            RNA thermometers. If --output_all is True, all results
                            will be printed out. Otherwise, only the best one will
                            be printed out. Default is False.
      --fuzzy FUZZY, -z FUZZY
                            The fuzzy (nucleotides) for extracting the sequences
                            of potential riboswitches or RNA thermometers. Default
                            is 10.
      --fuzzy_rbs FUZZY_RBS, -zr FUZZY_RBS
                            The number of nucleotides of ribosome binding site can
                            be different with AGGAGG. Default is 2.
      --start_codon START_CODON, -ac START_CODON
                            The types of start coden. If multiple types need to be
                            assigned, please use comma to separate them. Default
                            is ATG.
      --max_dist_rbs MAX_DIST_RBS, -Mr MAX_DIST_RBS
                            The maximum distance (nucleotides) between ribosome
                            binding site and start codon. Default is 14.
      --min_dist_rbs MIN_DIST_RBS, -mr MIN_DIST_RBS
                            The minimum distance (nucleotides) between ribosome
                            binding site and start codon. Default is 5.

- **Output files**

Output files of the riboswitch are stored in ``$ANNOgesic/output/riboswitch`` and 
output files of the RNA thermometer are stored in ``$ANNOgesic/output/RNA_thermometer``.

Names of the output folders are following:

**scan_Rfam:** Stores the results of searching to Rfam with ``cmscan`` (`Infernal <http://infernal.janelia.org/>`_). 

**gffs:** Stores gff files of riboswitchs / RNA_thermometer. 
Some useful information can be found in the tags of the attributes within the gff file.
Based on this information, we can know the details of the specific riboswitchs / RNA_thermometer. 
The tags are as following:

	**rfam_id:** Rfam ID of this riboswitchs / RNA_thermometer.

	**e-value:** E-value of searching this riboswitchs / RNA_thermometer to Rfam.

	**method:** This riboswitchs / RNA_thermometer is detected by which method.

**tables:** Stores tables of riboswichs / RNA_thermometer with more details. The meaning of each column in this table is as following:

	**ID:** Riboswichs / RNA_thermometer ID.

	**strain:** Strain name.

	**strand:** Strand of the riboswichs / RNA_thermometer.

	**associated_CDS:** Downstream CDS of the riboswichs / RNA_thermometer.

	**start_genome:** This riboswichs / RNA_thermometer starts from which position of the genome.

	**end_genome:** This riboswichs / RNA_thermometer ends to which position of the genome.

	**Rfam:** Rfam ID of this riboswichs / RNA_thermometer.

	**e_value:** E-value of searching this riboswitchs / RNA_thermometer to Rfam.

	**start_align:** Position of this riboswichs / RNA_thermometer can be aligned to the genome.

	**end_align:** Position this riboswichs / RNA_thermometer can be aligned to the genome.

**statistics:** Stores the file which contains the riboswichs / RNA_thermometer with correspnding amount.

crispr
---------------
``crispr`` integrates CRISPR Recognition Tool (`CRT <http://www.room220.com/crt/>`_) which can detect the repeat 
units and spacer of CRISPR. Moreover, the false positives can be removed by comparing candidates with genome annotation.

- **Required tools**

`CRT <http://www.room220.com/crt/>`_.

- **Required files**

**Fasta file of the genome sequence**

- **Optional input files**

**Gff files of the genome annotation:** This file can be used for removing false positives.

- **Arguments**

::

    usage: annogesic crispr [-h] [--CRT_path CRT_PATH] [--gff_path GFF_PATH]
                            [--fasta_path FASTA_PATH] [--window_size WINDOW_SIZE]
                            [--min_number_repeat MIN_NUMBER_REPEAT]
                            [--min_length_repeat MIN_LENGTH_REPEAT]
                            [--Max_length_repeat MAX_LENGTH_REPEAT]
                            [--min_length_spacer MIN_LENGTH_SPACER]
                            [--Max_length_spacer MAX_LENGTH_SPACER]
                            [--ignore_hypothetical_protein]
                            [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --CRT_path CRT_PATH   If you want to assign the path of CRT.jar, please
                            assign here. Default is /usr/local/bin/CRT.jar
      --gff_path GFF_PATH, -g GFF_PATH
                            If genome gff file is provided, this function will
                            compare CRISPRs with the genome annotation for
                            removing the false positives. Default is None.
      --fasta_path FASTA_PATH, -f FASTA_PATH
                            Path of the genome fasta folder.
      --window_size WINDOW_SIZE, -w WINDOW_SIZE
                            Length of the window size for searching CRISPR (range:
                            6-9). Default is 8.
      --min_number_repeat MIN_NUMBER_REPEAT, -mn MIN_NUMBER_REPEAT
                            Minimum number of repeats that a CRISPR must contain.
                            Default is 3.
      --min_length_repeat MIN_LENGTH_REPEAT, -ml MIN_LENGTH_REPEAT
                            Minimum length of CRISPR repeats. Default is 23.
      --Max_length_repeat MAX_LENGTH_REPEAT, -Ml MAX_LENGTH_REPEAT
                            Maximum length of CRISPR repeats. Default is 47.
      --min_length_spacer MIN_LENGTH_SPACER, -ms MIN_LENGTH_SPACER
                            Minimum length of CRISPR spacers. Default is 26.
      --Max_length_spacer MAX_LENGTH_SPACER, -Ms MAX_LENGTH_SPACER
                            Maximum length of CRISPR spacers. Default is 50.
      --ignore_hypothetical_protein, -in
                            This function is for ignoring the hypothetical
                            proteins. Default is False.

- **Output files**

Output files are stored in ``$ANNOgesic/output/crispr``. The folders which are generated by the subcommand are following:

**CRT_output:** Stores the output of `CRT <http://www.room220.com/crt/>`_.

**gffs:** Stores CRSIPR gff files. Two sub-folders are under this folder:

	**all_candidates:** Stores gff file which contains all CRISPRs.
 
	**best:** Stores gff file which contains the CRISPRs without overlapping genome annotation.

**statistics:** Stores statistic files.

optimize_tsspredator
---------------

``optimize_tsspredator`` can adapt the parameter set of `TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_. 
For running it, please manual detect TSSs around 200kb and find at least 50 TSSs (using gff format).
If there are less than 50 TSSs within 200kb, please continue checking until 50 TSSs are detected.
Then ``optimize_tsspredator`` can scan whole genome based on the principle of the manual detection to get optimized parameters.

- **Required tools**

`TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_.

- **Required files**

**Wiggle files of TEX +/-:** Please check the section ``The format of libraries for import to ANNOgesic``.

**Fasta file of the genome sequence**

**Gff file of the genome annotation**

**Gff file of the manual detection**

- **Arguments**

::

    usage: annogesic optimize_tsspredator [-h]
                                          [--TSSpredator_path TSSPREDATOR_PATH]
                                          [--fasta_file FASTA_FILE]
                                          [--annotation_file ANNOTATION_FILE]
                                          [--wig_folder WIG_FOLDER]
                                          [--manual MANUAL]
                                          [--strain_name STRAIN_NAME]
                                          [--max_height MAX_HEIGHT]
                                          [--max_height_reduction MAX_HEIGHT_REDUCTION]
                                          [--max_factor MAX_FACTOR]
                                          [--max_factor_reduction MAX_FACTOR_REDUCTION]
                                          [--max_base_height MAX_BASE_HEIGHT]
                                          [--max_enrichment_factor MAX_ENRICHMENT_FACTOR]
                                          [--max_processing_factor MAX_PROCESSING_FACTOR]
                                          [--utr_length UTR_LENGTH] [--lib LIB]
                                          [--output_prefix OUTPUT_PREFIX]
                                          [--cluster CLUSTER]
                                          [--partial_length PARTIAL_LENGTH]
                                          [--core CORE] [--program PROGRAM]
                                          [--replicate_match REPLICATE_MATCH]
                                          [--steps STEPS]
                                          [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --TSSpredator_path TSSPREDATOR_PATH
                            If you want to assign the path of TSSpredator, please
                            assign here. Default is /usr/local/bin/TSSpredator.jar
      --fasta_file FASTA_FILE, -fs FASTA_FILE
                            Path of one target genome fasta file.
      --annotation_file ANNOTATION_FILE, -g ANNOTATION_FILE
                            Path of one target genome annotation gff file.
      --wig_folder WIG_FOLDER, -w WIG_FOLDER
                            Path of the TEX+/- wig folder.
      --manual MANUAL, -m MANUAL
                            Path of the manual-checked gff file.
      --strain_name STRAIN_NAME, -n STRAIN_NAME
                            The strain name for optimization.
      --max_height MAX_HEIGHT, -he MAX_HEIGHT
                            This value relates to the minimum number of read
                            starts at a certain genomic position to be considered
                            as a TSS candidate. During optimization, --max_height
                            will be never larger than this value. Default is 2.5.
      --max_height_reduction MAX_HEIGHT_REDUCTION, -rh MAX_HEIGHT_REDUCTION
                            When comparing different strains/conditions and the
                            step height threshold is reached in at least one
                            strain/condition, the threshold is reduced for the
                            other strains/conditions by the value set here. This
                            value must be smaller than the step height threshold.
                            During optimization, --max_height_reduction will be
                            never larger than this value. Default is 2.4.
      --max_factor MAX_FACTOR, -fa MAX_FACTOR
                            This is the minimum factor by which the TSS height has
                            to exceed the local expression background. During
                            optimization, --max_factor will be never larger than
                            this value. Default is 10.
      --max_factor_reduction MAX_FACTOR_REDUCTION, -rf MAX_FACTOR_REDUCTION
                            When comparing different strains/conditions and the
                            step factor threshold is reached in at least one
                            strain/condition, the threshold is reduced for the
                            other strains/conditions by the value set here. This
                            value must be smaller than the step factor threshold.
                            During optimization, --max_factor_reduction will be
                            never larger than this value. Default is 9.9.
      --max_base_height MAX_BASE_HEIGHT, -bh MAX_BASE_HEIGHT
                            This is the minimum number of reads should be mapped
                            on TSS. During optimization, --max_base_height will be
                            never larger than this value. Default is 0.06.
      --max_enrichment_factor MAX_ENRICHMENT_FACTOR, -ef MAX_ENRICHMENT_FACTOR
                            This is the minimum enrichment factor. During
                            optimization, --max_enrichment_factor will be never
                            larger than this value. Default is 6.0.
      --max_processing_factor MAX_PROCESSING_FACTOR, -pf MAX_PROCESSING_FACTOR
                            This is the minimum processing factor. If untreated
                            library is higher than the treated library and above
                            which the TSS candidate is considered as a processing
                            site and not annotated as detected. During
                            optimization, --max_processing_factor will be never
                            larger than this value. Default is 6.0
      --utr_length UTR_LENGTH, -u UTR_LENGTH
                            The length of UTR. It is for Primary and Secondary
                            TSSs. Default is 300.
      --lib LIB, -l LIB     TEX+/- wig files for TSSpredator. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --output_prefix OUTPUT_PREFIX, -p OUTPUT_PREFIX
                            The output prefixs of all conditions. If multiple
                            conditions need to be assigned, please use comma to
                            separate them. For example,
                            prefix_condition1,prefix_condition2.
      --cluster CLUSTER, -cu CLUSTER
                            This value defines the maximal distance (nucleotides)
                            between TSS candidates have to be clustered together.
                            If the distance between these multiple TSSs is smaller
                            or equal to this value, only one of them will be
                            printed out. Default is 2.
      --partial_length PARTIAL_LENGTH, -le PARTIAL_LENGTH
                            The genome length for comparing between predicted TSSs
                            and manual checked TSSs. Please assign the genome
                            length of your manual detected gff file. If you want
                            to compare whole genome, please don't use this
                            function (Default). The default is comparing whole
                            genome.
      --core CORE, -c CORE  Paralle runs for optimization. Default is 4.
      --program PROGRAM, -t PROGRAM
                            The feature for optimization. Please assign "TSS" or
                            "Processint_site". Default is TSS.
      --replicate_match REPLICATE_MATCH, -rm REPLICATE_MATCH
                            This value is the minimal number of replicates that a
                            TSS has to be detected. The format is
                            $NUMBERofCONDITION_$NUMBERofREPLICATED. If different
                            --replicate_match values need to be assigned to
                            different conditions, please use comma to separate
                            them. For example, 1_2,2_2,3_3. It means that
                            --replicate_match is 2 in number 1 and number 2
                            conditions. In number 3 condition, --replcate_match is
                            3. For assigning the same --replicate_match to all
                            conditions, just use like all_1 (--replicate_match is
                            1 in all conditions). Default is all_1.
      --steps STEPS, -s STEPS
                            The total runs for optimization. Default is 4000 runs.

- **Output files**

Based on the programs (TSS/processing site), Output files are stored in 
``$ANNOgesic/output/TSS/optimized_TSSpredator`` or ``$ANNOgesic/output/processing_site/optimized_TSSpredator``. 
Two output files are following:

**stat.csv:** Stores the information of every run. The first column is the number of run.
The second column is the parameter set. ``he`` represents height; ``rh`` represents 
height reduction; ``fa`` means factor; ``rf`` means factor reduction; ``bh`` indicates 
base height; ``ef`` indicates enrichment factor; ``pf`` means processing factor. About the details 
of parameters, please refer to `TSSpredator <http://it.inf.uni-tuebingen.de/?page_id=190>`_.
For example, ``he_2.0_rh_1.8_fa_4.4_rf_2.8_bh_0.08_ef_3.0_pf_2.6`` means that height is 2.0, 
height reduction is 1.8, factor is 4.4, factor reduction is 2.8, base height is 0.08, 
enrichment factor is 3.0 and processing factor is 2.6. The third and fourth columns are 
the number of true positives. The fifth and sixth columns are true positive rate. The seventh 
and eighth columns are the number of false positives. The ninth and tenth false positive rate. 
The eleventh and twelfth columns are the number of false negatives. The thirteenth and fourteenth 
columns are missing rate.

**best.csv:** Stores the best parameter set. The meanings of all columns are the same as ``stat.csv``.

screenshot
-----------

``screenshot`` can generate batch files for producing screenshot of `IGV <https://www.broadinstitute.org/igv>`_. 
Generating screenshots can reduce the time for checking the results in genome browser.
When the batch files is produced, the user just needs to open `IGV <https://www.broadinstitute.org/igv>`_, then presses ``tools`` 
on the top tags and choose ``run batch script``. The program will automatically produce screenshots. 

- **Required tools**

`IGV <https://www.broadinstitute.org/igv>`_.

- **Required files**

**Gff files that the user want to produce screenshots:** All screenshots will be produced based on the positions of ``--main_gff``. 
If comparing ``--main_gff`` with other features is required, please assign gff files of other features to ``--side_gff``. 

**Fasta files of the genome**

**Wiggle files of TEX+/- or fragmented libraries:** Please check the section ``The format of libraries for import to ANNOgesic``.

- **Arguments**

::

    usage: annogesic screenshot [-h] [--main_gff MAIN_GFF] [--side_gffs SIDE_GFFS]
                                [--fasta FASTA]
                                [--frag_wig_folder FRAG_WIG_FOLDER]
                                [--tex_wig_folder TEX_WIG_FOLDER]
                                [--height HEIGHT] [--tex_libs TEX_LIBS]
                                [--frag_libs FRAG_LIBS] [--present PRESENT]
                                [--output_folder OUTPUT_FOLDER]
                                [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --main_gff MAIN_GFF, -mg MAIN_GFF
                            Screenshot will be generated based on the position of
                            --main_gff file.
      --side_gffs SIDE_GFFS, -sg SIDE_GFFS
                            If you want to compare other features with the
                            --main_gff, please assign the path of other gff files
                            here. If multiple gff files need to be assigned, just
                            use comma to separate them.
      --fasta FASTA, -f FASTA
                            Path of the genome fasta folder.
      --frag_wig_folder FRAG_WIG_FOLDER, -fw FRAG_WIG_FOLDER
                            If the information of fragmented wig file is required,
                            please assign the fragmented wig folder.
      --tex_wig_folder TEX_WIG_FOLDER, -tw TEX_WIG_FOLDER
                            If the information of TEX+/- wig file is required,
                            please assign the TEX+/- wig folder.
      --height HEIGHT, -he HEIGHT
                            Please assign the height of the screenshot. Default is
                            1500.
      --tex_libs TEX_LIBS, -tl TEX_LIBS
                            If TEX+/- wig file is required, please also assign the
                            proper format here. The format is:
                            wig_file_name:TEX+/-(tex or notex):condition_id(intege
                            r):replicate_id(alphabet):strand(+ or -). If multiple
                            wig files need to be assigned, please use comma to
                            separate the wig files. For example,
                            wig1:tex:1:a:+,wig2:tex:1:a:-.
      --frag_libs FRAG_LIBS, -fl FRAG_LIBS
                            If the fragmented wig file is required, please also
                            assign the proper format here. The format is: wig_file
                            _name:fragmented(frag):condition_id(integer):replicate
                            _id(alphabet):strand(+ or -). If multiple wig files
                            need to be assigned, please use comma to separate the
                            wig files. For example,
                            wig1:frag:1:a:+,wig2:frag:1:a:-.
      --present PRESENT, -p PRESENT
                            The presentation type of the features in the
                            screenshot. expand/collapse/squish. Default is expand.
      --output_folder OUTPUT_FOLDER, -o OUTPUT_FOLDER
                            Please assign the output folder. It will create a sub-
                            folder "screenshots" in --output_folder to store the
                            results.

- **Output files**

Based on the paths of ``main_gff``, ``screenshot`` will generate a folder - ``screenshots`` under the 
folder of ``main_gff``. Output files will stored in this folder. Names of the output file and folder are 
as following:

**forward.txt:** Batch file of the forward strand for running on IGV.

**reverse.txt:** Batch file of reverse strand for running on IGV.

**forward:** Folder for storing screenshots of the forward strand.

**reverse:** Folder for storing screenshots of the reverse strand.

When batch files are executed on IGV, the screenshots will be automatically stored in ``forward`` and ``reverse``. 
Format of the filenames will be ``$STRAIN:$START-$END.png``. For example, ``NC_007795:1051529-1051696.png`` 
means the strain is NC_007795, the feature's start point is 1051529 and end point is 
1051696.

color_png
----------

``color_png`` is a following procedure of ``screenshot``. If numerous samples are included in one figure, 
Tracks will be difficult to checked. ``color_png`` can color the tracks based on TEX +/- libraries 
for improving the checking process.

- **Required tools**

`ImageMagick <http://www.imagemagick.org/script/index.php>`_.

- **Required files**

**The screenshots:** Please make sure the folders of ``forward`` and ``reverse`` 
exist in the folder of ``screenshots``.

- **Arguments**

::

    usage: annogesic color_png [-h] [--screenshot_folder SCREENSHOT_FOLDER]
                               [--track_number TRACK_NUMBER]
                               [--ImageMagick_covert_path IMAGEMAGICK_COVERT_PATH]
                               [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --screenshot_folder SCREENSHOT_FOLDER, -f SCREENSHOT_FOLDER
                            The folder which stores "screenshots" (a folder
                            generated by subcommand "screenshot").
      --track_number TRACK_NUMBER, -t TRACK_NUMBER
                            The number of tracks.
      --ImageMagick_covert_path IMAGEMAGICK_COVERT_PATH, -m IMAGEMAGICK_COVERT_PATH
                            Please assign the path of "convert" in ImageMagick
                            package.

- **Output files**

The new screenshots will replace the previous ones automatically.

merge_features
--------------
If storing multiple features of the annotation to one gff file is needed, ``merge_features`` can achieve this purpose. 
``merge_features`` can merge all features that the user assigned to one gff file, and search the parent transcript to each feature.

- **Arguments**

::

    usage: annogesic merge_features [-h] [--transcript_path TRANSCRIPT_PATH]
                                    [--other_features_path OTHER_FEATURES_PATH]
                                    [--fuzzy_term FUZZY_TERM]
                                    [--fuzzy_TSS FUZZY_TSS]
                                    [--strain_name STRAIN_NAME]
                                    [project_path]
    
    positional arguments:
      project_path          Path of the project folder. If none is given, the
                            current directory is used.
    
    optional arguments:
      -h, --help            show this help message and exit
      --transcript_path TRANSCRIPT_PATH, -a TRANSCRIPT_PATH
                            If transcript gff file is provided. The parent
                            transcripts ("Parent" in gff attributes) of all
                            features will be generated. If there is no transcript,
                            this function will just simply combine all input gff
                            files.
      --other_features_path OTHER_FEATURES_PATH, -of OTHER_FEATURES_PATH
                            Please assign the gff files (besides transcript gff
                            file) which you want to merge.
      --fuzzy_term FUZZY_TERM, -fm FUZZY_TERM
                            For merging terminators, please assign the fuzzy
                            nucleotides between transcript and terminator.
                            ATTENTION, the third column of gff file of terminator
                            should be exact "terminator". Default is 30.
      --fuzzy_TSS FUZZY_TSS, -ft FUZZY_TSS
                            For merging TSSs, please assign the fuzzy between TSS
                            and transcript. ATTENTION, the third column of gff
                            file of terminator should be exact "TSS". Default is
                            5.
      --strain_name STRAIN_NAME, -s STRAIN_NAME
                            Please assign the strain name of the input files. It
                            will become the prefix name of output gff file.

- **Output files**

Output gff file are stored in ``$ANNOgesic/output/merge_all_features``. The tag - ``Parent`` in the attributes of 
gff file shows the parent transcript.
