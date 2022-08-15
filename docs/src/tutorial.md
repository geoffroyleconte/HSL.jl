# Tutorial

HSL MA57 and MA97 can be used for the solution of symmetric, possibly indefinite, linear systems.
They are often used for the solution of saddle-point systems, i.e., systems of the form
```math
\begin{bmatrix}
H & A^T \\
A &
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
=
\begin{bmatrix}
b \\ c
\end{bmatrix}.
```
Special cases occur when ``H`` is the identity matrix and either ``b = 0`` or ``c = 0``, which correspond to the least-squares problem
```math
\min_y \ \|A^T y - b\|
```
and to the least-norm problem
```math
\min_x \ \|x\| \ \text{subject to } Ax = c
```
respectively.

## HSL_MA97

Supported versions:
* 2.6.0
* 2.7.0

[HSL_MA97](http://www.hsl.rl.ac.uk/catalogue/hsl_ma97.html): an OpenMP-based direct solver for symmetric linear systems. Example:

```JULIA
using MatrixMarket
using HSL

K = MatrixMarket.mmread("K.mtx")  # only the lower triangle
rhs = readdlm("rhs.rhs")

LBL = Ma97(K)
ma97_factorize!(LBL)
x = ma97_solve(LBL, rhs)  # or x = LBL \ rhs
```

### Rectangular Systems

There is a convenience interface to solve rectangular systems that complements
the sparse QR factorization in Julia.

When *A* is *m*-by-*n* with *m* < *n* and has full row rank,
```JULIA
(x, y) = ma97_solve(A, b)
```
solves for the minimum-norm solution, i.e., *x* such that *Ax = b* and *x +
Aᵀ y = 0*. The call
```JULIA
(x, y) = ma97_min_norm(A, b)
```
is also defined, and is equivalent to the above.

When *m* > *n* and has full column rank,
```JULIA
(r, x) = ma97_solve(A, b)
```
solves for the least-squares solution, i.e., *x* such that *r = b - Ax*
satisfies *Aᵀ r = 0*. The call
```JULIA
(r, x) = ma97_least_squares(A, b)
```
is also defined, and is equivalent to the above.

## HSL_MA57

[HSL_MA57](http://www.hsl.rl.ac.uk/specs/ma57.pdf) version 5.2.0:
a sparse, multifrontal solver for symmetric linear systems. Example:

```JULIA
using MatrixMarket
using HSL

K = MatrixMarket.mmread("examples/K_0.mtx")  # only the lower triangle
rhs = readdlm("examples/rhs_0.rhs")
rhss = hcat(rhs, rhs)

## factorize and solve
LDL = Ma57(K)
ma57_factorize(LDL)
LDL.info.rank
x = ma57_solve(LDL, rhs)  # or x = LBL \ rhs
norm(K*x - rhs)
xx = ma57_solve(LDL, rhss)  # or x = LBL \ rhss
```

### Rectangular Systems

There is a convenience interface to solve rectangular systems that complements
the sparse QR factorization in Julia.

When *A* is *m*-by-*n* with *m* < *n* and has full row rank,
```JULIA
(x, y) = ma57_solve(A, b)
```
solves for the minimum-norm solution, i.e., *x* such that *Ax = b* and *x +
Aᵀ y = 0*. The call
```JULIA
(x, y) = ma57_min_norm(A, b)
```
is also defined, and is equivalent to the above.

When *m* > *n* and has full column rank,
```JULIA
(r, x) = ma57_solve(A, b)
```
solves for the least-squares solution, i.e., *x* such that *r = b - Ax*
satisfies *Aᵀ r = 0*. The call
```JULIA
(r, x) = ma57_least_squares(A, b)
```
is also defined, and is equivalent to the above. Example:


```JULIA
using MatrixMarket
using HSL

K = MatrixMarket.mmread("examples/K_0.mtx")  # only the lower triangle
rhs = readdlm("examples/rhs_0.rhs")

## solve min norm
K_mn = K[1:200,:]
x_mn, y_mn = ma57_min_norm(K_mn, rhs[1:200]) # == ma57_solve(K_mn, rhs[1:200])

## solve least squares
K_ls = K[:,1:200]
r_ls, x_ls = ma57_least_squares(K_ls, rhs)   # == ma57_solve(K_ls, rhs)
```