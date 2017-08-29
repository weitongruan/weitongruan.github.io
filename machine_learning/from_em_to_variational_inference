### From EM to Variational Inference

[Expectation Maximization](https://en.wikipedia.org/wiki/Expectation%E2%80%93maximization_algorithm) (EM) algorithm solves the Mamimum Likelihood Estimation (MLE) problem with latent variables (something that we don't observe). Its classic applications include Gaussian Mixture Model (GMM) and Hidden Markov Model (HMM).

#### Idea:
For a given instance, we observe its features $$x_i$$, however, its latent state(class) `$z_i$` is not known to us. 

In MLE, we want to solve this optimization problem

$$
\hat{\theta}_{MLE} = \arg \max_{\theta} p(x ; \theta) = \arg \max_{\theta} \log p(x; \theta)
$$

where $$\theta$$ is a set of parameters.

$$
\log p(x \lvert \theta) = \log \sum_{z} p(x,z ; \theta)
$$

This ML problem is hard to solve without knowning $$z$$.

By Jensen's inequality, a lower bound of the above expression can be written as

$$
\log \sum_{z} p(x,z ; \theta) = \log \sum_{z} \frac{p(x,z ; \theta)q(z)}{q(z)} \geq \sum_{z} q(z) \log \frac{p(x,z ; \theta)}{q(z)}
$$

we choose 

$$
q(z) = \frac{p(x,z ; \theta)}{\sum_{z} p(x,z ; \theta)} = p(z \lvert x; \theta)
$$

The algorithm work in two steps:

- E(expctation) step: 

$$
q(z) = p(z \vert x; \theta^{(t)})
$$

- M(aximization) step:

$$
\theta^{(t+1)} = \arg \max_{\theta} E_{q(z)} \left[ \log p(x,z; \theta) \right]
$$







