### Broadcasting in Numpy

In the previous [post](https://weitongruan.github.io/python_related/an_example_on_using_matrix_operation_to_replace_for_loops), I mentioned using matrix operation to replace (for) looping. Today, I've prepared something more general and I hope this can help readers design faster codes.

I know this notion of "[broadcasting](https://docs.scipy.org/doc/numpy/user/basics.broadcasting.html#general-broadcasting-rules)" for a while but I didn't realize it is this powerful until today. A good documentation on broadcast is [here](http://scipy.github.io/old-wiki/pages/EricsBroadcastingDoc). The best part of broadcasting is that it can be used on 2 ndarrays with different sizes. For example, we have two vectors

```python
a, b = np.array([1, 2, 3, 4]), np.array([5, 6, 7, 8, 9])
```

and we want to generate/calculate a matrix $$M$$ such that $$M_{i,j} = a[i] + b[j]$$, here $$+$$ can be replace with $$-, *, /$$.

There are lot of ways to generate this $$M$$, for example, we can use **two for loops** and follow the formula above. Another approach can be to **create two matrices** $$M_1$$ and $$M_2$$

```python
M1 = np.array([[1, 1, 1, 1, 1], [2, 2, 2, 2, 2], [3, 3, 3, 3, 3], [4, 4, 4, 4, 4]])
M2 = np.array([5, 6, 7, 8, 9], [5, 6, 7, 8, 9], [5, 6, 7, 8, 9], [5, 6, 7, 8, 9], [5, 6, 7, 8, 9])
```

$$
M_1 = \begin{bmatrix} 1 & 1 & 1 & 1 & 1\\ 2 & 2 & 2 & 2 & 2\\ 3 & 3 & 3 & 3  & 3\\ 4 & 4 & 4 & 4 & 4 \end{bmatrix} \qquad M_2 = \begin{bmatrix} 5 & 6 & 7 & 8 & 9 \\ 5 & 6 & 7 & 8 & 9 \\ 5 & 6 & 7 & 8 & 9 \\ 5 & 6 & 7 & 8 & 9 \end{bmatrix}

$$

and add them together element-wise.


```python
M = M1 + M2
```
**But, how to create these two matrices?** A efficient implementation is to use the **np.broadcast_to()**.

```python
M1 = np.broadcast_to(a[:, np.newaxis], (4, 5))
M2 = np.broadcast_to(b[np.newaxis, :], (4, 5))
M = M1 + M2
```
----
#### Magic is here!
**With boradcasting, these two matrices do not have to be generated explicitly.** All we need to do is just this one-liner.

```python
M = a[:, np.newaxis] + b[np.newaxis, :]
```
----
The broadcasting rule for add/minus/product ndarrays element-wise is: if they are of the same shape, keep the original shape; if they are not of the same shape, for all those axes that differ have to be 1 from either two.

I know it's hard to understant, in our previous example, `a[:, np.newaxis]` creates an implicit matrix $$A$$ of size $$4 \times 1$$, and the same for $$B$$ of size $$1 \times 5$$. Since the shape of$$A$$ and $$B$$ differ in both axes, when we want to add them element-wise, in terms of the first axis, $$B$$ has shape 1 and in terms of the second axis, $$A$$ is of shape 1, so broadcasting rule satisfies here. 

#### Note:
**Broadcasting provides clean code and faster implementation, however, avoid using it when the size of the ndarray gets large, e.g. 10000 * 100 * 20. It will take too much memory for storing the large ndarray and slow down the algorithm.** This explains our observation in previous [post](https://weitongruan.github.io/python_related/an_example_on_using_matrix_operation_to_replace_for_loops).

I performed a few simple tests. Here is a nearest neighbor classification algorithm where I compare both two implementations.

```python
# A toy example
import numpy as np
obs = np.random.randn(4000, 40)  # observations 4000-samples 40-features
codes = np.random.randn(400, 40) # reference for each known class 400-classes

def classify(ob, codes):  # classification algorithm 
    return np.argmin(((codes - ob) ** 2).sum(axis=1))
    
import time
start = time.time()  # start time for loop implementation
for i in range(500):  # repeat 500 times
    [classify(obs[j], codes) for j in range(obs.shape[0])]
end = time.time()-start
print(end)
start = time.time()  # start time for broadcasting implementation
for i in range(500):
    np.argmin(((obs[:, np.newaxis, :] - codes[np.newaxis, :, :]) ** 2).sum(axis=2))
end = time.time()-start
print(end)

# running time is 85.83619689941406s vs 303.67309284210205s
# here in broadcasting, it creates a huge 4000 * 400 * 40 ndarray.
# If we have a smaller ndarray (500 * 20 * 20), broadcasting is faster
# In this case where we have a huge number of samples, it is more wise
# to use for loop along this axis and use broadcasting to create ndarray
# of size 400 * 40 only.
```



#### Extra:
`np.newaxis` is used to expand the dimension of ndarray by 1 at the specified axis.
