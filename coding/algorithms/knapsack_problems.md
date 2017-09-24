### Knapsack Problem

In this post, we talk about a classic series of problem called Knapsack problem. It falls into the category of ***combinatorial optimization***. 

In one of the previous post we looked at [combination problems](http://weitongruan.github.io/coding/algorithms/combination_problems), where the combination problem aims to find all possible (feasible) solutions under some certain constraints and is solved mainly using backtracking. 

The problem we discussed today is one level higher than the previous one, because, from all possible combinations that satisfy the constraint, it wants an optimized solution/ the highest score given a score function.


Table of Contents:
- [Problem Statement](#problem_statement)
- [Naive Solution from Backtracking](#naive_solution_from_backtracking)
- [Dynamic Programming Solution](#dynamic_programming_solution)

<a name='problem_statement'></a>
#### Problem Statement:

The problem can be found on [this wiki page](https://en.wikipedia.org/wiki/Knapsack_problem).

A very simple example would be, a robber goes into a jewery house and the bag it has can only carry say 40 pounds and each jewery has its weight and value, as detailed in the table. The question we want to answer is how can the robber get the maximum value.

| Item | Weight | Value |
| --   | --     | --    |
| 1    |   4    |   15  |
| 2    |   7    |   13  |
| 3    |   11   |   27  |


Mathematically speaking, this problem can be thought of as an optimization problem formulated below:

Given the table, and maximum weight $$W$$ it can carry, we want to solve for $$k_1, \cdots, k_n$$ where each $$k_i$$ stands for the number of $$i$$-th jewery the robber should take with him.

$$
\begin{aligned}
\max_{k_1, \cdots, k_n} & \sum_{i=i}^n k_i v_i \\
s.t. & \sum_{i=1}^n k_i w_i <= W \\
& k_i \in \{0, 1, 2, \cdots, N \} \quad \forall i
\end{aligned}
$$

where $$n$$ stands for the number of various type of jeweries.

Based on how large $$N$$ becomes, this problem has serveral variants.

- If $$N = 1$$, each jewery can be taken at most once. ---- 0-1 knapsack problem
- If $$N = c$$, each jewery can be taken at most $$c$$ times. ---- bounded knapsack problem
- If $$N = \infty$$, each jewery can be taken unlimited number of times. ---- unbounded knapsack problem.


<a name="naive_solution_from_backtracking"></a>
#### Naive solution from backtracking:

***It is obvious that this is a combination problem, where all potential answers come from various combination of the jewery set and it has to satisfy a certain condition.*** 

In our example, the condition is on the total weight.

Following the ***backtracking*** approach that we are familiar with, this problem can also be solved. ***Simply try each possible solution using backtracking and updata the score for each combination that satisfies the condition.***


<a name='dynamic_programming_solution'> </a>
#### Dynamic Programming Solution:

1. For the unbounded:

    Let's think about the solution this way, suppose $$s(w)$$ stands for the optimal profit the robber can get when his bag can carry at most $$w$$ pounds, we can surely have an update formula:
    
    $$
    s(w) = \max_{i: w_i \leq w} \left( v_i + s(w-w_i) \right)
    $$

    ```python
    def knapsackUnbounded(table, W):
        # table is the table above where keys are weights and attributes are values
        # result stores the optimal solution for each maximum weight (0-W)
    
        result = [0 for _ in range(W+1)]
        for i in range(1, W+1):
            result[i] = max([result[i-j]+table[j] for j in table.keys() if j<= i], default=0)
        
        return result[-1]
    ```
    
2. For the 0-1 knapsack:

    Here we need to consider that each jewery can be chosen at most once.
    
    What we can do is rewrite the above update formula.
    
    $$
    s(i, w) = 
    \begin{cases}
    s(i-1, w), & \text{if } w_i > w \\
    \max (s(i-1, w), s(i-1, w-w_i)+v_i) & \text{otherwise}
    \end{cases}
    $$
    where $$s(i, w)$$ stands for the max profit the robber can get with the first $$i$$ jewery, each of them used at most once under max weight $$w$$.
    
    ***Note that here, due to the first inserted index $$i$$, and the update formula use $$i-1$$ to opdate $$i$$ makes sure that each time we only use the each jewery once.***

    ```python
    def knapsackOne(table, W):
    
        result = [[0]* (W+1) for _ in range(len(table.keys()))]
        
        weights = list(table.keys())
        
        for i in range(len(weights)):
            for j in range(1, W+1):
                
                if weights[i] > j:
                    result[i][j] = result[i-1][j]  
                else:
                    result[i][j] = max(result[i-1][j], result[i-1][j-weights[i]]+table[weights[i]])
                
        return result[-1][-1]
    ```
    
3. For the bounded:

    One thing we can do is to add all available jeweries into the list and do what we did for the 0-1 problem.

    

    





