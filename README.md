# Hoffman2 Guide

## Getting Started
Hoffman2's new documentation can be found [here](https://www.hoffman2.idre.ucla.edu/). Instructions on how to request an account can be found there.

Additional resources:
* For submitting batch jobs to Univa Grid Engine, look under section 4 of [this](http://www.univa.com/resources/files/univa_user_guide_univa__grid_engine_854.pdf) pdf. Section 4.2.2: _Example 2: An Advanced Batch Job_ is particularly helpful.
* Hoffman2 documentation page on computing is [here](https://www.hoffman2.idre.ucla.edu/Using-H2/Computing/Computing.html).
* The (old) IDRE Hoffman2 webpage on commonly-used UGE commands is [here](http://web.archive.org/web/20170226162759/https://www.hoffman2.idre.ucla.edu/computing/sge/).
* Another (old) page on UGE commands and command files is [here](http://web.archive.org/web/20170517201108/http://www.hoffman2.idre.ucla.edu/computing/running/#Build_a_UGE_command_file_for_your_job_and_use_UGE_commands_directly).
* Another useful website from the Center for Cognitive Neuroscience is [here](https://www.ccn.ucla.edu/wiki/index.php/Hoffman2).

To find comprehensive documentation for most of the commands mentioned below, use the command:
```
man <command>
```
To search the documentation:
* Type `/` + `phrase`, and then the `ENTER` key
* Next occurance: Type `n` and then `ENTER`
* Previous occurance: `SHIFT` + `n` and then `ENTER`

## Logging in
I recommend using `ssh` to log in. The basic syntax is:
```bash
ssh <username>@hoffman2.idre.ucla.edu
```
After entering your password, you should find yourself at the login node.

## Navigating Hoffman2 and Some Tidbits
Use the command `cd` to get to your home directory (only 20 GB of storage here). The scratch directory (stored in the variable `SCRATCH`) gives you 2 TB of storage, but with the caveat that files that haven't been modified in the past couple weeks will be deleted.
```bash
cd              # to get to home
cd $SCRATCH     # to get to scratch

echo $SCRATCH   # to print variables (in this case, printing the scratch directory path)
pwd             # to print out where you are in the filesystem
```

To touch all your files in the scratch directory:
```bash
find $SCRATCH -exec touch {} \;
```

Hoffman2 uses what are called "modules" to quickly load up the necessary environment for certain programs and libraries.
```bash
module avail                # to see all available modules
module avail python         # viewing the available submodules for a module (most modules have various versions as the submodules)
module load python/3.7.2    # establish environment for a module
module unload python/3.7.2  # unload module from the environment
module purge                # unload all modules from the environment
```

## Interactive session
`qrsh` is the command to use for creaing a so-called "interactive session". When you request an interactive session, the cluster allocates you the set of resources you specify. Without specifying options, `qrsh` defaults to a 2 hour session. Here are some common options:
 * `-l`: resources that you want allocated
    - `h_rt=<time>` specifies the amount of time
    - `h_data=<mem>` specifies the amount of memory
    - `highp` specifies that you want to use resources owned by your group (check with `mygroups`). This can make the time for initialization faster.
 * `-pe shared <n>`: specifies the number of CPU cores to allocate. For multiple CPU cores, `h_data` refers to the memory per core, so the total memory requested will be `h_data * pe shared`
```bash
# 4 hours, 4GB of memory per core, 4 CPU cores, use owned resources, (total of 16GB memory requested)
qrsh -l h_rt=4:00:00,h_data=4G,highp -pe shared 4

# 2 hours, 1GB of memory per core, 12 cores ditributed across multiple compute nodes
qrsh -l h_data=1G,h_rt=2:00:00 -pe dc\* 12
```

NOTE: the more resources you request, the longer it takes for the session to initialize. The same thing goes for submitting jobs.

## Running Jobs
`qsub` is the command for submitting jobs to the cluster. Options include:
* `N <name>`: name that you would like to assign to the job
* `-cwd`: run the job in the same directory that the executable is in
* `-o <filename>`: file that output of program will be appended to
* `-V`: copies all environmental variables present when submitting the job
* `-j <y/n>`: merge error log with the normal output of the program (default `n` for no) 
* `-l`: resources. `h_rt`, `h_data`, `highp`
* `-pe shared <n>`: CPU cores
* `-M $USER@mail`: send notifications via email
* `-m bea`: notifications sent when: `b` job begins, `e` job ends, `a` job aborts
* `-b <y/n>`: specify if the executable is a binary (default `n` for no)

Example that the documentation provides:
```bash
qsub -l h_rt=200,h_data=50M \
    -o $SCRATCH/hello_world.$JOB_ID \ # JOB_ID is a number assigned by the job scheduler
    -j y \
    -M $USER@mail \
    -m bea \
    -b y \
    $HOME/fortran/hello_world # file, either executable binary or shell script
```

## Running Jobs with Shell Scripts
The following are tutorials on how to use shell scripts when submitting jobs to the job scheduler.
### [Simple shell script tutorial](shell.md)
### [Using R](shell_R.md)

## Job Tidbits
Some useful commands:
```shell
myjobs              # lists jobs that are queueing and running
qdel <job-ID>       # removes job from the queue or aborts running job
                    # job-ID can be found with myjobs
qhold <job-ID>      # keeps the job in queue until you call...
qrls <job-ID>
```