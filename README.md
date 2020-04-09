# High-throughput _Bacillus thuringiensis_ toxin mining pipeline

![Platform](https://badgen.net/badge/platform/WSL,Linux,macOS,Docker?list=|)
![License](https://badgen.net/github/license/liaochenlanruo/BTCMP)
[![GitHubversion](https://badge.fury.io/gh/liaochenlanruo%2FBTCMP.svg)](https://badge.fury.io/gh/liaochenlanruo%2FBTCMP)
![Downloads conda](https://img.shields.io/conda/dn/bioconda/BTCMP.svg?style=flat)
[![install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg?style=flat)](http://bioconda.github.io/recipes/BTCMP/README.html)

## Contents

- [Introduction](#introduction)

- [Installation](#installation)

- [Usage](#usage)

- [Examples](#examples)

- [Outputs](#outputs)

- [License](#license)

- [Feedback](#feedback)

- [Citation](#citation)

- [FAQs](#faqs)

- [Updates](#updates)

## Introduction

This is a high-throughput, automatic gene mining tool that can mine toxin genes, such as Cry, Cyt and Vip, etc, from _Bacillus thuringiensis_. The pipeline accepts multiple forms of input data including Reads, assembled genomes, ORFs, and protein sequences and can output rich and useful results.

## Installation

- Required dependencies
  - [BioPerl](http://metacpan.org/pod/BioPerl)
  - [HMMER](https://www.ebi.ac.uk/Tools/hmmer/)
  - [libsvm](https://github.com/cjlin1/libsvm)
  - [NCBI-blast+](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=Download)
  - [Perl](http://www.perl.org/get.html)
  - [PGCGAP](https://liaochenlanruo.hzaubmb.org/pgcgap/)

- Install with Bioconda - OSX/Linux/WSL
```
conda create -n btcmp python=3
conda activate btcmp
conda install btcmp
```

## Usage
```
btcmp [Options]
```

Options:

    [--help]                      Print the help message and exit

    [--version]                   Show version number of BTCMP and exit

    [--threads (INT)]             Number of threads to be used ( Default 4 )

    [--SeqPath (PATH)]            [Required] The path of input sequences ( Default "the current directory" )

    [--SequenceType (STRING)]     [Required] Sequence type for inputs. "reads", "nucl", "orfs", and "prot" avaliable ( Default nucl )

    [--platform (STRING)]         [Required] Sequencing Platform, "illumina", "pacbio", "oxford" and "hybrid" available ( Default illumina )

    [--assemble_only (STRING)]    Only perform genome assembly without predicting toxins.

    [--reads1 (STRING)]           [Required by "reads"] The suffix name of reads 1 ( for example: if the name of reads 1 is "YBT-1520_L1_I050.R1.clean.fastq.gz", "YBT-1520" is the strain same, so the suffix name should be ".R1.clean.fastq.gz" )

    [--reads2 (STRING)]           [Required by "reads"] The suffix name of reads 2( not required by "oxford" and "pacbio". For example: if the name of reads 2 is "YBT-1520_2.fq", the suffix name should be _2.fq" )

    [--suffix_len (INT)]          [Required by "reads"] (Strongly recommended) The suffix length of the reads file, that is the length of the reads name minus the length of the strain name. For example the --suffix_len of "YBT-1520_L1_I050.R1.clean.fastq.gz" is 26 ( "YBT-1520" is the strain name ) ( Default 0 )

    [--short1 (STRING)]           [Required] FASTQ file of first short reads in each pair. Needed by hybrid assembly ( Default Unset )

    [--short2 (STRING)]           [Required] FASTQ file of second short reads in each pair. Needed by hybrid assembly ( Default Unset )

    [--long (STRING)]             [Required] FASTQ or FASTA file of long reads. Needed by hybrid assembly ( Default Unset )

    [--hout (STRING)]             [Required] Output directory for hybrid assembly ( Default "../../Results/Assembles/Hybrid" )

    [--genomeSize (STRING)]       [Required] An estimate of the size of the genome. Common suffixes are allowed, for example, 3.7m or 2.8g. Needed by PacBio data and Oxford data ( Default 6.07m )

    [--Scaf_suffix (STRING)]      The suffix of scaffolds or genomes ( Default ".filtered.fas" )

    [--orfs_suffix (STRING)]      The suffix of orfs files ( Default ".ffn" )

    [--prot_suffix (STRING)]      The suffix of protein files ( Default ".faa" )

## Examples

- Processing Illumina paired-end Reads
```
btcmp --SeqPath <Illumina Reads PATH> --SequenceType reads --platform illumina --reads1 <suffix name of reads 1> -reads2 <suffix name of reads 2> --threads <INT> --suffix_len <INT>
```

- Processing PacBio long Reads
```
btcmp --SeqPath <PacBio Reads PATH> --SequenceType reads --platform pacbio --reads1 <suffix name of PacBio reads> --threads <INT> --suffix_len <INT>
```

- Processing Oxford long Reads
```
btcmp --SeqPath <Oxford Reads PATH> --SequenceType reads --platform oxford --reads1 <suffix name of Oxford reads> --threads <INT> --suffix_len <INT>
```

- Processing Hybrid Reads (Long reads + illumina short reads)
```
btcmp --SeqPath <Reads PATH> --SequenceType reads --platform hybrid --short1 <short reads 1> --short2 <short reads 2> --long <long reads> --threads <INT>
```

- Processing assembled genomes
```
btcmp --SeqPath <Assembled genome PATH> --SequenceType nucl --Scaf_suffix <suffix of genomes> --threads <INT>
```

- Processing protein sequences
```
btcmp --SeqPath <Protein file PATH> --SequenceType prot --prot_suffix <suffix of protein files> --threads <INT>
```

- Processing orfs sequences
```
btcmp --SeqPath <orfs file PATH> --SequenceType orfs --orfs_suffix <suffix of orfs files> --threads <INT>
```

## Outputs

- __Results/Toxins/*.list:__ Toxin list of each strain;
- __Results/Toxins/*.gbk:__ Toxin sequences in Genbank format of each strain;
- __Results/Toxins/Bt_all_genes.table:__ A matrix describes Strains vs. Toxins;
- __Results/Toxins/All_Toxins.txt:__ A table containing all information and sequences of all toxin genes. See table 1 for details.

__Table 1: Description of \'All_Toxins.txt\'__

|Header|Description|
|:-----|:----------|
|Strain|The name of your input|
|Protein_id|The protein ID|
|Protein_len|The length of protein sequence|
|Strand|Positive or negative strand where the gene comes from|
|Gene location on scaffold|Gene coordinates on the genome|
|SVM|Is the protein predicted by SVM|
|BLAST|Is the protein predicted by BLAST|
|HMM|Is the protein predicted by HMM|
|Hit_id|The subject sequence ID|
|Hit_length|The length of subject sequence|
|Aln_length|alignment length (sequence overlap)|
|Query start-end|Start and end of alignment in query|
|Hit stard-end|Start and end of alignment in subject|
|Identity|Percentage of identical matches|
|Evalue of blast|Expect value of BLAST|
|Hmm hit|The subject model ID|
|Hmm hit length|The length of subject model sequence|
|Evalue of Hmm|Expect value of HMM|
|Endotoxin_N|Whether the Cry protein contain Endotoxin_N domain|
|Endotoxin_M|Whether the Cry protein contain Endotoxin_M domain|
|Endotoxin_C|Whether the Cry protein contain Endotoxin_C domain|
|Endotoxin_mid|Whether the Cry protein contain Endotoxin_mid domain|
|Toxin_10|Whether the Cry protein contain Toxin_10 domain|
|ETX_MTX2|Whether the Cry protein contain ETX_MTX2 domain|
|Gene sequence|The nucleotide sequence of the toxin|
|Protein sequence|Amino acid sequence of the toxin|
|Scaffold sequence|The scaffold sequence where the toxin gene is located|

## License

BTCMP is free software, licensed under [GPLv3](https://github.com/liaochenlanruo/BTCMP/blob/master/LICENSE).

## Feedback/Issues

Please report any issues about usage of the software to the [issues page](https://github.com/liaochenlanruo/BTCMP/issues).

## Citation

If you use this software please cite:

## FAQs

## Updates

- v1.0.2
  - Waiting...
