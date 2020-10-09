# Using a GUI in an Interactive Session
[Back](README.md)

## Setup
Hoffman2 uses a protocol called X11 as the framework for the GUI environment. You will need an X11 environment set up on your computer.

__For MacOS users__: Install XQuartz from [here](https://www.xquartz.org/).

__For Windows users__: look up tutorials on how to install and use [Xming](http://www.straightrunning.com/XmingNotes/) with [PuTTy](https://www.chiark.greenend.org.uk/~sgtatham/putty/). The following 3rd party tutorials might work:
* https://cc.jlab.org/windows/X11onWindows
* http://www.geo.mtu.edu/geoschem/docs/putty_install.html

## Test on Hoffman2
Log in to Hoffman2 with `ssh -Y`, which enables trusted X11 forwarding. The "trusted" part is necessary for newer versions of MacOS, otherwise you can use `-X`.
```bash
ssh -Y <username>@hoffman2.idre.ucla.edu
```
Test to see if X11 is working with the following command:
```shell
xeyes
```
You should be able to see a pair of eyes on your screen that move with your cursor.

## RStudio example
To use RStudio, load the version of R you want and the RStudio module
```shell
module load R/4.0.2
module load Rstudio
rstudio
```
You should be able to see the RStudio GUI rendering. This process tends to be very slow, so my recommendation is to use RStudio locally, and then run your scripts on the server without a GUI.