### An example on using matrix operation to replace for loops

Recently, I ran into a case where I was given a matrix $$A$$ of size $$m \times n$$ and another matrix `$B$` with the same size.

The objective here is to update matrix `$B$` where the `$i$`-th column of `$B$`

```math
b_i = P_i b_i \quad \text{where} \quad P_i = I - \frac{1}{m} a_i a_i^H
```

what this is actually doing is to project `$b_i$` (or each column of `$B$`) on to a subspace orthogonal to `$a_i$` (its corresponding column in `$A$`)

**(Note: Since I'm dealing with complex numbers, I use `$H$` to represent conjugate transpose, which is equivalent to `$T$` (transpose) in real domain. The same applies in the code below.)**

This can be easily coded with a naive **for** loop.

```python
# say A and B and both numpy arrays
# import numpy as np

for i in range(A.shape[1]):
    P = np.outer(A[:, i], np.conj(A[:, i])) / A.shape[0]
    B[:, i] -= P.dot(B[:, i])
```

This approach becomes slower when `$n$` is large, because the naive **for** loop is not optimized.

You can probably try to use **multiprocessor** to process this, but I take advantage of the **matmul** function in **numpy**. [Here](https://docs.scipy.org/doc/numpy/reference/generated/numpy.matmul.html) is the detailed domumentation for this function. The feature that I take advantage of is it's ability to compute two ndarrays of dimension greater than 2 (3 in my case). According to the documentation, when calling this function with two ndarrays of dimension 3, the function would treat both ndarrays as a stack of 2d matrices, where the last two indices specify the matrices, and perform matrix multiplication per stack(layer).

As an example,

```math
matmul((200, 20, 90), (200, 90, 70)) = (200, 20, 70)
```
where `$(200, 20, 90)$` represents a matrix of size `$200 \times 20 \times 90$`.

I believe most people already know where this is going. In our case, we can simply "reshape" (not exactly reshape, but to add one dimension) our matrices `$A$` and `$B$`.

Let's "reshape" both `$A$` and `$B$` to make them of size `$n \times m \times 1$`. This can be easily implemented using the following code:

```python
A1 = np.expand_dims(A.T, axis=2)
B = np.expand_dims(B.T, axis=2)
# after the expand_dims, A becomes 1*m*n, then the transpose
# make it n*m*1
# Transpose in here swap the 0th and 2nd index
```

To compute outer product, we need another matrix from `$A$`, let's build another ndarray `$A2$` from `$A$` of size `$n \times 1 \times m$`

```python
A2 = np.expand_dims(A.T, axis=1)
```

The above **for** loop can be easily implemented as:

```python
P = np.matmul(A1, np.conj(A2)) / A.shape[0]
B -= np.matmul(P, B)
# up till now, B is of size n*m*1
B = B[:,:,0].T  # resulting in a matrix of size m*n
```

To get rid of all these unnecessary variables, we can simply compress it as the following:

```python
P = np.matmul(np.expand_dims(A.T, axis=2), \
              np.conj(np.expand_dims(A.T, axis=1))) / A.shape[0]
B = np.expand_dims(B.T, axis=2)
B -= np.matmul(P, B)
# up till now, B is of size n*m*1
B = B[:,:,0].T
```

A simple experiment demonstrates the improvement.

```python
import numpy as np
import time
m, n = 10, 100

A = np.random.randn(m, n) + 1j* np.random.randn(m, n)
B = np.random.randn(m, n) + 1j* np.random.randn(m, n)
print('Shape of A: ', A.shape)
print('Shape of B: ', B.shape)

B1 = B
start = time.time()
for i in range(A.shape[1]):
    P = np.outer(A[:, i], np.conj(A[:, i])) / A.shape[0]
    B1[:, i] -= P.dot(B1[:, i])
end = time.time()
print('Running time of for loop is: ', end - start, 's')

start = time.time()
B2 = B
P = np.matmul(np.expand_dims(A.T, axis=2), \
              np.conj(np.expand_dims(A.T, axis=1))) / A.shape[0]
B2 = np.expand_dims(B2.T, axis=2)
B2 -= np.matmul(P, B2)
# up till now, B is of size n*m*1
B2 = B2[:,:,0].T
end = time.time()
print('Running time of matrix operation is: ', end - start, 's')

# Check if B1 == B2
if sum(sum(B1-B2)) != 0:
    print('B1, B2 are not the same!')
else:
    print('B1, B2 are the same!')
    
# prints:
# Shape of A:  (10, 100)
# Shape of B:  (10, 100)
# Running time of for loop is:  0.013581037521362305 s
# Running time of matrix operation is:  0.0006909370422363281 s
# B1, B2 are the same!
```

However, matrix operation is not always the winner. If we compare the following results, we notice that it is very sensitive to `$m$`. if `$m$` is small, `$n$` can be relatively large, while matrix operation still offers huge improvement. However, when `$m$` gets larger, the matrix operation slows down and eventually becomes slower than naive for loop.

```python
# Shape of A:  (10, 10000)
# Shape of B:  (10, 10000)
# Running time of for loop is:  0.16220903396606445 s
# Running time of matrix operation is:  0.04723477363586426 s
# B1, B2 are the same!

# Shape of A:  (50, 100)
# Shape of B:  (50, 100)
# Running time of for loop is:  0.017223834991455078 s
# Running time of matrix operation is:  0.007061004638671875 s
# B1, B2 are the same!

# Shape of A:  (150, 100)
# Shape of B:  (150, 100)
# Running time of for loop is:  0.03846406936645508 s
# Running time of matrix operation is:  0.08285021781921387 s
# B1, B2 are the same!

# Shape of A:  (50, 1000)
# Shape of B:  (50, 1000)
# Running time of for loop is:  0.055570363998413086 s
# Running time of matrix operation is:  0.15408706665039062 s
# B1, B2 are the same!
```
    
To figure out why, I make the following changes to the code:

```python
import numpy as np
import time
m, n = 50, 1000

A = np.random.randn(m, n) + 1j* np.random.randn(m, n)
B = np.random.randn(m, n) + 1j* np.random.randn(m, n)
print('Shape of A: ', A.shape)
print('Shape of B: ', B.shape)

B1 = B
start = time.time()
for i in range(A.shape[1]):
    P = np.outer(A[:, i], np.conj(A[:, i])) / A.shape[0]
    B1[:, i] -= P.dot(B1[:, i])
end = time.time()
print('Running time of for loop is: ', end - start, 's')

start = time.time()
A_transpose = A.T
B_transpose = B.T
end = time.time()
print('Running time of matrix transpose is: ', end-start, 's')

start = time.time()
C = np.expand_dims(A_transpose, axis=2)
D = np.conj(np.expand_dims(A_transpose, axis=1))
B2 = np.expand_dims(B_transpose, axis=2)
end = time.time()
print('Running time of matrix reshape is: ', end-start, 's')

start = time.time()
P = np.matmul(C, D) / A.shape[0]

B2 -= np.matmul(P, B2)
# up till now, B is of size n*m*1
B2 = B2[:,:,0].T
end = time.time()
print('Running time of matrix operation is: ', end - start, 's')


# prints:
# Shape of A:  (50, 1000)
# Shape of B:  (50, 1000)
# Running time of for loop is:  0.05406689643859863 s
# Running time of matrix transpose is:  8.296966552734375e-05 s
# Running time of matrix reshape is:  0.0004200935363769531 s
# Running time of matrix operation is:  0.07828092575073242 s

```

This basically provides the explanation: It's the **np.matmul()** that slows down the matrix operation.




