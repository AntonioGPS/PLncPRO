[![Build Status](https://travis-ci.org/urmi-21/PLncPRO.svg?branch=master)](https://travis-ci.org/urmi-21/PLncPRO)


                          _____  _            _____  _____   ____  
                         |  __ \| |          |  __ \|  __ \ / __ \ 
                         | |__) | |_ __   ___| |__) | |__) | |  | |
                         |  ___/| | '_ \ / __|  ___/|  _  /| |  | |
                         | |    | | | | | (__| |    | | \ \| |__| |
                         |_|    |_|_| |_|\___|_|    |_|  \_\\____/ 
                                           

#  INTRODUCTION

PlncPRO (Plant Long Non-Coding rna Prediction by Random fOrests) is a program to classify coding (mRNAs) and long non-coding transcripts (lncRNAs).
Our method is based on random forest method and uses protein homology search, sequence based and 3-mer frequency based features. 
We have developed predictive models for several plant species to predict lncRNAs. 
We comprehensively tested our method on plants and vertebrates and found that our model works better as compared to the existing tools.

## Citation
Singh et. al. PLncPRO for prediction of long non-coding RNAs (lncRNAs) in plants and its application for discovery of abiotic stress-responsive lncRNAs in rice and chickpea. Nucleic Acids Res. 2017 Dec 15;45(22):e183. doi: 10.1093/nar/gkx866.


#  INSTALLATION

## Pre-requisite: 
1. OS: Linux, macOS
2. Python 3.5 or later versions (http://www.python.org/)
3. NCBI BLAST (https://blast.ncbi.nlm.nih.gov/Blast.cgi)
4. GNU C Library (glibc >= 2.14)

## python dependencies
1. NumPy (http://www.numpy.org/) 
2. SciPy (https://www.scipy.org/)
3. Scikit-learn (http://scikit-learn.org/) 
4. Biopython (http://biopython.org/)
5. regex

## Using PIP
```
pip install plncpro
```

## From source

```
git clone https://github.com/urmi-21/PLncPRO.git
pip install PlncPro
```

## Run tests

```
bash tests/local_test.sh
```

# Basic Usage

See [examples]() for detailed usage examples.

### `plncpro predict` 

Label lncRNAs and mRNAs. This file reads an input
file containing sequences and then classifies the sequences as coding or
non-coding. It uses a model generated by build.py to make classifications.
It outputs a file containing class label and class probabilities for each
sequence.
 
```
plncpro predict -i <input fasta> -o <output_dir> -p <output_file_name> -t 2 -d <blast_db> -m <model_file>
```

### PARAMETERS

```	
-p,--prediction_out	output file name
-i,--infile		file containing input sequences
-m,--model		model file
-o,--outdir		output directory name
-d,--db			path to blast database
		OPTIONAL
-t,--threads		number of threads [default: 4]
-l,--labels		path to the files containg labels(it outputs classification accuracy)
-r,--remove_temp	clean up intermediate files
-v,--verbose		show more messages
--min_len		specifiy min_length to filter input files
--noblast		Don't use blast features
-no_ff			Don't use framefinder features
--qcov_hsp		specify query coverage parameter for blast[default:30]
--blastres*		path to blast output for input file
*blast result should be in following format: -outfmt '6 qseqid sseqid pident evalue qcovs qcovhsp score bitscore qframe sframe'
```

     


### `plncpro build` 
Build model using the given training data
(mRNA/lncRNA transcripts). This file reads two labelled datasets
containing coding and non-coding transcripts. Then it makes a random
forest based classification model and saves the model, which can be used
to predict unknown sequences.

 
```
plncpro build -p <mrna fasta> -n <lncrna fasta> -o <out_dir> -m <model_name> -d <blast db> -t <threads>
```
		
### PARAMETERS   
```
-p,--pos		file containing mRNA sequences
-n,--neg		file containing lncRNA sequences
-m,--model		output model name
-o,--outdir		output directory name
-d			path to blast database
		OPTIONAL
-t,--threads		number of threads [default: 4]
-k,--num_trees		number of trees[default: 1000]
-r,--remove_temp	clean up intermediate files
-v,--verbose		show more messages	
--min_len		specifiy min_length to filter input files
--noblast		Don't use blast features
--no_ff			Don't use framefinder features
--qcov_hsp		specify query cov parameter for blast[default:30]
--pos_blastres*		path to blast result for mRNA input file
--neg_blastres*		path to blast result for lncRNA input file

*blast result should be in following format: -outfmt '6 qseqid sseqid pident evalue qcovs qcovhsp score bitscore qframe sframe' 
```   




### plncpro predtoseq
Extract mRNA or lncRNA sequences from
PLNCPRO output file. This file reads a prediction output file and
extracts sequences from a given class. User can specify class and
probability cut-off and extract desired transcript sequences.


```
plncpro predtoseq -f <fasta_file> -o <outputfile> -p <PLNCPRO_prediction_file> -l <required_label>
```
  		
### PARAMETERS
```
-f			input fasta file name
-o			output fasta file name	
-p			path to file containg predictions by PLNCPRO
		OPTIONAL
-l			label of the required sequences (0 for lncRNA;1 for mRNA) [default:0]
-s			class probability cutoff (extract sequences with probability greater than or equal to s
--min			specifiy min_length of sequences[default:0]
--max			specifiy min_length of sequences[default:Inf]
```



# COPYING

GNU Public License version 3 (GPLv3)
Details on http://www.gnu.org/copyleft/gpl.html

