## Machine Learning Cheatsheet - All about the basics

When I was interviewing for machine learning related jobs, I was always asked about lots of fundamental/basic machine learning algorithms/concepts/cost functions/assumptions. 

Sometimes if you don't look at them everyday, the memory about them becomes relatively vague (at least to me). I decided to write this post with the hope that it keeps the most important and useful fundamental machine learning material.

Table of contents:

- [Linear Regression](#linear_regression)
- [Logistic Regression](#logistic_regression)

Let's start with most fundamental task/algorithm, Linear Regression, which serves as the foundation or building block of lots of advanced machine learning algorithms (including **neural networks** and **deep learning**)


<a name='linear_regression'></a>
### Linear Regression

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

#### Extras I:

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


<a name='logistic_regression'></a>
### Logistic Regression

In previous section, we talked about regression and the easiest algorithm [linear regression](#linear_regression). In this part, we are going to introduce the classification problem, another fundamental problem in machine learning. Compeard with regression problem where the target is continuous, in classification problems, the target is dicrete (i.e. class labels).

The name of logistic regression is a little interesting because it is indeed a classification algorithm. However, it comes with the name logistic regression because it has a very close relationship with linear regression and logit function.

Logistic regression deals with the binary classification problem where the target is  from a set of two class labels $$\{0, 1\}$$. Starting from the previous [linear regression](#linear_regression), where the targets are from $$- \infty$$ to $$+ \infty$$, people start to ask if there is a non-linear function that maps $$[ - \infty, + \infty ]$$ to $$[0, 1]$$. The most widely used in the old days (10 or 20 years ago) is the logitistic/sigmoid (inverse of logit function) function, where 

$$
logistic(x) = g(x) = \frac{e^x}{1+e^x} = \frac{1}{1+e^{-x}}
$$

$$
\lim_{x \to - \infty} g(x) = 0 \qquad \lim_{x \to + \infty} g(x) = 1 \qquad g(0) = \frac{1}{2}
$$

The plot of the sigmoid function can be found in [here](https://en.wikipedia.org/wiki/Sigmoid_function).

#### Model:

We first define the the probability of being either $$1 \text{ or } 0$$,

$$
P(y=1 \lvert x; w) = g_w \left( x \right) = g(w^Tx + b)

P(y=0 \lvert x; w) = 1 - g_w \left( x \right).
$$

Then with an independence model, the probability of an instance can be written as:

$$
P(y \lvert x; w) = \left( P(y=1 \lvert x; w) \right)^{y} \left( 1 - P(y=1 \lvert x; w) \right) ^{1-y} 

= \left( g_w \left( x \right) \right)^{y} \left( 1 - g_w \left( x \right) \right) ^{1-y}
$$

#### Parameters:

The parameters needs to be estimated from the training data are the same as those in [linear regression](#linear_regression).

#### Cost function

As with the [linear regression](#linear_regression), the cost function is from the ***negative log likelihood***, which has the form:

$$
L(\theta) = - \sum_{i=1}^{n} y_i \log g_{\theta}(x_i) + \left( 1 - y_i \right) \log \left( 1- g_{\theta}(x_i) \right) 
$$

#### Parameter estimation algorithm

The update equation is for the `$j$`-th element of parameter $$\theta$$ is

$$
\theta_j = \theta_j + \alpha \left( y_i - g_{\theta}(x_i) \right) x_{i,j}
$$

where $$\alpha$$ is called the learning rate or step size. (Note, it has the same form as in linear regression except the form of $$g_{\theta}(x)$$.)


#### Extra I:

Logistic regression can be easily extended to dealing with multiple classes, resulting in a model called ***Softmax Regression***. The revised cost function has the form:

$$
L(\theta) = - \sum_{i=1}^{n} \log \prod_{l=1}^{k} \left( \frac{\exp \left( \theta_{l}^T x_i \right)}{\sum_{j=1}^{k} \exp \left( \theta_{k}^T x_i \right)} \right)^{1 \{y_i = l \}}
$$

Here we have `$k$` classes in total, each $$\theta_l$$ is optimized for the $$l$$-th class and $${1 \{y_i = l \}}$$ stands for a indicator random variable where it generates $$1$$ only when $$y_i = l$$ and $$0$$ otherwise.

Softmax is commonly used as the final layer to generate class labels in Neural Nets/ Deep Learning, most likely due to its probabilistic interpretation.


#### Extra II:

Another "similar" algorithm is called the Perceptron learning algorithm. Compared with Logistic function, where the ouput is conitnuous in the range $$[-1, 1]$$, the non-linearity function used in perceptron is piece-wise constant.

$$
g(x) = \begin{cases} 1 & x \geq 0 \\ 0 & x < 0 \end{cases}
$$

