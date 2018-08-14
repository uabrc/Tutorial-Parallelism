# Scenario #
Often times, one needs to submit a huge number of very similar jobs on the cluster. For example, if you want to  run a software with same parameters over lots of subjects or if you want to run Quality Check on all the raw data that you have to process.

# Methods #

## Manual process##
The most basic method would be to create a [SLURM](https://docs.uabgrid.uab.edu/wiki/Slurm#Batch_Job) job script, and copy it over and over again, till you have an individual job script for each subject, and then you can make the minor tweak, so that each job is processing on the correct subject.

*Problem:*   
* Lots of manual work and really time consuming.

## Bash Scripting ##
Second method would be to create a bash script, that will loop through the files in your DATA directory, and create a job script for each one, as well as submit it.
```
for file_name in `ls $data_dir`
do
#Do something here. 
#Each loop would have a unique value (file name in $data_dir) 
#that would be in $file_name variable
done
```

Usage:  
```
./prep_env seq
./bash_parallel_script
```

*Problem:*  
* This script would be great for a small number of files (20ish), not for large numbers.  
* SLURM will have to keep track of and schedule each job/file, which is not very efficient for SLURM, so you might have to increase time between job submissions.


## Array Jobs ##
A better method to achieve this is to use [SLURM job arrays](https://slurm.schedmd.com/job_array.html)   
###Sequential File Names###
If your input files are named sequentially, then you can utilize the environment variable ${SLURM_ARRAY_TASK_ID} to submit different files in different array tasks.
```
#!/bin/bash
#SBATCH --array=1-5
.
.
#SBATCH --job-name=test_job_%A_%a
#SBATCH --error=job_err/test_job_%A_%a.err
#SBATCH --output=job_out/test_job_%A_%a.out
.
.
srun echo "Processing file test$SLURM_ARRAY_TASK_ID" >> test_dir/test$SLURM_ARRAY_TASK_ID
srun sleep 30
```
* %A in the #SBATCH line becomes the job ID
* %a in the #SBATCH line becomes the array index
* ${SLURM_ARRAY_TASK_ID} is a shell variable that is set when the job runs, and each array task has unique value: 1, 2, .., 5



# Content #

* **prep_env**:  
An executable to create test environment to run the job scripts on, along with job error and job output directories.

	Usage: ./prep_env {seq,rand.diff}  

	seq: Create a test_dir with sequential file names.    
```
		Example:    
			└── test_dir  
				├── test1  
				├── test2  
				├── test3  
				├── test4  
				└── test5     
```
	rand: Create a test_dir with random file names.    
```
		Example:  
			└── test_dir  
			    ├── CU4KT7  
			    ├── GEJ0IF  
			    ├── LHZBSN  
			    ├── MYODBC  
			    └── X9E4T5    
```
	diff: Create a test_dir with random sub-directory names, and a file 'test' in each directory.    
```
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
```

* **clean_env**:  
An executable to remove all the files/directories from your current directory, except the ones originally present.

	Usage: ./clean_env


* **bash_parallel_script**:  
This is a bash script which creates and submits jobs for each file in the test directory.

* **array_seq.job**:  
This is a [SLURM](https://docs.uabgrid.uab.edu/wiki/Slurm) Array batch script, which works when the names of input files are in sequential form.  

* **array_rand.job**:  
This is a [SLURM](https://docs.uabgrid.uab.edu/wiki/Slurm) Array batch script, which works when the names of input files are in random form.



