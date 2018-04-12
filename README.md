# PLncPRO
|//////////////////////////////////////////////////////////////////////|
|                                                                      |
|  PLNCPRO - 	Plant Long Non-Coding rna Prediction by Random fOrests |
|  Author: 	Urminder Singh                                         |
|  Contact: 	urmind13_sit@jnu.ac.in                                 |
|  Website:     http://ccbb.jnu.ac.in/plncpro                          |
|  Version: 	1.1                                                    |
|  Updated on: 	July 18, 2016                                          |
|                                                                      |
|//////////////////////////////////////////////////////////////////////|

                          _____  _            _____  _____   ____  
                         |  __ \| |          |  __ \|  __ \ / __ \ 
                         | |__) | |_ __   ___| |__) | |__) | |  | |
                         |  ___/| | '_ \ / __|  ___/|  _  /| |  | |
                         | |    | | | | | (__| |    | | \ \| |__| |
                         |_|    |_|_| |_|\___|_|    |_|  \_\\____/ 
			 
=====================
  Publication
=====================
https://academic.oup.com/nar/article/45/22/e183/4282667
                                           
=====================
  INTRODUCTION
=====================
We have developed a program to classify coding (mRNAs) and long non-coding transcripts (lncRNAs).
Our method is based on random forest method and uses protein homology search, sequence based and 3-mer frequency based features. 
We have developed predictive models for several plant species to predict lncRNAs. 
We comprehensively tested our method on plants and vertebrates and found that our model works better as compared to the existing tools.

=====================
  INSTALLATION
=====================
Pre-requisite: 
1. OS: Linux
2. Python 2.7.11 or later versions (http://www.python.org/)
	a.)NumPy (http://www.numpy.org/)
	b.)SciPy (https://www.scipy.org/)
	c.)Scikit-learn (http://scikit-learn.org/)
	d.)Biopython (http://biopython.org/)
3. NCBI BLAST (https://blast.ncbi.nlm.nih.gov/Blast.cgi)
4. GNU C Library (glibc >= 2.14)

Steps:
1. Install Python and the required modules

2. Download PLNCPRO.1.0.tar.gz from http://localhost/lncRNAtool/
   and extract the files.
   $ tar zvxf plncpro.1.0.tar.gz

3. make framefinder executable
   * Go to directory plncpro/lib/estate	    
   $ sudo make
   * Copy framefinder executable to plncpro/lib/framefinder
   $ cp bin/framefinder ../framefinder

3. Setup BLAST.
   * Put the blast binaries in folder plncpro/lib/blast/bin

  


=====================
  USAGE AND EXAMPLES
=====================
Usage of prediction.py -- To label lncRNAs and mRNAs. This file reads an input
file containing sequences and then classifies the sequences as coding or
non-coding. It uses a model generated by build.py to make classifications.
It outputs a file containing class label and class probabilities for each
sequence.
 
python prediction.py -i input_fasta_file -o output_file -p output_file_name -t number_of_threads 
   -d path_to_blastdb -m path_to_model_file

		PARAMETERS
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
   
Examples: 
1. $ python prediction.py -i sample_data/test/neg.fa -p pred_res -o sample_preds -m sample_out/sample_model -d lib/blastdb/sprotdb/sprotdb -t 10

   Above command will label the sequences in the 'neg.fa' file using
   10 threads. The output files will be written to the 'sample_preds'
   directory and 'pred_res' will contain the predicted class with
   probabilistic score. Each sequence predicted as mRNA will be
   labelled as 1 and lncRNAs will be labelled as 0.

   

2. $ python prediction.py -i sample_data/test/neg.fa -p pred_res -o sample_preds -m sample_out/sample_model -d lib/blastdb/sprotdb/sprotdb -t 10 --min_len 500

   This command is same as above but it will use sequences with length
   greater than or equal to 500 bp for prediction.

   
     
==========================================================================================================

Usage of build.py -- used to build model using the given training data
(mRNA/lncRNA transcripts). This file reads two labelled datasets
containing coding and non-coding transcripts. Then it makes a random
forest based classification model and saves the model, which can be used
to predict unknown sequences.

 
python build.py -p mRNAs_fasta -n lncRNAs_fasta -m output_model_name -t number_of_threads 
   -o output_dir -d path_to_blast_database
		
		PARAMETERS   
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
  
           

Examples: 
1. $ python build.py -p sample_data/train/mrna.fa -n sample_data/train/lncrna.fa -o sample_out -m sample_model -d path_to_blast_db -t 10

   NOTE: This constructs a model using the mRNA sequences in the
   'pos.fa' file and lncRNA in 'neg.fa'. The program outputs the model in
   the file 'sample_model' in 'sample_out' directory. 
   To use this model for predictions simply give the path to this 
   model file as the -m,-- model argument in prediction.py, as below:
   
    $ python prediction.py -i test.fa -o prediction_out -p prediction_file -m sample_out/sample_model -d path_to_blast_db -t 10

2. $ python python build.py -p sample_data/train/pos.fa -n sample_data/train/neg.fa -o sample_out -m sample_model -d lib/blastdb/sprotdb/sprotdb -t 10 --min_len 300

   This command will use all sequences from neg.fa and pos.fa having
   length greater than or equal to 300 bp for constructing the model.


==========================================================================================================

Usage of predtoseq.py -- used to extract mRNA or lncRNA sequences from
PLNCPRO output file. This file reads a prediction output file and
extracts sequences from a given class. User can specify class and
probability cut-off and extract desired transcript sequences.


python predtoseq.py -f fasta_file -o outputfile -p PLNCPRO_prediction_file -l required_label 
  		
		PARAMETERS
-f			input fasta file name
-o			output fasta file name	
-p			path to file containg predictions by PLNCPRO
		OPTIONAL
-l			label of the required sequences (0 for lncRNA;1 for mRNA) [default:0]
-s			class probability cutoff (extract sequences with probability greater than or equal to s
--min			specifiy min_length of sequences[default:0]
--max			specifiy min_length of sequences[default:Inf]



=====================
  COPYING
=====================
GNU Public License version 3 (GPLv3)
Details on http://www.gnu.org/copyleft/gpl.html
