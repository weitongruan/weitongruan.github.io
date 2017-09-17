### Calculate Conditional Probability

Table of Contents:
- [Problem statement](#problem_statement)
- [My ideas](#my_ideas)
- [Notes on sorting](#notes_on_sorting)

<a name='problem_statement'></a>
#### Problem statement:

The problem statement is as follows:

Suppose we have a set of tuples, where each tuple has two elements `$(p_i, y_i)$`. `$y_i$` is a discrete class label and can only be either `$+1$` or `$-1$` and `$p_i$` can be either discrete or continuous. The problem is to calculate the conditional probability `$P(p_i > p_j \lvert y_i=+1, y_j=-1)$`.


<a name='my_ideas'></a>
#### My ideas:

##### First intuition:

Because the conditional probability involves a pair of tuple, a ***naive*** approach can be implemented that compares every possible pair combination and use two counter to record the number of times when the conditions are satisfied. More specifically, use `$C(p_i > p_j)$` to denote the number of times this condition satisfies and use `$C(y_i = +1, y_j = -1)$` to denote the number of pairs that meet this condition, then the conditional probability can be calculated as:

```math
P(p_i > p_j \lvert y_i=+1, y_j=-1) = \frac{C(p_i > p_j)}{C(y_i = +1, y_j = -1)}
```

This is definitely an `$O(n^2)$` algorithm, where `$n$` stands for the total number of tuples.

##### Second thought:

A slightly better way to use the same idea might be first split the set into two, with one being tuples with label `$+1$` and another with label `$-1$`. Suppose we have `$m$` tuples labeled `$+1$` and `$n-m$` tuples with label `$-1$`. Then for every tuple in `$+1$` set, we compare it with all tuples in `$-1$` set to check if the above conditions are met.

In this way, the total number checks reduces from `$n^2$` to `$m(n-m)$`, but still a `$O(n^2)$` algorithm.

Can we do better?

##### Use sort:

What if we first sort them using `$p$`? Because sort only takes `$O(nlogn)$`, if we can calculate the result using algorithms better than `$O(n^2)$`, we have a better algorithm.

Let's use an example where the set is organized as a list:

```math
(-2, -1), (1, 1), (2, 1), (8, -1), (3, 1), (7, 1), (2, -1)
```

If we sort the tuples following `$p$`, the new list becomes:

```math
(-2, -1), (1, 1), (2, 1), (2, -1), (3, 1), (7, 1), (8, -1)
```

From here, we notice that both `$C(p_i > p_j)$` and `$C(y_i = +1, y_j = -1)$` can be counted by going through the sorted list, which takes only `$O(n)$`.

The way to update `$C(p_i > p_j)$` is to use two variables, ***total*** and ***up_to_now***, where ***total*** corresponds to the total number of pairs which satisfy the condition and ***up_to_now*** means the number of tuple with label `$-1$` that we have seen up to the current index. The update formula is:

```python

if p_i == -1:
    up_to_now += 1
else:
    total += up_to_now
```

Suppose the input is sorted, then the code is:

```python

def calProb(data):
    # data is a list of tuples sorted along p
    
    total, up_to_now = 0, 0
    minus, plus = 0, 0
    for d in data:
        if d[1] == -1:
            up_to_now += 1  # we have one more small data with -1
            minus += 1  # number of minus gets updated by one
        elif d[1] == 1:
            total += up_to_now  # add the number of smaller data with -1
            plus += 1  # number of plus gets updated by one
    
    return total / (plus * minus)

```


***The algorithm seems fine except we didn't consider a special case where `$p_i = p_j$`, since we are calculating the conditional probability `$P(p_i > p_j \lvert y_i = +1, y_j = -1)$`, the above code works only when data is reversely sorted according to `$y$`, which means that data with label `$+1$` has to be placed in front of `$-1$` when they share the same `$p$`.***

This sort can be easily implemented in python using the following code:

```python
# dataset is a list of tuples

# sort in place, "-x[1]" means y is reversely sorted first
dataset.sort(key=lambda x: (x[0], -x[1]))

# in case a new copy is needed, use the following
# sorted(dataset, key=lambda data: (data[0], -data[1]))

# Numpy also has nice built-in function to implement sort 
# according to multiple attributes, e.g. np.lexsort()

```
Hence, with a sorting as a preprocessing and a linear counting algorithm, we can calculate the result in `$O(nlogn)$` time.

<a name='notes_on_sorting'></a>
#### Notes on sorting

##### The idea of stable/stability:

When two element has the same value in terms of sorting, stability means their original order is kept.

##### Sort with multiple attributes:

A naive implementation is: first sort following the least important attribute, then use a stable sorting algorithm to sort iteratively from least important to most important.


