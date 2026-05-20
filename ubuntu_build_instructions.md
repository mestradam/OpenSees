# Instructions to build OpenSees-epfl in Ubuntu/LinuxMint

1. Create bin and lib directories and clone de OpenSees epfl repo at home directory

```
cd ~
mkdir bin
mkdir lib
git clone https://github.com/mestradam/OpenSees.git
```

2. Dependencies needed

```
sudo apt update
sudo apt install make tcl8.6 tcl8.6-dev gcc g++ gfortran python3-dev hdf5-dev
```

3. Makefile.def was originally moved from the UBUNTU Makefile.def 

```
cp MAKES/Makefile.def.EC2-UBUNTU Makefile.def
```

This is no longer needed because Makefile.def is already the correct one


4. Fixes made to Makefile.def afetr moved from directory "MAKES"

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








