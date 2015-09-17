Installation of Spharm toolkit in MacOS
===================
##Tested on:
OS X Yosemite version 10.10.5
Matlab 2014a (8.3.0.532), 64-bit

##Download, Installation and Tutorial

SPHARM-MAT
http://imaging.indyrad.iupui.edu/projects/SPHARM/?page_id=22

Spharm-pdm
https://www.nitrc.org/projects/spharm-pdm/

SPHARM-MAT Tutorial
http://imaging.indyrad.iupui.edu/projects/SPHARM/SPHARM-docs/C01_Introduction.html


##To use SPHARM-MAT-v1-0-0
In the directory SPHARM-MAT-v1-0-0/code there are files that contains the instruction:

> [path, name, ext, ver] = fileparts(file);

The number of the return parameters is four, but to use the toolkit with Matlab 2014a it is necessary to change them, in all the files in the directory, to the new version that only contains three. The parameter eliminated is always the fourth one from left to right.

> [path, name, ext] = fileparts(file);

##To use SPHARM-MAT-v1-0-0 + Spharm-pdm
The new version of Spharm-pdm uses the functions SegPostProcessCLP and GenParaMeshCLP, but when they are invoked from SPHARM-MAT, it calls to the functions SegPostProcess and GenParaMesh that are deprecated. In order to have everything working togueter it is necessary to do some changes in the files of SPHARM-MAT.

1. Install Spharm-pdm according to http://imaging.indyrad.iupui.edu/projects/SPHARM/SPHARM-docs/C01_Introduction.html#install-spharm-pdm
2. Change, in all the lines needed, the call of the funtions SegPostProcess to SegPostProcessCLP, and  GenParaMesh to GenParaMeshCLP in the files
	* SPHARM-MAT-v1-0-0/code/SpharmMatUtilBatchJobs.m
	* SPHARM-MAT-v1-0-0/code/initOptions.m
3. Since the SegPostProcessCLP and GenParaMeshCLP are new versions, they have different flags used at the moment of the invokation, so in the file SpharmMatUtilTopologyFix.m it is necessary to change the lines:
from optionSTR=[optionSTR '-o' new_name];
to optionSTR=[optionSTR '  ' new_name];
