# `qsub` with R: One-file Method
[Back](README.md)

**_WARNING_**: This method may not work when using `/usr/bin/env` in the shebang (reason unknown). See [this](https://stackoverflow.com/questions/17496810/usr-bin-env-rscript-no-such-file-or-directory-after-recent-r-3-0-1-install) StackOverflow post. I've had issues with this method and have defaulted to the two-file method.

NOTE: The dollar sign is a placeholder for the informative text before the actual command prompt (e.g. `[username@login4 ~]$` is simplified to `$`)

## 1. Setup: Load R
```console
$ module load R/4.0.2
$ nano run.R # or any other text editor (e.g. vim, emacs, ...)
```

## 2. Add the following lines to `run.R`
```shell
#!/usr/bin/env Rscript
#$ -N my_R_test
#$ -cwd
#$ -V
#$ -j y
#$ -o logs/$JOB_NAME.$JOB_ID.log
#$ -l h_data=1G,h_rt=00:10:00
#$ -pe shared 2
#$ -M $USER@mail
#$ -m bea

x <- 1:10
y <- rep(1:2, 5)
df <- data.frame("x" = x, "y" = y)
write.csv(df, file = "df.csv")
```
Exit your text editor once you're finished. The first line is what is known as a shebang. This indicates what program to run the script with when it is run as an executable. The following lines are the options that the job scheduler will parse. This program generates a dataframe and saves it as a `.csv` file.

Any output printed by the program will be saved in a `.log` file in an automatically generated `logs` folder.

NOTE: make sure to have the `-V` option specified. This copies the environment variables for R that was loaded with `module load` into the job's environment.

## 3. Turn `run.R` into an executable and run
```console
$ chmod u+x run.R
$ ./run.R
$ ls
run.R df.csv
```
This simple program was run in the login node. Let's run it as a job.

## 5. Submit `run.R` to the job scheduler
```console
$ rm df.csv # remove the .csv file generated earlier
$ qsub run.R
Your job 4714826 ("my_R_test") has been submitted
```
The job will run and you'll receive two emails, one indicated the job started, and one indicating it ended.
```console
$ ls
df.csv  my_R_test.e4714826  my_R_test.o4714826  my_R_test.pe4714826  my_R_test.po4714826  run.R
$ less df.csv
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