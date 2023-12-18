
Dependencies
============

Python3   >= 3.7    (earlier may work)
NumPy     >= 1.16.2 (earlier may work)
Pandas    >= 0.24.2 (earlier may work)
BioPython >= 1.80   (earlier will not be able to merge/align data)
PyYAML    >= 5.1    (earlier may work)


PRISM data and pipeline repository 
==================================

This folder contains directories of curated or generated data for the PRISM project and scripts for processing.

There is an python module, scripts/PrismData.py, with functions to parse, merge and write data files.

PrismData.py also has a main that implements some tasks:

# Gererate an overview of all MAVE data sets in spreadsheet format
>>> ./scripts/PrismData.py --dump_header_csv prism_mave_overview.csv  prism_mave/prism_mave_*.txt

# Generate a fasta file of all MAVE data, e.g. for BLAST'ing
>>> ./scripts/PrismData.py --dump_fasta prism_mave_overview.fasta  prism_mave/prism_mave_*.txt

# Add all single mutants in defined order and chenge the residue numbering to start at 5
>>> ./scripts/PrismData.py --parse --saturate --first_res_num 5 -v  prism_mave/prism_mave_010_UBE2I_growth_abundance.txt

# Merge 2 data sets and remove lines with NA only and give lots of output
>>> ./scripts/PrismData.py --merge pten.txt --clean_na -vv   prism_mave/prism_mave_002_PTEN_phosphatase_activity.txt \
                                                             prism_mave/prism_mave_003_PTEN_abundance.txt

# Merge all GAL4 data
>>> ./scripts/PrismData.py --merge prism_mave_GAL4_all_conditions.txt  prism_mave/prism_mave_*GAL4*.txt

# See all options
>>> ./scripts/PrismData.py -h

General data file format
========================

Filen names are 'prism_method_xxx_name.txt' where 'method' is the data generating method, xxx is a entity number and 'name'
is some short and describing title typically related to the protein. For a given method, the number should be unique so keep
enough digits for this, e.g. computational methods to use proteome-wide should have 5 digits.

Files consists of meta-data lines starting with a hash (#), followed by a number of white-space separated columns containing
the data. The format is designed to contain data of one protein only. The structure is the following:

'''
# --------------------
#  Header
# --------------------
#
# Other comments
#
variant  number  number ...
1MA         1.0     1.0
...
'''

Any number of columns containing scores, uncertainties, etc. may be given with missing data notated as NA. Avoid rows only
containing NA and keep the first score column as close to the original scores as possible.

Header
------

The header is in machine readable YAML format and contains information on the protein and the experiment. Most important
field here is the amino acid sequence of the target protein. The 'version', 'protein' and 'columns' fields are madatory and
common for all prism data files. All fields are described below.

Other comments may be included outside the header but should be concise and of general relevance.

General header fields
---------------------

**version** [number] : version of the data set used to check if the data has changed

**Protein** sub fields give information on the target protein of the MAVE experiment

- name [text] : Typically the gene name or a common name of the protein, possible followed by the domain considered. Need
                not match the file names and is mostly ment for human readability.
- sequence [word] : The actual sequence that is measured. All amino acid given in the data section should match this.
                    Unknown residues may be marked with 'X'. 
- first_residue_number [int] : Residue number of the first amino acid in the sequence. Must be non-negative.
                               Optional, default 1
- organism [text] : The source of the target protein. Mostly ment for human readability.
- uniprot [word] : Uniprot entity, amino acid sequence may not match 100% but should be close. Organism and function should
                   match.

**Column** fields describe the measured quantities given in the file. The first column (variant, residue or similar) is not
described in the header. Other columns are named and described here and must appear in order of appearence in the data section.
A column name must be a single word without white space since the columns are white-spece separated.

In addition to the version, protein and column fields, a header can contain a field describing the data, e.g. statistics on
variants, and a field on the data generating method e.g. the software, technology and/or protocols used as well as information
on the environment of the protein in the experiment. These fields are described below.

Variant data files
==================

Variant notation
----------------

Notation is: original amino acid, residue number and variant amino acid, e.g. F2Y. Use the original amino acid numbering from
the source of the experiment, e.g. article or PDB file. Nonsense substitutions (stop codons) are notated with *, synonymous
substitutions with = and multi-mutants are separated by colons, e.g. A3V:K6=:V10*. If the reference sequence is measured
this variant is notated 'WT'. A variant can only be listed once.

The aim is to have a simple notation that is close to what many groups already use and with a 1:1 translation to the HGVS
format.

Indels
------

Single amino acid deletions may be denoted with a ~, e.g. D83~. Insertions may be denoted in a similar way with the number
giving the new position, e.g. ~83D if an Asp is inserted between the original position 82 and 83. All downstream substitutions
should still refer to the original numbering.

Variant header fields
---------------------

Variant fields describe the amount and the nature of the maesured variants. The variant header section is **optional**.

- number [intger] : Number of measured variants given in the file including WT (if given) and not including given variants
                    with all data missing (NA).
- coverage [float] : Fraction of mutated positions in the target protein sequence (given in the header)
- depth [float] : Average number of variants per mutated position. For multimutants, its the average number of variants
                  that involves each mutated position. 
- width [keyphrase] : Current options are: single mutants, single and double mutants, multi mutants

MAVE data
=========

All prism_mave_xxxxx_name.txt files should have an 'mave' meta-data section in the header that describes the assay method
used in the MAVE experiment.

Mave header fields:

- organism [text] : The assy organism may be different from the source organism of the target protein
- cloning [keyphrase] : Current options are: plasmid, plasmid CEN, plasmid 2u, chromosomal, landing pad
- expression [keyphrase] : Current options are: endogenous, overexpression, single copy, unknown
- technology [keyphrase] : Current options are: growth, VAMPseq, FACS, survival
- doi [word] : DOI of the original text that describes the experiment
- year [integer] : Publication year of the text above

Rosetta data
============

All prism_rosetta_xxxxx_name.txt files should have an 'rosetta' meta-data section in the header that describes the method
used in the calculation.

Rosetta header fields:

- build [word] : The build name of the software, e.g. 2018 or 2020
- protocol [word] : Name of the computational protocol used, e.g. ddG_02
- pdb [word] : PDB entry and chain id, e.g. 1gb1a 1gb1b
- env_pdb : 1gb1c
- pdb_pproc_protocol : clean_01
