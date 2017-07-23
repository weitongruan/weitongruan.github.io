## Machine Learning Cheatsheet - All about the basics

When I was interviewing for machine learning related jobs, I was always asked about lots of fundamental/basic machine learning algorithms/concepts/cost functions/assumptions. 

Sometimes if you don't look at them everyday, the memory about them becomes relatively vague (at least to me). I decided to write this post with the hope that it keeps the most important and useful fundamental machine learning material.

Table of contents:

- [Linear Regression](#linear_regression)

Let's start with most fundamental task/algorithm, Linear Regression, which serves as the foundation or building block of lots of advanced machine learning algorithms (including **neural networks** and **deep learning**)

### Linear Regression
<a name='linear_regression'></a>

#### Model:

The idea is simple, we fit the data by using a model:

$$
y = Xw + b
$$

where $$y$$ is a vector of continuous values that you are interested in, $$X$$ is a design matrix where each row corresponds to all the features from one sample, $$w$$ is a weight vector and $$b$$ is a intercept or a constant offset.

This model assumes that for every sample $$x_i$$ ($$i$$-th row in $$X$$, reshaped as a column vector), its corresponding value (something value that you are interested in) can be represented as 

$$
y_i = w^Tx_i + b.
$$

#### Parameters:

**The main task in machine learning, no matter which algorithm you are using, is to estimate the parameters that controls this model.**

In linear regression, the parameters are $$w$$ and $$b$$. Here is a little trick that we can use to simplify the notation. We can always stack $$b$$ on the top of $$w$$, forming a new weight vector $$\theta = \begin{bmatrix} b \\ w \end{bmatrix}$$ and adding a column filled with 1 to the left of data matrix $$X = \begin{bmatrix} \mathbf{1} & X \end{bmatrix}$$. With this notation, the parameters are just a vector $$\theta$$.

#### Cost function:

In regression, the most common cost function is the **least squared error**, which computes the L2 distance (Euclidean norm) between the real value and the estimated value. In our case,

$$
L(\theta) = \left( y - X \theta \right)^T \left( y - X \theta \right)
$$

Without this matrix notation, the cost function can also be represented as the sum of squared errors from all samples.

$$
L(\theta) = \sum_{i=1}^{n} (y_i - \theta^T x_i)^2 = \sum_{i=1}^{n} (y_i - w^T x_i - b)^2
$$

where $$n$$ stands for the number of samples in the dataset.

#### Parameter estimation algorithm:

Because the cost function is convex, there is a global optimal solution to minimize the above cost function.

$$
\theta = \left( X^T X \right)^{-1} X^T y
$$

---





