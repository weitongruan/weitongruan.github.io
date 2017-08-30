### Reinforcement learning

Reinforcement learning (RL) is thought of as the next gereration of AI algorithms, which solves problems involving an agent interacting with an environment and the best decision-making criterion is to maximize the rewards received from the enviroment.

(Reference: [Standford cs231n lecture note](http://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture14.pdf))

- [Markov Decision Process](#markov_decision_process)



#### Markov Decision Process 
<a name='markov_decision_process'></a>

##### Setup:
- It provides a mathematical formulation of RL problem.
- It relies on the markov property (current state only depends on previous state)
- A MDP is characterized by a set of variables $$( \mathcal{S}, \mathcal{A}, \mathcal{R}, \mathbb{P}, \gamma )$$
    - $$\mathcal{S}$$ is the set of states 
    - $$\mathcal{A}$$ is the set of actions
    - $$\mathcal{R}$$ is the distribution of reward given (state, action) pair 
    - $$\mathbb{P}$$ is the transition probability (distribution over next state given (state, action) pair)
    - $$\gamma$$ is a discount factor for the reward

##### Procedure:

- at time step $$t=0$$, environment samples initial state $$s_0 \sim p(s_0)$$
- Then from $$t=0$$ until done:
    - Agent selects action $$a_t$$ from a policy $$\pi$$ (a function from $$\mathcal{S}$$ to $$\mathcal{A}$$ that specifies what action to take in each state)
    - Environment samples reward $$r_t \sim \mathcal{R}(s_t, a_t)$$
    - Environment samples next state $$s_{t+1} \sim \mathbb{P}(s_t, a_t)$$
    - agent receives reward $$r_t$$ and next state $$s_{t+1}$$

##### Objective: 

Find policy $$\pi^*$$ that maximizes cumulative discounted reward $$\sum_{t \geq 0} \gamma^t r_t$$.








