# `qsub` with R: Two-file Method
[Back](README.md)

NOTE: The dollar sign is a placeholder for the informative text before the actual command prompt (e.g. `[username@login4 ~]$` is simplified to `$`)

## 1. Setup: Load R
```console
$ module load R/4.0.2
```

## 2. Create `run.sh` and `analysis.R`
Create the files with `nano`, `vim`, `emacs`, or another editor of your choice.

`analysis.R`
```R
x <- 1:10
y <- rep(1:2, 5)
df <- data.frame("x" = x, "y" = y)
write.csv(df, file = "df.csv")
```

`run.sh`
```shell
#!/bin/bash
#$ -N my_R_analysis
#$ -cwd
#$ -V
#$ -j y
#$ -o logs/$JOB_NAME.$JOB_ID.log
#$ -l h_data=1G,h_rt=00:10:00
#$ -pe shared 2
#$ -M $USER@mail
#$ -m bea

Rscript analysis.R
```
Exit your text editor once you're finished. The first line of `run.sh` is what is known as a shebang. This indicates what program to run the script with when it is run as an executable. The following lines are the options that the job scheduler will parse. This program runs `Rscript` which interprets the R file and generates a dataframe, saving it to a `.csv` file.

Any output printed by the program will be saved in a `.log` file in an automatically generated `logs` folder.

NOTE: make sure to have the `-V` option specified. This copies the environment variables for R that was loaded with `module load` into the job's environment.

## 3. Turn `run.sh` into an executable and run
```console
$ chmod u+x run.sh
$ ./run.sh
$ ls
run.sh df.csv
```
This simple program was run in the login node. Let's run it as a job.

## 5. Submit `run.R` to the job scheduler
```console
$ rm df.csv # remove the .csv file generated earlier
$ qsub run.sh
Your job 4714826 ("my_R_analysis") has been submitted
```
The job will run and you'll receive two emails, one indicated the job started, and one indicating it ended.
```console
$ ls
df.csv  logs  run.R
$ ls logs
my_R_analysis.4714826.log
$ cat df.csv
"","x","y"
"1",1,1
"2",2,2
"3",3,1
"4",4,2
"5",5,1
"6",6,2
"7",7,1
"8",8,2
"9",9,1
"10",10,2
```
NOTE: type `q` to exit `less`.