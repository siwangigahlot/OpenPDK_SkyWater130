# OpenPDK_SkyWater130
This repository contains the documentation of the work done for clearing the Physical Design screening test of ESSCI-SFAL-VSD VLSI Training program for Women.

The workshop describes the physical verification process that takes place during an RTL to GDSII flow using Skywater 130nm Technology, including DRC and LVS tests. This helps the participants get ready for the tape out and is particularly helpful for chip fabrication. The workshop assists in locating and fixing violations made during the physical verification stage.

More information about the workshop can be found at https://www.vlsisystemdesign.com/physical-verification-using-sky130/

A special thanks to Tim and Kunal for creating and helping out.

<a href = "https://github.com/RTimothyEdwards"> R. Timothy Edwards </a><br>

<a href = "https://github.com/kunalg123"> Kunal Ghosh </a>


# Index
- Chapter 0 - Getting the tools
- Chapter 1 - Understanding the design flow
- Chapter 2 - DRC and LVS Fundamentals
- Chapter 3 - DRC Issues
- Chapter 4 - LVS
- Additional Content Workshop 

# Chapter 0 - Getting the tools
Stepping into the LINUX Operating System. First download the VirtualBox and then UBUNTU by following the given links.<br>
1. <a href = "https://www.virtualbox.org/">Download VirtualBox </a><br>
2. <a href = "https://ubuntu.com/download/desktop">Download UBUNTU_26.04 </a><br>

Open Ubuntu and right-click to open terminal. Everything will be coded here.Now before stepping into installation of tools lets first install pre-requisites.

```bash
$ sudo apt-get update
$ mkdir -p eda_tools
$ cd eda_tools
$ echo "installing pre-requisite"
echo "___________________________________________________"
sudo apt-get install build-essential clang bison flex \
	libreadline-dev gawk tcl-dev libffi-dev git \
	graphviz xdot pkg-config python3 libboost-system-dev \
	libboost-python-dev libboost-filesystem-dev zlib1g-dev \
	make m4 tcsh csh libx11-dev gperf  tcl8.6-dev tk8.6 tk8.6-dev \
	libxmp4 libxpm-dev  libxcb1 libcairo2  \
      libxrender-dev libx11-xcb-dev libxaw7-dev freeglut3-dev automake yosys
$ sudo apt-get update
$ sudo apt-get -y install libtool
```
 
To adequately utilize the open source skywater130 pdk and understand the design flow, we first require to install all the tools, which are

- open_pdk
- magic
- ngspice
- xschem
- netgen
  ![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Pre-requisites/ToolsImage/toolchains.png)

Note: open_pdk has to be installed last so it can correctly associate the xschem and magic directories. Note: if the configure step fails during any process, its most likely due to missing additional packages, and they need to be installed (preferably from source) to complete the installation

### Magic <br>
Magic is an open-source VLSI layout tool.

Install steps:
```bash
$  git clone git://opencircuitdesign.com/magic
$  cd magic
$	 ./configure
$  make
$  sudo make install
```
More info can be found at http://opencircuitdesign.com/magic/index.html<br>

### Netgen
Netgen is a tool for comparing netlists, a process known as LVS, which stands for "Layout vs. Schematic"

Install steps:
```bash
$  git clone git://opencircuitdesign.com/netgen
$  cd netgen
$	./configure
$  make
$  sudo make install
```
More info can be found at http://opencircuitdesign.com/netgen/index.html<br>


### Xschem
Xschem is a schematic capture program

Install steps:
```bash
$  git clone https://github.com/StefanSchippers/xschem.git xschem_git
$	./configure
$  make
$  sudo make install
```
More info can be found at http://repo.hu/projects/xschem/index.html<br>

### Ngspice
ngspice is the open-source spice simulator for electric and electronic circuits.

Install steps:

After downloading the tarball from https://sourceforge.net/projects/ngspice/files/ to a local directory, unpack it using:
```bash
 $ tar -zxvf ngspice-37.tar.gz
 $ cd ngspice-37
 $ mkdir release
 $ cd release
 $ ../configure  --with-x --with-readline=yes --disable-debug
 $ make
 $ sudo make install
```
More info can be found at https://ngspice.sourceforge.io/index.html<br>


Please note that to view the simulation graphs of ngspice, xterm is required and can be installed using.
```bash
$ sudo apt-get update
$ sudo apt-get install xterm
open_pdk
```
Open_PDKs is distributed with files that support the Google/SkyWater sky130 open process description https://github.com/google/skywater-pdk. Open_PDKs will set up an environment for using the SkyWater sky130 process with open-source EDA tools and tool flows such as magic, qflow, openlane, netgen, klayout, etc.

Install steps:
```bash
$  git clone git://opencircuitdesign.com/open_pdks
$  open_pdks
$	./configure --enable-sky130-pdk
$  make
$  sudo make install
```
Now that we have all the necessary tools installed let's understand the design flow!

# Chapter 1 - Understanding the design flow
Verifiying the open_pdk installation

An initial working directory can be made by copying the required files as follows:
```bash
$ mkdir Lab1_and
$ cd Lab1_and
$ mkdir mag
$ mkdir netgen
$ mkdir xschem
$ cd xschem
$ cp /usr/local/share/pdk/sky130A/libs.tech/xschem/xschemrc .
$ cp /usr/local/share/pdk/sky130A/libs.tech/ngspice/spinit .spiceinit
$ cd ../mag
$ cp /usr/local/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc .magicrc
$ cd ../netgen
$ cp /usr/local/share/pdk/sky130A/libs.tech/netgen//sky130A_setup.tcl .
```
Magic in my Desktop:-
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Pre-requisites/ToolsImage/MagicOpened.png)

Netgen in my Desktop:-
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Pre-requisites/ToolsImage/Netgen.png)

Xschem in my Desktop:-
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Pre-requisites/ToolsImage/xschem1opened.png)

Ngspice in my Desktop:-
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Pre-requisites/ToolsImage/NgspiceOpened.png)

# Creating inverter schematic using xschem

An initial schematic is made by placing components from the open_pdk library
The required changes to the properties of the device can be made here and will automatically reflect in the layout 
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Lab1/inverter.sch.png)

Converted the schematic to a symbol 
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Lab1/invertersym.png)

Using the symbol, we can create an independent test bench to simulate the circuit 
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Lab2/inverter.tb.sch.png)

#Creating and simulating testbench Schematic

The circuit can be simulated in ngspice. make sure to disable .subckt in the simulation tab for the netlist generated for the sim
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/Lab2/simulation%20combo.png)

# Creating inverter layout in Magic and exporting its netlist

The original schematic can be used to export a netlist, which can be imported into magic to create the layout.

Set the appropriate device properties and route the layout. 
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/lab3/pfet%20para.png)
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/lab3/nfet%20par.png)
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/lab3/extractcommandsmagic.png)

# Performing LVS checks on testbench and layout netlists

The netlist of the layout can be extracted using
``` bash
% extract do local
% extract all
% ext2spice lvs
% ext2spice
```

ext files are intermidiate files and can be cleaned using
``` bash
rm *.ext
/usr/local/share/pdk/scripts/cleanup_unref.py -remove
```

The schematic netlist and layout netlist can be compared using LVS by netgen
```bash
$ netgen -batch lvs "../mag/inverter.spice inverter" "../xschem/inverter.spice inverter"
```
At first the LVS done but msimatch.
![image](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/lab4/mismatch%20lvs.png)

After LVS , the parasitic capacitances couldnot load complete but still here is what I got in terminal.
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/lab4/after_lvs1.png)
![image alt](https://github.com/siwangigahlot/OpenPDK_SkyWater130/blob/main/lab4/after_lvs2.png)











