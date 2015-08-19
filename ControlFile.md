Three input files are needed to perform simulations with StokesDT. The [config file](ControlFile#Config_File.md) (.cfg) specifies all the simulation parameters, including the time step length, the type of the simulation algorithm, the types of methods for building mobility matrices and computing Brownian forces, etc. The [model file](ControlFile#Model_File.md) (.mod) defines the molecular system to be simulated, including the properties of the particles, and the forces between particles. The [XYZ file](ControlFile#XYZ_File.md) (.xyz) contains the initial positions of the particles.

The config file also specifies the optional output from a simulation, which consists of two types of files. The log file (.log) is a log of the simulation, with performance statistics, error messages and debugging information. The trajectory file, in XYZ format (.xyz), contains the positions of the particles in the simulation at user-defined intervals.


### Config File ###
The config file uses keyword-value pairs to specify simulation parameters. There are four types of keywords: boolean (true or false), integer, float (real floating point number), and string. If a keyword is not specified in the config file, a default value will be used for that keyword. The lines beginning with one or multiple # are comment lines and are not processed by the program. Each line of the config file should contain only one keyword and its value.

The keywords are defined as follows:
|**Keyword** | **Type** | **Description** | **Value**|
|:-----------|:---------|:----------------|:---------|
|delta-t     |float     |the length of time steps|default:0.001|
|mob-method  |string    |the method for computing mobility matrices|<ewald|spme>,default:ewald|
|brownian-method|string    |the method for computing Brownian displacements|<lanczos|cholesky|random>,default:cholesky|
|traj-startstep|integer   |the first frame at which outputs trajectories|default:0 |
|traj-interval|integer   |the interval of outputting trajectories|default:-1(not output trajectories)|
|mob-interval|integer   |the interval for computing mobility matrices|default:100|
|ewald-tol   |float     |the requested tolerance for the Ewald errors|default:1.0e-4|
|spme-nfft-grids|integer   |the dimension of the FFT mesh for computing SPME|default:128|
|spme-porder |integer   |the interpolation order for computing SPME|default:4 |
|spme-xi     |float     |the Ewald parameter for computing SPME|default: 0.5|
|spme-rmax   |float     |the real-space cutoff for computing SPME|default:4.0|
|lanczos-maxiters|integer   |the maximum number of iterations|default:50|
|lanczos-maxnrhs|integer   |the maximum number of right hand sides|default:20|
|lanczos-tol |float     |the requested tolerance|default:1.0e-3|


An example config file:
```
# main control ################################################################
## the length of time steps
## <double>, default: 0.001
delta-t            0.001
## the method for computing mobility matrices
## <ewald|spme>, default: ewald
mob-method           ewald
## the method for computing Brownian displacements
## <lanczos|cholesky>, default: cholesky
brownian-method   cholesky

# trajectory ##################################################################
## the first frame at which trajectories
## <int>, default: 0
traj-startstep         0
## the interval of outputting trajectories
## <int|-1>, default: -1 (not output trajectories) 
traj-interval           -1

# mobility ####################################################################
## the interval for computing mobility matrices
## default: 100
mob-interval            100
## the requested tolerance for the Ewald errors
## <double>, default: 1.0e-4 
ewald-tol            1.0e-4
## the dimension of the FFT mesh for computing SPME
## <int>, default: 128
spme-nfft-grids         128
## the interpolation order for computing SPME
## <int>, default: 4
spme-porder               4
## the Ewald parameter for computing SPME
## <double>, default: 0.5
spme-xi                 0.5
## the real-space cutoff for computing SPME
## <double>, default: 4.0
spme-rmax               4.0

# Lanczos #####################################################################
## the maximum number of iterations
## <int>, default: 50
lanczos-maxiters          50
## the maximum number of right hand sides
## <int>, default: 20
lanczos-maxnrhs            20
## the requested tolerance
## <double>, default: 1.0e-3
lanczos-tol            1.0e-3
```

### Model File ###
The model file contains of a number of predefined "**lines**" that specify the structure of the studied simulation system. A meaningful line begins with a keyword and parameters. The predefined "**lines**" are listed as follows.

  1. _**par**_ lines - each line describes a type of particle.
```
par    par_type    radius    charge
```
where _**par\_type**_ is the type of the particle which can match the XYZ file, _**radius**_ is the particle radius, and _**charge**_ is the charge of the particle

  1. _**steric**_ line - specifies the steric forces.
```
steric    r    k
```
where _**r**_ is the cutoff of the steric forces, and _**k**_ is the steric force constant.

  1. _**vdw**_ lines - each line specifies a type of van der Waals force and its parameters.
```
vdw    type1    type2    contantA    constantB
```

  1. _**elec**_ lines - each line specifies a type of electrostatic force and its parameters.
```
elec    type1    type2    e
```

  1. _**bond**_ lines - each line define a single bond and its parameters
```
bond    id1    id2    r     k
```
where _**id1** and_**id2**_are identifiers of particles to be connected by the bond,_**r**_is the cutoff of the bond, and_**k**_is the bond force constant._

An example model file:
```
# par lines (type, radius, charge)
par A    85.229550     1.0
par B    66.488380    -1.0

# steric line (r0, k0)
steric    2.00    125.0

# vdw lines (type1, type2, contantA constantB)
vdw    A    B    1.0    2.0

# elecstatics lines (type1, type2, e)
elec    A    B    1.0

# bond lines (id1, id2, r0, k0)
bond    1    2    3.0    125.0
bond    2    3    4.0    125.0
bond    4    5    2.0    100.0
bond    5    6    3.0    125.0
```

### XYZ File ###
The XYZ file contains the initial positions of particles. The XYZ file format used in StokesDT is compatible with the standard XYZ format.

The first line specifies the number of particles. The second line of a XYZ file is the optional comment line, which is used by StokesDT to specify the step number, the simulation time and the dimensions of the simulation box. The general form of the comment line in StokesDT is:

```
step_id sim_time Lx Ly Lz
```
  * _**step\_id**_ is the step number
  * _**sim\_time**_ is the current simulation time
  * _**Lx**_, _**Ly**_ and _**Lz**_ specify the dimensions of the simulation box along x, y and z directions, respectively.

Starting from the third line, each particle is described as:
```
par_type X Y Z
```
  * _**par\_type**_ is the type of the particle, which can match one "**par**" line in the model file.
  * _**X**_, _**Y**_ and _**Z**_ specify the x, y and z coordinates of the particle respectively.

An example XYZ file:
```
3
0  0.0  5.008834 5.008834 5.008834
H    1.141210    3.871684    4.132215
H    0.583238    4.300058    1.828481
O    3.368920    3.233168    4.566184
```