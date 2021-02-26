# Sparse Matrix
You may have heard sparse matrices in large scale problems. But is it far away from you? No.

When solving numerical PDEs, sparse matrices are much [better](https://www.mathworks.com/help/matlab/math/computational-advantages-of-sparse-matrices.html).

## Visualization
Let's visualize the coefficient matrix (47x47):
![sparsity](https://user-images.githubusercontent.com/12702149/109365270-c7d7ce00-785e-11eb-8a3b-0eea5ae73d61.png)
It's almost tri-diagonal, except for boundaries & interfaces. The density is 143/47/47=6.5% which means 93.5% of the RAM is wasted. You can easily imagine that the total number of elements increases quadratically but the number of non-zero elements only increases linearly.

## Initialization
The usual way to initialize a "dense" matrix in MATLAB is
```MATLAB
A = zeros(N,N);
B = zeros(N,1);
% Assembling Matrix
x = A\B;
```
or in Python
```Python
import numpy as np
A = np.zeros((N, N))
b = np.zeros(N)
# Assembling Matrix
x = np.linalg.solve(A, B)
```
To smartly use sparse matrix, you only need to do some changes in MATLAB
```MATLAB
A = sparse(N,N);
B = zeros(N,1);
% Assembling Matrix
x = A\B;
```
or in Python
```Python
import numpy as np
from scipy.sparse import lil_matrix
from scipy.sparse.linalg import spsolve
A = lil_matrix((N, N))
b = np.zeros(N)
# Assembling Matrix
x = spsolve(A, B)
```
You don't have to change anything when assembling the matrix.

## Benchmark
Let's test the time consumed (in seconds) to solve the linear system.
| Mesh Size | MATLAB Dense | MATLAB Sparse | Python Dense | Python Sparse|
| --------- | ------------ | ------------- | ------------ | ------------ |
| 90 | 0.012 | 0.012 | 0.002 | 0.008 |
|	900 | 0.046 | 0.021 | 0.043 | 0.050 |
|	9000 | 12.294 | 0.331 | 11.055 | 0.513 |

As mesh size increases, sparse matrices are getting significantly faster than dense matrices.
