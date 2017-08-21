## NLP Related Topics

Table of Contents:

- [Learning Word Embedding with Noise Contrast Estimation](#learning_word_embedding_with_noise_contrast_estimation)


<a name='learning_word_embedding_with_noise_contrast_estimation'></a>

### Learning Word Embedding with Noise Contrastive Estimation

Original paper can be found [here](http://papers.nips.cc/paper/5165-learning-word-embeddings-efficiently-with-noise-contrastive-estimation.pdf).


Learning word representation as opposed to assigning probabilities to sentences.

#### Neural probabilistic language models (NPLMs):

Given a context $$h$$, an NPLM defines the distribution for the word to be predicted using the scoring function $$s_\theta(w, h)$$ that quantifies the compatibility between the context and the candidate target word. Here $$\theta$$ are model parameters, which include the word embeddings. The scores are converted to probabilities by exponentiating and normalizing:

$$
P_\theta^h(w) = \frac{\exp(s_\theta(w,h))}{\sum_{w'}\exp(s_\theta(w',h))}
$$

Unfortunately both evaluating $$P_\theta^h(w)$$ and computing the corresponding likelihood gradient requires normalizing ***over the entire vocabulary***, which means that maximum likelihood training of such models ***takes time linear in the vocabulary size***, and thus is ***prohibitively expensive for all but the smallest vocabularies***.

There are ***two main approaches*** to ***scaling up NPLMs to large vocabularies***. The first one involves ***using a tree-structured vocabulary with words at the leaves***, resulting in ***training time logarithmic*** in the vocabulary size [15]. ***Unfortunately***, this approach is considerably more involved than ML training and finding well-performing trees is non-trivial [13]. The alternative is to ***keep the model but use a different training strategy***. Using ***importance sampling*** to approximate the likelihood gradient was the first such method to be proposed [2, 3], and though it could produce substantial speedups, ***it suffered from stability problems***. Recently, a method for ***training unnormalized probabilistic models***, called ***noise-contrastive estimation (NCE) [6]***, has been shown to be a stable and efficient way of training NPLMs [14]. As it is also considerably simpler than the tree-based prediction approach, we use NCE for training models in this paper. We will describe NCE in detail in Section 3.1.


#### Noise contrastive estimation

***Replacing normalized probabilities/models with un-normalized models*** for $$P_\theta^{h}(w)$$

$$
P^h(D=1 \lvert w, \theta) = \frac{P^h_\theta (w)}{P^h_\theta (w) + k P_n(w)} = \sigma(\Delta s_\theta (w, h)) 
$$

$$
\Delta s_\theta (w, h) = s_\theta (w, h) - \log(k P_n(w))
$$

$$
P^h_\theta (w) = \exp( s_\theta (w, h))
$$




