# tinyBERT-NQ-Dataset

Files: 
1. demo_60k - BERT model fine-tuned on 60k training examples
2. demo_syn - BERT model fine-tuned on 90k training examples (60k original, 30k synonym replacement)
3. build_training_record - Contains code to build a training record
4. build_training_record_syn - Contains code to build a training record w/ synonym replacement

Note: .gz files are compressed training and validations samples. We did not include all of them (too large) but include
      a small set for demo purposes
	
      nq-dev-00.jsonl.gz -> 1600 sample dev set
      nq-dev-sample.jsonl.gz -> 200 sample dev set

      train_0-10_record -> compressed training feature vectors (from 60k training samples) 
      train_syn_0-10_record -> compressed training feature vectors (from 60k training samples augmented to 90k with synonym replacement)

**WARNING** This code requires TensorFlow 1, it is not compatible with TensorFlow 2. 
We used TensorFlow 1.14 specifically but should be compatible with older versions of TensorFlow 1 as well framework.

**********************************************************************************************************************************
How to build predictions file:
------------------------------

~ The predictions file is dumped in a .json format. 
~ Run the command below in either demo folder to generate the predictions 

	python mainHPC.py

How to get performance metrics (F1, precision, recall):
-------------------------------------------------------

~ Once the predictions file has been generated, run the command below to compare the predictions file to the raw evaluation training file (.gz). 

	python nq_eval.py

~ The script will dump F1, precision, and recall for both long and short answers.

How to train model from scratch:
--------------------------------
~ In both demo folders, the “ensemble” directory contains the post trained BERT model weights. 
~ BERT looks at this folder during prediction time in order to reload all of its weights that it learned during training. 
~ To re-train BERT from scratch follow the steps below. 
	1. Delete the “ensemble” directory
	2. Inside mainHPC.py, change from False to True. 
	   This tells BERT to train on the record specified by the ‘trainfile0’ variable
		
		runBERT(trainfile0, record00_size, predictfile0, False, True)
					CHANGE TO
		runBERT(trainfile0, record00_size, predictfile0, True, True)

	3. Run mainHPC.py - this should take 4~5 hours depending on which demo BERT is being retrained. 


How to build training record from scratch:
------------------------------------------

~ In both demo folders, there is a file called ‘train_***_record’. 
~ This record contains the feature vectors that BERT uses to train. 
~ This record is built from the training .gz files. In order to build this from scratch, follow the steps below
	1. Using extract_train_set.py, specify how many .gz files you want to compress into one using the num_train variable. 
	   This script will load each .gz file and compress them into a master .gz file that can be used to generate the record
 	2. Once the master .gz file has been created, run prepare_nq_data.py. 
	   Change the “input_jsonl” flag to the name of the master .gz file and change the “output_tfrecord” flag to the name of the record you want to create
	3. Run prepare_nq_data.py 

***********************************************************************************************************
Reference Websites

NQ Dataset Homepage - https://ai.google.com/research/NaturalQuestions/dataset
Gsutil Download to get all data - https://cloud.google.com/storage/docs/gsutil_install
Github Repository for calculating performance metrics - https://github.com/google-research-datasets/natural-questions
Google BERT Baseline - https://github.com/google-research/language