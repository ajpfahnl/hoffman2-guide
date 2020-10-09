# `qsub` with Shell Scripts
[Back](README.md)

Instead of specifying the job options for `qsub` in the shell, you can specify them in a shell script. Let's do a little walkthrough on how to do this.

NOTE: The dollar sign is a placeholder for the informative text before the actual command prompt (e.g. `[username@login4 ~]$` is simplified to `$`)

## 1. Setup
```console
$ cd $SCRATCH
$ mkdir test_folder
$ cd test_folder
$ nano run.sh # or any other text editor (e.g. vim, emacs, ...)
```

## 2. Add the following lines to `run.sh`
```shell
#!/bin/bash

echo Hello world!
```
Exit your text editor once you're finished. The first line is what is known as a shebang. This indicates what program to run the script with when it is run as an executable. `echo` is a shell command that prints out it's argument.

## 3. Turn `run.sh` into an executable and run
```console
$ chmod u+x run.sh
$ ls # list the files in the directory
run.sh
$ ./run.sh
Hello world!
```
This simple program was run in the login node. Let's change it so that we can run it as a job.
## 4. Edit `run.sh`
```console
$ nano run.sh
```
Change run.sh to the following:
```shell
#!/bin/bash
#$ -N my_test
#$ -cwd
#$ -l h_data=1G,h_rt=00:10:00
#$ -pe shared 2
#$ -M $USER@mail
#$ -m bea

echo Hello world!
```

## 5. Submit `run.sh` to the job scheduler
```console
$ qsub run.sh 
Your job 4713234 ("my_test") has been submitted
```
The job will run and you'll receive two emails, one indicated the job started, and one indicating it ended.
```console
$ ls
my_test.e4713234  my_test.o4713234  my_test.pe4713234  my_test.po4713234  run.sh
$ less my_test.o*
Hello world!
```
NOTE: type `q` to exit `less`.

