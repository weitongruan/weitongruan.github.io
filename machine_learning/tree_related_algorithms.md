## Trees related Algorithms

Table of Contents:

- [Desision Tree](#decision_tree)
- [Boosted Trees](#boosted_trees)
- 



<a name='decision_tree'></a>


<a name='boosted_trees'></a>
### Boosted Trees

#### Regression Tree and Ensemble

Reference: [Introduction to boosted trees](https://homes.cs.washington.edu/~tqchen/pdf/BoostedTree.pdf)

1. Regression tree(aka classification and regression tree)
    - Decision rule is the same as decision tree
    - Each leaf contains a score

2. Regression tree ensemble
    - total score is the sum of scores from all trees
    - benefits of tree ensembles:
        - Invariant to scaling of inputs, not necessary to do carful normalization
        - is able to learn higher order correlation between input and output (a complicated nonlinear function is decomposed into and ensemble of simple functions)
        - can be scalable and used in industry
    
    - Mathematical Model:
    
        Assume that we have $$K$$ trees:
        $$
        \hat{y}_i = \sum_{k=1}^{K} f_k \left( x_i \right), f_k \in \mathcal{F}
        $$
        here $$\mathcal{F}$$ stands for the space of functions containing all regression trees.
        
        ***Regression tree is a function that maps the attributes(inputs) to a score and is optimized to be an approximation to the true hidden mapping.***
        
        Parameters: $$f_1, \cdots,  f_K$$ or $$K$$ regression trees (structure and score in the leaves)
    
    - Single variable regression tree:
        - objective: learns a piece-wise constant function
        - Training loss: how will the function fit on training data?
        - Regularization: how to define complexity of this function?
            - number of splitting points, $$l_2$$ norm of the height in each segment.
    - Objective function:
    
        $$
        L = \sum_{i=1}^n l(y_i, \hat{y}_i) + \sum_{k=1}^{K} \Omega(f_k)
        $$
        
        The first term on the RHS is the training loss and the second is a regression term that meaures the complexity of the model(trees).
        
    - Versatility
    
        This model can be applied at either regression or classification or ranking tasks based on the how the score is defined and the resulting cost function.
        
        - Using squared error loss $$l(y_i, \hat{y}_i) = (y_i - \hat{y}_i)^2$$ results in a boosted machine
        - Using logistic loss $$l(y_i, \hat{y}_i) = y_i \log (1+ e^{-\hat{y}_i}) + (1-y_i) \log (1+e^{\hat{y}_i})$$ results in the LogitBoost
        
3. Gradient Boosting

    - Additive training (boosting)
        - start from constant prediction, add a new function each time.
        
        $$
        \begin{aligned}
        \hat{y}_i^{(0)} &= 0 \\
        \hat{y}_i^{(1)} &= f_1(x_i) = \hat{y}_i^{(0)} + f_1(x_i) \\
        \hat{y}_i^{(2)} &= f_1(x_i) + f_2(x_i) = \hat{y}_i^{(1)} + f_2(x) \\
        \cdots \\
        \hat{y}_i^{(t)} &= \sum_{k=1}^{t} f_k(x_i) = \hat{y}_i^{(t-1)} + f_t(x_i)
        \end{aligned}
        
        $$
    
    - At each iteration $$t$$, add a new function $$f_t$$.
        - how to find the best $$f_t$$? Minimize objective/cost
        - At iteration $$t$$, the prediction is $$\hat{y}_i^{(t)} = \hat{y}_i^{(t-1)} + f_t(x_i)$$ and the objective is: 
        
        $$
        \begin{aligned}
        L^{(t)} &= \sum_{i=1}^{n} l(y_i, \hat{y}_i^{t}) + \sum_{k=1}^{t} \Omega(f_k) \\
        &= \sum_{i=1}^{n} l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i)) + \Omega(f_t) + const
        \end{aligned}
        $$
        
        - Special case: Suppose we use square loss:
        
        $$
        \begin{aligned}
        L^{(t)} &= \sum_{i=1}^{n} \left( y_i - ( \hat{y}_i^{(t-1)} + f_t(x_i)) \right)^{2} + \Omega(f_t) + const \\
        &= \sum_{i=1}^{n} \left( 2* \left( \hat{y}_i^{(t-1)} - y_i \right) f_t(x_i) + f_t^{2}(x_i) \right)
        \end{aligned}
        $$
        where $$\hat{y}_i^{(t-1)} - y_i$$ is called the residual term from previous iteration.
        
        - For the more general case, use Taylor Expansion of the objective:
        
        Taylor expansion:
        
        $$
        f(x + \Delta x) \approx f(x) + f'(x) \Delta x + \frac{1}{2} f''(x) \left( \Delta x \right)^2
        $$
        Let's define:
        
        $$
        g_i = \frac{\partial}{\partial \hat{y}_i^{(t-1)}} l(y_t, \hat{y}_i^{(t-1)}) \qquad h_i = \frac{\partial ^2}{\partial \hat{y}_i^{(t-1)}} l(y_t, \hat{y}_i^{(t-1)})
        $$
        Then the objective function can be approximated by:
        $$
        L(t) \approx \sum_{i=1}^{n} \left[ l(y_i, \hat{y}_i^{(t-1) } ) + g_i f_t(x_i) + \frac{1}{2} h_i f_t^2(x_i) \right] + \Omega(f_t) + const
        $$
        
    - Define a tree:
        
        $$
        f_t(x) = w_{q(x)}, w \in \mathcal{R}^{T}, q : \mathcal{R}^d \rightarrow \{1, 2, \cdots, T\}
        $$
        
        $$w$$ stands for the score for each leaf (leaf weight) and $$q$$ is a mapping from the input attributes to which leaf the data end up with (aka structure of the tree).
        
        - Define complexity of the tree:
        
        $$
        \Omega(f_t) = \gamma T + \frac{1}{2} \lambda \sum_{j=1}^{T} w_j^2
        $$
        where the first term penalizes large number of leaves and the second term perfers a smoothing leaf scores.
        
        - Rewrite objective function
        
            - Define the instance set in leaf $$j$$ as:
            $$
            I_j = \{ i \lvert q(x_i) = j \}
            $$
            
            - Regroup the objective function by each leaf
            
            $$
            \begin{aligned}
            L^{(t)} &\approx \sum_i^{n} \left[ g_i f_t(x_i) + \frac{1}{2} h_i f_t^2(x_i) \right] + \Omega(f_t) \\
            & = \sum_i^{n} \left[ g_i w_{q(x_i)} + \frac{1}{2} h_i w_{q(x_i)}^2 \right] + \gamma T + \frac{1}{2} \lambda \sum_{j=1}^{T} w_j^2 \\
            & = \sum_{j=1}^{T} \left[ \left( \sum_{i \in I_j} g_j \right) w_j + \frac{1}{2} \left( \sum_{j \in I_j} h_j + \lambda \right) w^2_j \right] + \gamma T
            \end{aligned}
            $$
            
            This results in a sum of $$T$$ independent quadratic functions
            
         - The structure score
            - Two facts about single variable quadratic function
            $$
            \arg \min_x Gx + \frac{1}{2} H x^2 = - \frac{G}{H} \qquad \min_x Gx + \frac{1}{2} H x^2 = - \frac{1}{2} \frac{G^2}{H}
            $$
        
            - Let's define $$G_j = \sum_{j \in I_j} g_j$$ and $$H_j = \sum_{j \in I_j} h_j$$, the cost function becomes:
        
            $$
            L^{(t)} =  \sum_{j=1}^T \left(G_j w_j + \frac{1}{2}(H_j + \lambda) w_j^2 \right) + \gamma T 
            $$
            
            - Assume the structure of the tree ($$q(x)$$) is fixed.
            
            $$
            \begin{aligned}
            & \text{The optimal value of leaves: } \quad w_j^* = - \frac{G_j}{H_j + \lambda} \\ 
            & \text{The optimal objective: } \quad L^{(t)*} = - \frac{1}{2} \sum_j^{T} \frac{G_j^2}{(H_j + \lambda)^2} + \gamma T
            \end{aligned}
            $$
        
        - How to find the optimal tree structure $$q(x)$$?
        
            - A naive searching won't work because there might be infinite number of tree structures.
            - In practice, a greedy learning approach is employed.
            
                - start with tree of depth 0, every instance is in the same leaf, and add spliting.
                - When adding a new split, the gain in objective:
                $$
                Gain = \frac{G_L^2}{H_L^2 + \lambda} + \frac{G_R^2}{H_R^2+ \lambda} - \frac{G(_L+G_R)^2}{H_L + H_R + \lambda} - \gamma
                $$
                - scan the sorted instance (w.r.t $$x_j$$) is enough to decide the best spliting.
                
            - Splitting finding algorithm:
            
                For each node, enumerate over all feature:
                - For each feature, sort the instance by feature value
                - Use linear scan to decide best splitting along that feature
                - Take the best splitting solution along all features
                
            - Time complexity of growing a tree of depth $$K$$ with $$n$$ samples and $$d$$ features is $$O(Kd n \log n)$$.
            
                For each depth, we need to go over all $$d$$ features and for each feature, sort takes $$n \log n$$ time.
                
                This can be further optimized and scale to very large dataset.
                
            - Able to handle categorical data.
            
            - Pruning and Regularization:
            
                - $$Gain$$ can be negative because of the $$\lambda$$ term.
                - Pre-stopping: stop split if best split have negative gain, however, maybe a split can benefit future splits
                - Post-pruning: grow a tree to maximum depth, recursively prune all the leaf splits with negative gain
        
    - Overall algorithm:
    
        - Add a new tree in each iteration
        - Calculate $$g_i$$ and $$h_i$$, at the begining of each iteration, for all samples
        - Use the stats to grow a tree $$f_t(x)$$
        - Add $$f_t(x)$$ to the model $$\hat{y}_i^{(t)} = \hat{y}_i^{(t-1)} + f_t(x)$$
            
            - Usually $$f_t(x)$$ is weighted by $$\epsilon$$, a step-size or shrinkage, usually set around 0.1
            - do not do full optimization in each step, prevent overfitting
        
        
        
        
        



