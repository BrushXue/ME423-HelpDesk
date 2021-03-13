# What is the definition of H?

To avoid confusion, we're normalizing H based on the y-dimensional length, which means <img src="https://render.githubusercontent.com/render/math?math=H=\frac{Mb}{k}">

If you use <img src="https://render.githubusercontent.com/render/math?math=H=\frac{Ma}{k}">, please adjust your solution accordingly.

This normalization applies to both question (a) and (b).

# How justify the removal of z-axis
You don't have to prove this part in your assignment. It's just for those who are curious.

## Physics explaination

Adiabatic B.C. is applied on both sides so heat transfer is not supposed to happen along z-axis.

## Mathematics explaination

The eigenfunction on z-axis (Neumann B.C. on two sides) is
<img src="https://render.githubusercontent.com/render/math?math=C_0=1,\ C_n=\sqrt{2}\cos(n\pi z)">.

Assume the y-eigenfuction is B(y), consider the following integral

<img src="https://render.githubusercontent.com/render/math?math=A_{ij}(0)=\int_0^1\int_0^1 f(y) B_i(y)C_j(z)dydz">

where f(y) is not related to z. We can take out z and get

<img src="https://render.githubusercontent.com/render/math?math=A_{ij}(0)=\int_0^1 f(y) B_i(y)dy\cdot \int_0^1\sqrt{2}\cos(j\pi z)dz=0,\ j\geq 1">

Therefore the only eigenfunction left is <img src="https://render.githubusercontent.com/render/math?math=C_0(z)=1">, the problem is reduced to two-dimensional.
