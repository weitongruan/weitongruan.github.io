### Hidden Markov Model (HMM)

Given a sequence of ***observations*** $$O = o_1, o_2, \cdots, 0_t$$, find the correct ***hidden state sequence*** $$Q = q_1, q_2, \cdots, q_t$$.

Table of Contents:

- [Likelihood (Forward Algorithm)](#likelihood)
- [Decoding (Viterbi Algorithm)](#decoding)
- [Learning (Forward Backward Algorithm)](#learning)

#### Notetaions:

Notations | Explanations
---|---
$$Q = q_1, q_2, \cdots, q_N$$ | a set of N states
$$A = a_{11}, a_{12}, \cdots, a_{1n}, \cdots, a_{nn}$$ | transition probability matrix A
$$O = o_1, o_2, \cdots, o_T$$ | a sequence of T observations drawn from a vocabulary $$V = v_1, v_2, \cdots, v_V$$
$$B = b_i (o_t)$$ | emission probabilities from matrix B
$$q_0, q_F$$ | start state and end (final) state


#### Key assumptions:

- Markov: $$P(q_i \lvert q_1, \cdots, q_{i-1}) = P(q_i \lvert q_{i-1})$$
- independence: $$P(o_i \lvert q_1, \cdots, q_T, o_1, \cdots, o_T) = P(o_i \lvert q_i)$$

<a name='likelihood'></a>
#### Likelihood (Forward Algorithm)

The way likelihood is calculated is as follows:

$$
\begin{aligned}
P(O; A, B) &= \sum_{Q} P(O, Q; A, B) \\
&= \sum_{Q} P(O \lvert Q; A, B) P(Q; A) \\
&= \sum_{Q} \left[ \prod_{t=1}^{T} P(o_t \lvert q_t) \prod_{t=1}^{T} P(q_{t} \lvert q_{t-1}) \right]
\end{aligned}
$$

The key idea to forward algorithm is the following equation, which is used to design the ***DP*** algorithm:

Suppose $$\alpha_t(j) = P(o_1, o_2, \cdots, o_t, q_t = j \lvert A, B)$$:

$$
\alpha_{t+1}(j) = \sum_{i} \alpha_{t}(i) a_{ij} b_{j}(o_t)
$$

A detailed derivation is provided below:
$$
 
 P(o_1, \cdots, o_t) = \sum_{p_t} P(o_1, \cdots, o_t, p_t)
$$
$$
 \begin{aligned}
    P(o_1, \cdots, o_t, p_t) &= \sum_{p_{t-1}} P(o_1, \cdots, o_{t-1}, o_t, p_{t-1}, p_t) \\
    &= \sum_{p_{t-1}}P(o_t, p_t \lvert o_1, \cdots, o_{t-1}, p_{t-1}) P(o_1, \cdots, o_{t-1}, p_{t-1}) \\
    &= \sum_{p_{t-1}} P(o_t \lvert o_1, \cdots, o_{t-1}, p_{t-1}, p_{t}) P(p_t \lvert o_1, \cdots, o_{t-1}, p_{t-1} ) P(o_1, \cdots, o_{t-1}, p_{t-1}) \\
    &= \sum_{p_{t-1}} P(o_t \lvert p_t) P(p_t \lvert p_{t-1}) P(o_1, \cdots, o_{t-1}, p_{t-1}) \\
 \end{aligned}
$$

***Algorithm:***

- Initialization:

$$
\alpha_1(j) = a_{oj} b_j(o_1) \qquad 1 \leq j \leq N 
$$

- Recursion

$$
\alpha_t(j) = \sum_i \alpha_{t-1}(i) a_{ij} b_j(o_t) \qquad 1 \leq i \leq N , 1 \leq t \leq T
$$

- Termination

$$
P(O \lvert A, B) = \sum_j \alpha_T(j) a_{iF}
$$

<a name='decoding'></a>
#### Decoding (Viterbi Algorithm)

Let's first define 

$$
v_t(j)= \max_{q_0, q_1, \cdots, q_{t-1}} P(o_1, \cdots, o_t, q_1, \cdots, q_{t-1}, q_t=j \lvert A, B)
$$

then the update formula for Viterbi has the form:

$$
v_t(j) = \max_{i} v_{t-1}(i) a_{ij} b_j(o_t)
$$

***Algorithm:***

We use $$v$$ to store a table of viterbi values and $$b$$ to store backpointers.

- Initialization

$$
\begin{aligned}
    v_1(j) &= a_{0j} b_j(o_1) \qquad 1 \leq j \leq N \\
    b_1(j) &= 0
\end{aligned}
$$

- Recursion

$$
1 \leq j \leq N, 1 \leq t \leq T
$$
$$
\begin{aligned}
    v_t(j) &= \max_i v_{t-1}(i) a_{ij} b_j(o_t) \\
    b_t(j) &= \arg \max_i v_{t-1}(i) a_{ij} b_j(o_t)
\end{aligned}
$$

- Termination

The best score:

$$
P(o_1, \cdots, o_T, o_F, q_1, \cdots, q_T) = v_t(F) = \max_i v_{T}(j) a_{jF}
$$

The start of backpointer:
$$
b_T(F) = \arg \max_i v_{T}(j) a_{jF}
$$

<a name='learning'></a>
#### Learning (Forward-Backward Algorithm)


