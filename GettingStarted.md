## Installation ##

StokesDT runs on x86 based Linux systems with Pentium 4 or more recent processors. To build the source code, StokesDT requires Intel C++ compiler version 13.0 or greater and MKL version 11.0 or greater. It also requires installing the latest Maltab and the Mex compiler if the user wants to use the Matlab interface.

It is very straightforward to compile and install StokesDT. First, setup the proper environment variables in makevars.in in the top directory of the StokesDT source tree. For example:
```
CC     = /opt/intel/bin/icc
CXX    = /opt/intel/bin/icpc
LD     = /opt/intel/bin/xild
AR     = /opt/intel/bin/xiar
RANLIB = /opt/intel/bin/ranlib
MEX    = /opt/MATLAB/bin/mex
MEXEXT = mexa64

CXXFLAGS  = -O3 -Wall -w2
CXXFLAGS += -openmp
CXXFLAGS += -mkl
CXXFLAGS += -std=c++11
CXXFLAGS += -Wsign-compare -Wunknown-pragmas -Wreturn-type
CXXFLAGS += -Wunused-variable -Wuninitialized
CXXFLAGS += -Wmissing-prototypes -Wmissing-declarations
CXXFLAGS += -qno-offload
CXXFLAGS += -DENABLE_PROFILE_
MKLROOT = /opt/intel/mkl/

MEXFLAGS = CXXFLAGS="-O3 -Wall -fPIC -w2 -openmp -std=c++11 -qno-offload\
           -I${TOPDIR}/install/include" \
           LDFLAGS="-static-intel -mkl -liomp5 -lm"

CP    = cp -f
RM    = rm -f
MKDIR = mkdir -p
```

To find the proper value of _**MEXEXT**_, run the function _**mexext()**_ in Matlab.

Then, you can run the following the commands to build StokesDT.

  * To compile the StokesDT libraries and the main executable,
```
make
```
  * To build and run the test programs,
```
make test
```
  * To build the Matlab interface,
```
make matlab
```
  * To build the auxiliary tools,
```
make tool
```

All the libraries, header files and binaries will be installed in the _**install**_ directory.

| _**install/include**_ | header files |
|:----------------------|:-------------|
| _**install/lib**_     | StokesDT libraries |
| _**install/bin**_     | main executable |
| _**install/tools**_   | auxiliary tools |
| _**install/mex**_     | Mex binaries |

## Running StokesDT ##

To run StokesDT simply use the command _**stokesdt**_ and provide the name of the input files. StokesDT uses three input control files to specify a simulation. A detailed description of the control files can be found [here](ControlFile.md).

Generally, start a simulation with the following command:
```
./install/bin/stokesdt --config <config file> --model <model file> --xyz <XYZ file> -n <number of steps to simulate>
```

For more information:
```
./install/bin/stokesdt --help
```

## Using StokesDT as a Library ##

StokesDT provides a flexible, object-oriented user interface for the users who want to develop customized hydrodynamic simulation codes. The _**tests**_ directory contains some examples of using the interface and how to build and link the customized codes. A detailed description of the object-oriented interface can be found [here](API.md).

## Using StokesDT from Matlab ##

StokesDT also provides a Mex interface which allows users to use the highly optimized computational kernels of StokesDT with Matlab. A detailed description of the Matlab interface can be found [here](MexInterface.md).