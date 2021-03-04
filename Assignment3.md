# Assembling Matrices in 2D Problems
Again, let's visualize the **sparse** coefficient matrix first.
![sparse](https://user-images.githubusercontent.com/12702149/109998150-f29ea800-7cde-11eb-8401-53665a539a6f.png)
I only picked a 8x8 grid, and now we have a 64x64 sparse matrix. Obviously this matrix is not tri-diagonal like assignment 2 and you can't assemble it manually. Therefore you have to write some code to automatically do this job for you. Hard coding (writing numbers explicitly in your functions) is NOT recommended, since you may have to make changes when debugging.

## Define mesh
All below steps are demonstrated on a 8x8 grid(N=5). **You'll need much finer mesh in your homework**.

First we need to let computer know how does our mesh look like.
```
    -1     1     1     1     1     1     1    -1
     3     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     4
    -1     2     2     2     2     2     2    -1
```
You can create a 8x8 matrix for meshing. In my example I'm defining -1: empty nodes, 0:= internal nodes, 1:= X=0 ghost nodes, 2:= X=1 ghost nodes, 3:= Y=0 ghost nodes, 4:= Y=1 ghost nodes.
The reason to use ghost nodes, is to avoid conflict at four corner nodes. For example, without a ghost node, you can not make <img src="https://render.githubusercontent.com/render/math?math=\theta(1,1)"> satisfy both <img src="https://render.githubusercontent.com/render/math?math=\theta(1,1)=1"> and <img src="https://render.githubusercontent.com/render/math?math=\frac{d}{dx}\theta(1,1)=0">. With two extra neighbour ghost nodes, <img src="https://render.githubusercontent.com/render/math?math=\theta(1,1)"> can satisfy the heat equation as well as two boundary conditions at the same time.

## Anonymous Function
In order to assemble the coefficient matrix, the meshing matrix needs to be flattened. We need to know the **mapping** between the 8x8 matrix and the 1x64 array.

For convenience we're creating an [Anonymous Function](https://www.mathworks.com/help/matlab/matlab_prog/anonymous-functions.html)
```MATLAB
c = @(i,j) i+(N+3)*(j-1);
```
Or in Python, [Lambda Expression](https://docs.python.org/3/reference/expressions.html?highlight=lambda%20expression#lambda)
```Python
c=lambda i,j:i+(N+3)*j
```
Given the i,j coordinate in the 8x8 matrix (N=5), c(i,j) returns the corresponding index in the 1x64 array.

## Assembling Matrix
Then we can start assembling the sparse coefficient matrix.

For example in MATLAB:
```MATLAB
A=sparse((N+3)^2); % Initialize sparse matrix
B=zeros((N+3)^2,1);
k=0; % Row number in the coefficient matrix)
for i=1:N+3
    for j=1:N+3 % Scan through all nodes
        k=k+1; % Next row
        switch boundary(i,j) % Return the type of the current node as we defined
            case -1 % Empty node
                A(k,c(i,j))=1;
                B(k)=0; % Assign zero
            case 0 % Internal node
                A(k,c(i,j))=...; % Current node
                A(k,c(i-1,j))=...; % Find its left node
                A(k,c(i+1,j))=...; % Find its right node
                A(k,c(i,j-1))=...; % Find its bottom node
                A(k,c(i,j+1))=...; % Find its top node
            case 1 % X=0 ghost node
                A(k,c(i+1,j))=...; % Assign value directly to its neighbour
                B(k)=...;
            case 2 % X=1 ghost node
                ... % Use central difference instead of backward
            case 3 % Y=0 ghost node
                ... % Use central difference instead of forward
            case 4 % Y=1 ghost node
                ... % Use central difference instead of backward
        end
    end
end
x=A\B; % Solve linear system
sol=reshape(x,N+3,N+3); % Restore solution from array to matrix
numerical=sol(2:N+2,2:N+2); % Cut out ghost nodes
```
Python doesn't have `case-switch` structure so you need to use `dictionary`.
```Python
A=lil_matrix(((N+3)**2,(N+3)**2)) # Initialize sparse matrix
b=np.zeros((N+3)**2)
def empty(): # Empty node
    A[k,c(i,j)]=1.0
    b[k]=0.0 # Assign zero
def internal(): # Internal
    A[k,c(i,j)]=... # Current node
    A[k,c(i-1,j)]=... # Find its left node
    A[k,c(i+1,j)]=... # Find its right node
    A[k,c(i,j-1)]=... # Find its bottom node
    A[k,c(i,j+1)]=... # Find its top node
def left(): # X=0 ghost node
    A[k,c(i+1,j)]=... # Assign value directly to its neighbour
    b[k]=...
def right(): # X=1 ghost node
    ... # Use central difference instead of backward
def bottom(): # Y=0 ghost node
    ... # Use central difference instead of forward
def top(): # Y=1 ghost node
    ... # Use central difference instead of backward
point_dict = {
    -1: empty,
    0: internal,
    1: left,
    2: right,
    3: bottom,
    4: top}
k=0 # Row number in the coefficient matrix)
for i in np.arange(N+3): 
    for j in np.arange(N+3): # Scan through all nodes
        point_dict.get(boundary[i,j])() # Return the type of the current node as we defined
        k+=1 # Next row
x=spsolve(A,b) # Solve linear system
sol=x.reshape((N+3,N+3)) # Restore solution from array to matrix
numerical=sol[1:N+2,1:N+2] # Cut out ghost nodes
``` 
