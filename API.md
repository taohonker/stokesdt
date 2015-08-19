StokesDT provides an object-oriented interface which can be easily used to develop customized hydrodynamic simulation codes. These APIs are wrapped in static libraries, which can be found in _**install/lib**_. For each main computational kernel in Brownian/Stokesian simulations, StokesDT defines a group of C++ classes, and each of them implements a different method for computing the kernel. Each group of classes generally consists of a base class that contains the abstract interface and a number of derived classes that implements the computational kernels. The base classes defined in StokesDT and their brief descriptions are:

| Base class | Description |
|:-----------|:------------|
| [MobBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_base.html) |Abstract base class for constructing mobility matrix|
| [BrwnBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_brwn_base.html) |Abstract base class for computing brownian displacement vectors|
| [ForceBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_force_base.html) |Abstract base class for computing forces|
| [RndStream](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_rnd_stream.html) |Random number generator|
| [PairListBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_pair_list_base.html) |Abstract base class for Pair list|

A detailed description of the StokesDT library and its APIs can be found [here](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html). The StokesDT library can be used for the following purposes:
  * [Computing Mobility Matrices](API#Computing_Mobility_Matrices.md)
  * [Computing Brownian Displacements/Forces](API#Computing_Brownian_Displacements/Forces.md)
  * [Computing External Forces](API#Computing_External_Forces.md)
  * [Generating Random Vectors](API#Generating_Random_Vectors.md)
  * [Computing Pair List](API#Computing_Pair_List.md)

## Computing Mobility Matrices ##
The base class [MobBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_base.html) and its derived classes can be used to compute Mobility matrices. These classes and their brief descriptions are:

| Class | Description |
|:------|:------------|
| [MobBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_base.html) |Abstract base class for constructing mobility matrix|
|[MobMatrix](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_matrix.html)|Abstract base class for explicitly constructing and storing mobility matrices|
|[MobDirect](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_direct.html,)|Computes mobility matrices with free boundary conditions |
|[MobEwald](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_ewald.html)|Computes mobility matrices using Ewald summation|
|[MobSpme](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_mob_spme.html)|Computes mobility matrix using SPME|

The following example computes a mobility matrix using the Ewald algorithm.
```
// Create a MobEwald object
MobBase *mob = new MobEwald(npos, rdi, box_size, 1.0e-12);
if (!mob->Init()) {
    return false;
}

// Return the dimension of the Mobility matrix
int nm = mob->dim();
int ldm = nm;

// Update the particle positions
mob->Update(pos, rdi);

// Return the mobility matrix
double *mat = (double *)malloc(sizeof(double) * nm * ldm);
if (mat == NULL) {
    return false;
}
mob->GetMatrix(ldm, mat);

// Multiply the Mobility matrix by a vector
// v = alpha * mat + beta * v
mob->MulVector(num_rhs, alpha, ldm, f, beta, ldm, v);

// destroy the MobEwald object
delete mob;
```


## Computing Brownian Displacements/Forces ##
The base class [BrwnBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_brwn_base.html) and its derived classes can be used to compute Brownian displacements. These classes and their brief descriptions are:

| [BrwnBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_brwn_base.html) |Abstract base class for computing brownian displacement vectors|
|:------------------------------------------------------------------------------------------------|:--------------------------------------------------------------|
|[BrwnChol](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_brwn_chol.html)  |Computes Brownian displacement vectors using Cholesky factorization|
|[BrwnLanczos](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_brwn_lanczos.html)|Compute Brownian displacement vectors using the Krylov subspace method|
|[BrwnRandom](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_brwn_random.html)|Computes Brownian displacement vectors using randomized sampling|

The following example computes a Brownian displacement vector using the Lanczos method.
```
// Create a mobility matrix object
MobEwald *mob = new MobEwald(npos, rdi, box_size, 1.0e-12);
if (!mob->Init()) {
    return false;
}

// Generate a normal distribution random vector
int nm = mob->dim();
int ldm = nm;
double *z = (double *)malloc(sizeof(double) * ldm);
if (z == NULL) {
    return false;
}
RndStream *rnd = new RndStream(1234);
if (!rnd->Init()) {
    return false;
}
rnd->Gaussian(0.0, 1.0, 1, nm, ldm, z);

// Create a BrwnLanczos object
BrwnBase *brwn= new BrwnLanczos(nm, 100, 10, 1.0e-7);
assert(lanczos->Init());

// Compute a Brownian displacement vector y 
double *y = (double *)malloc(sizeof(double) * ldm);
if (y == NULL) {
    return false;
}
brwn->Compute(mob, 1, ldm, z, ldm, y);    

// Destroy the BrwnLanczos object
delete brwn;
// Destroy the RndStream object
delete rnd;
// Destroy the MobEwald object
delete mob;
```

## Computing External Forces ##
The base class [ForceBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_force_base.html) and its derived classes can be used to compute external forces, e.g., steric forces or bonded forces. These classes and their brief descriptions are:

| [ForceBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_force_base.html) |Abstract base class for computing forces|
|:--------------------------------------------------------------------------------------------------|:---------------------------------------|
|[StericForce](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_steric_force.html)|Computes steric forces                  |
|[BondedForce](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_bonded_force.html)|Computes bond forces                    |

The following example computes all the steric forces.
```
// Create a StericForce object
ForceBase *steric = new StericForce(npos, rdi, box_size, r0, k0);
if (!steric->Init()) {
    return false;
}

// Compute all the steric forces
int nf = 3 * npos;
double *f = (double *)malloc(sizeof(double) * nf);
if (f == NULL) {
    return false;
}
steric->Compute(pos, rdi, f);

// Destroy the StericForce object
delete steric;
```

## Generating Random Vectors ##
The class [RndStream](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_rnd_stream.html) can be used to generate random vectors in Gaussian or uniform distribution.

## Computing Pair Lists ##
The base class [PairListBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_pair_list_base.html) and its derived classes construct pair list, which can be used to compute short-range interactions. These classes and their brief descriptions are listed as follows:

| [PairListBase](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_pair_list_base.html)|Abstract base class for Pair list|
|:--------------------------------------------------------------------------------------------------------|:--------------------------------|
|[PairListR](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_pair_list_r.html)       |Pair list for absolute distance  |
|[PairListS](http://www.cc.gatech.edu/~xliu66/stokesdt/doc/html/classstokesdt_1_1_pair_list_s.html)       |Pair list for normalized distance|

An example of using Pair cell list to compute short-range interactions are shown as follows:
```
// Create a PairListR object
PairListBase *pair_list = new PairListR(npos, rdi, box_size, steric_r0_);
int nnz = pair_list->Init();

// Update the Pair list; 
nnz = pair_list->Build(pos);

// Return short-range interactions in the CSR format
std::vector<int> rowptr (npos + 1);
std::vector<int> colidx;
colidx.reserve(nnz);
pair_list->GetPairs(&rowptr[0], &colidx[0]);

// Destroy the PairListR object
delete pair_list;
```