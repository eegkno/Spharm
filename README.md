Installation of Spharm toolkit in MacOS
===================

##Tested on:
OS X El Capitan version 10.11.6
Matlab 2016b, 64-bit

##Download, Installation and Tutorial

SPHARM-MAT
http://imaging.indyrad.iupui.edu/projects/SPHARM/?page_id=22

Spharm-pdm
https://www.nitrc.org/projects/spharm-pdm/

SPHARM-MAT Tutorial
http://imaging.indyrad.iupui.edu/projects/SPHARM/SPHARM-docs/C01_Introduction.html

## PRE-INSTALL

1. Setup mex: To setup, you need a C/C++ compiler. In the MATLAB Command Window, type:
 > mex -setup C


## INSTALL SPHARM-MAT-v1-0-0

1. Go to the directory of the library (SpharmMatDir):
 > cd SpharmMatDir/code/C_sources

2. Compile compile simple.c
 > mex simple.c

 If this warning is generated:

 >simple.c:485:3: warning: implicitly declaring library function 'memset' with type 'void *(void *, int, unsigned long)' [-Wimplicit-function-declaration]
  memset(comp_table,0,MAX_COMP*sizeof(int));

 include the following headers in the file simple.c:
 >``#include <string.h> /* memset */``
 >``#include <unistd.h> /* close */``

3. compile LocalSmoothing.c
> mex LocalSmoothing.c -lmwlapack -lmwblas

4. Add to Matlab the path of the new files ``SpharmMatDir/code/C_sources``
 NOTE: I tried to copy the new mex files to SpharmMatDir/code, but for  some reason, Matlab doesn't find them.
	``LocalSmoothing.mexa64``
	``LocalSmoothing.mexw32``
	``simple.mexa64``
	``simple.mexw32``

5. Test SPHARM-MAT according to the manual.
 Source: http://www.iu.edu/~spharm/SPHARM-docs/C02_Data_Preparation.html#data-preparation

 Matlab could display errors related to the function:
 ``[path, name, ext, ver] = fileparts(file);``

 This function returns 4 parameters. The last one (ver) needs to be removed from all the files in the source code.
Now the function should look like

 ``[path, name, ext] = fileparts(file);``

6. To use SPHARM-MAT-v1-0-0 + Spharm-pdm

 I downloaded the version 1.8 for Mac (MacOSX_10.5.8.zip).
Source: https://www.nitrc.org/frs/?group_id=308

 Add the path of the SPHARM-PDM according to the "install SPHARM-PDM" of the SPHARM-MAT manual

 ``/home/user/MATLAB/Spharm_2/SPHARM-PDM-v10.5.8``


##Troubleshooting

### Error

```
union (line 106) Invalid input. Valid flags are 'rows', 'stable', 'sorted', 'legacy'.
```

Solution: In the file initParamCALD change

 ``indDif = union(indDif1, indDif2, indDif3);``
to
 ``indDif = union(indDif1, indDif2);``


### Error
```
Error using  ./
Matrix dimensions must agree.
Error in get_vertexnormals (line 42)
vnorm = vnorm./vnlen(:,[1 1 1]);
```

Solution: The function ``get(p,'VertexNormals');`` in get_vertexnormals.m doesn't return anything. Instead of it, I downloaded the function patchnormals() from https://www.mathworks.com/matlabcentral/fileexchange/24330-patch-normals.

The compiled mex file is included within SpharmMatDir/code/C_sources, a directory that was already in the MATLAB PATH (see step 4)

The code in ``get_vertexnormals(vs,fs)`` was modified as follows:
```
function vnorm = get_vertexnormals(vs,fs)
	f = figure;
    p = patch('vertices',vs,'faces',fs);
    %vnorm = - get(p,'VertexNormals');
    FV.vertices = p.Vertices;
    FV.faces = p.Faces;
    vnorm = - patchnormals(FV);
    vnlen = sqrt(sum(vnorm.^2,2));
    vnorm = vnorm./vnlen(:,[1 1 1]);
    vnlen = sqrt(sum(vnorm.^2,2));
    close(f);
return;
```

### Error: SPHARM-MAT-v1-0-0 + Spharm-pdm
The new version of Spharm-pdm uses the functions ``SegPostProcessCLP`` and ``GenParaMeshCLP``, but when they are invoked from SPHARM-MAT, it calls to the functions ``SegPostProcess`` and ``GenParaMesh`` that are deprecated. In order to have everything working togueter, it is necessary to do some changes in the files of SPHARM-MAT.

1. Install Spharm-pdm according to http://imaging.indyrad.iupui.edu/projects/SPHARM/SPHARM-docs/C01_Introduction.html#install-spharm-pdm
2. Change the call of the funtions
 >``SegPostProcess`` to ``SegPostProcessCLP``
 >``GenParaMesh`` to ``GenParaMeshCLP``
 >``ParaToSPHARMMesh`` to ``ParaToSPHARMMeshCLP``

 in the files
	* SPHARM-MAT-v1-0-0/code/SpharmMatUtilBatchJobs.m
	* SPHARM-MAT-v1-0-0/code/initOptions.m
	* initParamCALD
3. Since the ``SegPostProcessCLP`` and ``GenParaMeshCLP`` are new versions, they have different flags used at the moment of the invokation, so in the file ``SpharmMatUtilTopologyFix.m`` it is necessary to change the lines:
>from
>```optionSTR=[optionSTR '-o' new_name];```
>to
>```optionSTR=[optionSTR '  ' new_name];```
