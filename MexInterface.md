StokesDT provides the following functions via the Matlab interface.

### Computing the Ewald summations ###
```
>> help compute_ewald
  [mat] = compute_ewald(pos, rdi, L, tol, mode, ...)

  INPUT
    pos     the array of particle coordinates
    rdi     the array of particle radii
    L       the simulation box size
    tol     the requested tolerance of Ewald errors
    mode    'full'(full matrix), 'real'(real-space parts)
            or 'recip'(reciprocalspace parts)
    xi      the Ewald parameter (optional);
            if not specified, the optimal xi will be
            automatically chosen

  OUTPUT
    mat     the array of the mobility matrix
```


### Computing SPME ###
```
>> help compute_spme
  [v] = compute_spme(pos, rdi, L, f, mode, xi, rmax, dim, porder)
  INPUT
    pos       the vector of particle coordinates
    rdi       the vector of particle radii
    L         the simulation box size
    f         the block of vectors of forces
    mode      'full'(real + recip), 'real'(real-space parts)
              or 'recip'(reciprocalspace parts)
    xi        the Ewald parameter
    rmax      the real-space cutoff
    dim       the dimension of FFT grid
    porder    the interpolation order
  OUTPUT
    v         the block of vectors of velocities
```


### Computing Short-range interactions ###
```
>> help compute_pairlist
  [indi, indj] = compute_pairlist(pos, rdi, L, cutoff, mode)
    Compute short-range interactions between all the particles.
    The particle pairs within the cutoff distance are stored in [indi, indj].
  INPUT
    pos     the array of particle coordinates
    rdi     the array of particle radii
    L       the simulation box size
    cutoff  the cutoff
    mode    'R' (absulote distance) or 'S' (normalized distance)

  OUTPUT
    indi      the vector of source particle identities
    indj      the vector of destination particle identities
```