# Assembling Matrix
Again, let's visualize the **sparse** coefficient matrix first.
![hw3](https://user-images.githubusercontent.com/12702149/109912279-b553ff00-7c79-11eb-9f62-3cf76cc9383a.png)
I only picked a 10x10 grid, and now we have a 100x100 sparse matrix. Obviously this matrix is not tri-diagonal like assignment 2 and you can't assemble this matrix manually. Therefore you have to write some code to automatically do this job for you. Hard coding (writing numbers explicitly in your functions) is NOT recommended, since you may have to make changes when debugging.

## Define mesh
All below steps are demonstrated on a 10x10 grid. **You'll need much finer mesh in your homework**.

First we need to let computer know how does our mesh look like.
```
     1     1     1     1     1     1     1     1     1     1
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     3     0     0     0     0     0     0     0     0     4
     2     2     2     2     2     2     2     2     2     2
```
You can create a 10x10 matrix for mesh. In my example I'm defining 0:= internal nodes, 1:= X=0 boundary, 2:= X=1 boundary, 3:= Y=0 boundary, 4:= Y=1 boundary.

## Anonymous Function
In order to assemble the coefficient matrix, the mesh matrix needs to be flattened. We need to know the **mapping** between the 10x10 matrix and the 1x100 array.

For convenience we're creating an [Anonymous Function](https://www.mathworks.com/help/matlab/matlab_prog/anonymous-functions.html)
```MATLAB
c = @(i,j) i+(N+1)*(j-1);
```
Or in Python, [Lambda Expression](https://docs.python.org/3/reference/expressions.html?highlight=lambda%20expression#lambda)
```Python
c=lambda i,j:i+(N+1)*j
```
Given the i,j coordinate  in the 10x10 matrix (N=9), c(i,j) returns the corresponding index in the 1x100 array.

## Assembling Matrix
Then we can start assembling the sparse coefficient matrix.

For example in MATLAB:
```MATLAB
A=sparse((N+1)^2); % Initialize sparse matrix
B=zeros((N+1)^2,1);
k=0; % Index counter
for i=1:N+1
    for j=1:N+1 % Scan through all nodes
        k=k+1; % counter+1
        switch boundary(i,j) % Return the type of the current node as we defined before
            case 0 % If internal node
                A(k,c(i,j))=***; % Current node
                A(k,c(i-1,j))=***; % Find its left node
                A(k,c(i+1,j))=***; % Find its right node
                A(k,c(i,j-1))=***; % Find its bottom node
                A(k,c(i,j+1))=***; % Find its top node
                % Assign values from your finite difference equation
            case 1 % If X=0 boundary
                ...
            case 2 % If X=1 boundary
                ...
            case 3 % If Y=0 boundary
                ...
            case 4 % If Y=1 boundary
                ...
        end
    end
end
x=A\B; % Solve linear system
numerical_sol=reshape(x,N+1,N+1);# Restore solution from array to matrix
```
Python doesn't have `case-switch` structure so you need to use `dictionary`.
```Python
A=lil_matrix(((N+1)**2,(N+1)**2)) # Initialize sparse matrix
b=np.zeros((N+1)**2) 
def internal():
    A[k,c(i,j)]=*** # Current node
    A[k,c(i-1,j)]=*** # Find its left node
    A[k,c(i+1,j)]=*** # Find its right node
    A[k,c(i,j-1)]=*** # Find its bottom node
    A[k,c(i,j+1)]=*** # Find its top node
    # Assign values from your finite difference equation
def left():
    ...
def right():
    ...
def bottom():
    ...
def top():
    ...
point_dict = { # Define dictionary
    0: internal,
    1: left,
    2: right,
    3: bottom,
    4: top}
k=0 # Index counter
for i in np.arange(N+1):
    for j in np.arange(N+1): % Scan through all nodes
        point_dict.get(boundary[i,j])() % Return the type of the current node as we defined before
        k+=1 # counter+1
x=spsolve(A,b) # Solve linear system
numerical_sol=x.reshape((N+1,N+1)) # Restore solution from array to matrix
``` 
