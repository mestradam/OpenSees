# Instructions to build OpenSees-epfl in Ubuntu/LinuxMint

This file contains some short instructions to compile epfl version of OpenSees in an Ubuntu based linux distro. 
It was tested on Linux Mint 22.2 x86_64.

## Build instructions

The following instructions are base on the original instructions file written by the epfl people, which can be found on the directory DEVELOPER.


1. Create bin and lib directories and clone de OpenSees epfl repo at home directory

    ```
    cd ~
    git clone https://github.com/eesd-epfl/OpenSees.git
    mkdir bin
    mkdir lib
    ```

2. Dependencies needed

    ```
    sudo apt update
    sudo apt install make tcl8.6 tcl8.6-dev gcc g++ gfortran python3-dev hdf5-dev
    ```


3. Build OpenSees

    ```
    cd ~/OpenSees
    make
    ```

## Other tinkering already made to adjust the Makefile.def file

These two following steps are no longer needed because they are already done in the Makefile.dev file provided. This information is only to know what has been done to the original Makefile.def file.


1. Makefile.def was originally moved from the UBUNTU Makefile.def 

    ```
    cp MAKES/Makefile.def.EC2-UBUNTU Makefile.def
    ```


2. Fixes made to Makefile.def afetr moved from directory "MAKES"

    comment line 62: "#INTERPRETER_LANGUAGE = PYTHON"

    uncomment line 63: "INTERPRETER_LANGUAGE = TCL"

    line 90: remove dot "/usr/local"

    line 91: remove dot and add username "/home/mem" (mem is the user name)

    add line after 103:
    new line 104: "DEVdir       = $(HOME)/OpenSees/DEVELOPER"

    at new line 108, add "$(DEVdir)" to DIRS definition

    arpack library has some problems, so I will use the one installed in mi PC
    line 99: leave blank the arpack directory "ARPACKdir    =     "
    line 108: remove "$(ARPACKdir)" DIRS definition
    line 133: modify the directory for arpack lib "ARPACK_LIBRARY  = /usr/lib/x86_64-linux-gnu/libarpack.a"
    delete line 150: this prevent the "make clean" to wipe out the arpack library from the system








