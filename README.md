# tfb-miner [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
A data acquisition and analysis pipeline for the rapid identification of putative transcription factor-based biosensors.
## Description
This pipeline has two main stages: 1) enzymatic chain identification, and 2) chain processing.

### Enzymatic chain identification
The pipeline initially receives the KEGG COMPOUND database ID of a compound of interest (denoted C1) and uses the KEGG REST API to retrieve data regarding which reactions C1 is involved in. Subsequently, reactions that catabolize the compound are identified, and the IDs of each product (C2) are used to identify reactions that catabolize C2. Enzymes that catalyse the initial reactions are linked to enzymes that catalyse the subsequent reactions to form chains that sequentially processes C1. This process continues until chains reach the maximum chain length, which is set by the user. Each chain will be output to the console, keeping the user updated during this stage.

### Chain processing
Each enzymatic chain is then processed to identify putative transcriptional regulators of C1 degradation.  This begins by determining whether any genes that encode enzymes within the chain have genetic organisations that are characteristic of a catabolic operon. The KEGG REST API is used to retrieve lists of genes that encode each enzyme within the chain and the organisms that possess them, and this data is filtered to leave only organisms that possess all of the enzymes. 
For each organism, the program uses a local database file (named genome_assemblies) in the program directory to identify the GenBank accession code of its genome, and then searches within a subdirectory (named genome_files) for a feature table genome that contains this accession code in its filename. The genome is then read, and the data is used to predict operons that facilitate C1 degradation and their putative transcriptional regulators based upon a conceptual model of their relative organisational characteristics within genomes. Each prediction is then scored based upon how well its organisation fits the model. 
Data for each chain is then output to a csv file within a directory named chainlength=x, where x is the length of the chain, and each of these directories will be within a parent directory named C1_results. Predictions will be ranked in order of their scores, with a score of 0 being the highest achievable score. A progress bar in the console will keep the user updated on the progress of this stage.

## Usage
```sh
TFBMiner.py [-h] [compound] [-l length]
```

## Options
```-h, --help``` 

Display the program usage, description, options, and guidance in the terminal.

```compound```

Enter the KEGG COMPOUND database ID of the compound to perform computations for.

```-l, --length```

Specify the maximum length of the enzymatic chains. This value can currently range between 2 and 4.

## Examples

```sh 
TFBMiner.py C00259 -l 3
```

Performs computations for l-arabinose. Chains of length 2 and 3 will be identified and processed.

```sh
TFBMiner.py C01494 -l 4
```

Performs computations for ferulic acid. Chains of length 2, 3, and 4 will be identified and processed.

## Author
Tariq Joosab.