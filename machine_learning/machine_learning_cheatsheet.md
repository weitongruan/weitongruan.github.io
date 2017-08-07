## Machine Learning Cheatsheet - All about the basics

When I was interviewing for machine learning related jobs, I was always asked about lots of fundamental/basic machine learning algorithms/concepts/cost functions/assumptions. 

Sometimes if you don't look at them everyday, the memory about them becomes relatively vague (at least to me). I decided to write this post with the hope that it keeps the most important and useful fundamental machine learning material.

Table of contents:

- [Linear Regression](#linear_regression)
- [Logistic Regression](#logistic_regression)

Let's start with most fundamental task/algorithm, Linear Regression, which serves as the foundation or building block of lots of advanced machine learning algorithms (including **neural networks** and **deep learning**)

### Linear Regression
<a name='linear_regression'></a>

#### Model:

The idea is simple, we fit the data by using a model:

$$
y = Xw + b
$$

where $$y$$ is a vector of continuous values that you are interested in (target), $$X$$ is a design matrix where each row corresponds to all the features from one sample, $$w$$ is a weight vector and $$b$$ is a intercept or a constant offset.

This model assumes that for every sample $$x_i$$ ($$i$$-th row in $$X$$, reshaped as a column vector), its corresponding target can be represented as 

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

(Some of my thoughts: when the dataset is small, we are encouraged use this analytic solution directly, but when the dataset is large, we might need to refer to iterative approaches, such as (stochastic) gradient descent, I guess due to the inverse of the large matrix $$X^TX$$.)

#### Extras:

Linear regression has a nice probabilistic interpretation, where for each sample, the target can be represented as 

$$
y_i = \theta^T x_i + \epsilon_i
$$

where $$\epsilon_i$$ models either noise or part of the data that can not be explained by the model. With the assumption that the $$\epsilon_i$$ are IID Gaussian distributed and with the **maximum likelihood** that produces the cost function

$$
L(\theta) = - \log p(y | X ; \theta)
$$

(Note: here $$L$$ stands for **loss** instead of **likelihood**)

#### Extras II:

In the above, for each sample we have features $$x_i$$, just to remember here $$ features \ne inputs$$. For example, for a sample, we have its 2D coordinates $$(p, q)$$. Here $$inputs = (p, q)$$, however $$features = g(p, q)$$ where $$g$$ can be any function of $$(p, q)$$.


---

### Logistic Regression
<a name='logistic_regression'></a>

In previous section, we talked about regression and the easiest algorithm [linear regression](#linear_regression). In this part, we are going to introduce the classification problem, another fundamental problem in machine learning. Compeard with regression problem where the target is continuous, in classification problems, the target is dicrete (i.e. class labels).

The name of logistic regression is a little interesting because it is indeed a classification algorithm. However, it comes with the name logistic regression because it has a very close relationship with linear regression and logit function.

Logistic regression deals with the binary classification problem where the target is  from a set of two class labels `$\{0, 1\}$`. Starting from the previous [linear regression](#linear_regression), where the targets are from `$- \infty$` to `$+ \infty$`, people start to ask if there is a non-linear function that maps `$[ - \infty, + \infty ]$` to `$[0, 1]$`. The most widely used in the old days (10 or 20 years ago) is the logitistic/sigmoid (inverse of logit function) function, where 

$$
logistic(x) = g(x) = \frac{e^x}{1+e^x} = \frac{1}{1+e^{-x}}
$$

$$
\lim_{x \to - \infty} g(x) = 0 \qquad \lim_{x \to + \infty} g(x) = 1 \qquad g(0) = \frac{1}{2}
$$

The plot of the sigmoid function can be found in [here](#https://en.wikipedia.org/wiki/Sigmoid_function).



