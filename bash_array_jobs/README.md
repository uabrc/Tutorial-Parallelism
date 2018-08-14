# Scenario #
Often times, one needs to submit a huge number of very similar jobs on the cluster. For example, if you want to  run a software with same parameters over lots of subjects or if you want to run Quality Check on all the raw data that you have to process.

# Methods #

## Manual process##
The most basic method would be to create a [SLURM](https://docs.uabgrid.uab.edu/wiki/Slurm#Batch_Job) job script, and copy it over and over again, till you have an individual job script for each subject, and then you can make the minor tweak, so that each job is processing on the correct subject.

*Problem*: Really time consuming.

## Bash Scripting ##
 

# Content #

* **prep_env**:  
An executable to create test environment to run the job scripts on, along with job error and job output directories.

	Usage: ./prep_env {seq,rand.diff}  

	seq: Create a test_dir with sequential file names.    
		Example:    
			└── test_dir  
				├── test1  
				├── test2  
				├── test3  
				├── test4  
				└── test5     

	rand: Create a test_dir with random file names.    
		Example:  
			└── test_dir  
			    ├── CU4KT7  
			    ├── GEJ0IF  
			    ├── LHZBSN  
			    ├── MYODBC  
			    └── X9E4T5    

	diff: Create a test_dir with random sub-directory names, and a file 'test' in each directory.    
		Example:  
			└── test_dir  
			    ├── 1DOVW8  
			    │   └── test  
			    ├── 75DJ90  
			    │   └── test  
			    ├── 7CWD36  
			    │   └── test  
			    ├── FTG47D  
			    │   └── test  
			    └── L9D3C4  
			        └── test  


* **clean_env**:  
An executable to remove all the files/directories from your current directory, except the ones originally present.

	Usage: ./clean_env


* **bash_parallel_script**:  
This is a bash script which creates and submits jobs for each file in the test directory.

* **array_seq.job**:  
This is a [SLURM](https://docs.uabgrid.uab.edu/wiki/Slurm) Array batch script, which works when the names of input files are in sequential form.  

* **array_rand.job**:  
This is a [SLURM](https://docs.uabgrid.uab.edu/wiki/Slurm) Array batch script, which works when the names of input files are in random form.



