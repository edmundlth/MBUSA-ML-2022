---
title: Common Themes in Machine Learning
description: A summary of thematic elements of machine learning encountered so far.  
toc: true
layout: post
categories: [summary, machine-learning]
comments: true
---

One goal of this note is to help you navigate the **terminology** you might encounter is machine learning. People give names to things because the name refer to something important but also common enough pattern that we don't need to come up with lots of names. Below we will see one illustrative common pattern. This is not the full scope of modern machine learning but it is illustrative of lots of common elements. 

## The task of learning
There is usually a hidden function that we are trying to learn. 

$$
y = f(x) 
$$

and we try our best to come out with $\hat{f}$ that does that. 

We might be doing regression, in which case, we want to answer: given values of a independent variable $x$, what could the response $y = f(x)$ be? 

Or we could be doing classification where we want $\hat{f}(x)$ to output the correct class of the observation $x$. 

Or we could be trying to approximate the probability of `class = some class label` given observation $x$. 



Common element of machine learning: 
## Data 
We don't have access to the truth $f$, but we do have access to a list of data $D_N = \{(x_1, y_1), \dots, (x_N, y_N)\}$ and we hope that the data is **representative** of the function, meaning it is not concentrated on one small region of $x$ for example. 

We have lots of names for $x$ - predictors, features, input, measurements...  
And lots of names for $y$ - response, labels, output, value... 

$x$ can be a vector, $x = (x_j)_j = (x_1, x_2, \dots, x_m)$, which means sometimes you might see actual numbers being labeled by two indices: 

$$
x_i = (x_{ij})_j = (x_{i1}, x_{i2}, \dots, x{im})
$$
where $x_{ij} =$ value of feature $j$ for data $i$. 
For example: 
 * $y=$ whether or not it rains and $x$ is cloudiness. So we have one continuous features and one binary output. 
 * $y=$ whether or not it rains and $x$ is cloudiness and whether it rained yesterday. We have one binary output, one continuous feature and one binary feature. 
 * $y=$ probability of rain and $x$ is temperature and cloudiness. We have a continuous output and two dimensional continuous inputs. 
 * ...



Furthermore, we have **noise** in our data, meaning we only have 

$$y_i = f(x_i) + \epsilon_i$$ 

for each of the pair in $D_N$, not $y_i = f(x_i)$. This is one way statistics comes into learning. So not only do not know what $f$ is, we don't even know that the given $y_i$ in the dataset is exactly right. 


## Model
 This of this as family of possible guesses of $\hat{f}$. And they are usually **parametrised**, so we write $\hat{f}_\theta$. 

 The parameter $\theta$ determines one particular guess of the estimate $f$. Parameters itself can live in a huge space for example $\theta = (\theta_1, \dots, \theta_d)$. 

 Examples: 
  * Naive Bayes. The parameter space depends on the distribution you choose for the factors. For example, in Gaussian Naive Bayes, you still need to find the mean and variance for each $x_i$ and for each class $k$. 
  * Neural networks. The parameters are the network weights. 
  * Decision tree. Parameters are what to split on and where. This is a case where thinking about the set of estimator $\hat{f}$ itself (the tree) is actually easier than thinking about the parameters. 
  * Random Forest. Same parameters as decision trees but one set of parameter for each tree in the forest. 
  * ... 

Notice that in the above, there are still other choices to make: distributions for the factors in naive bayes; architecture / topology, depth, width for neural networks; maximum number of nodes or maximum depth for decision trees; total number of trees in random forest. 
These choices that are **made before training** are called **hyperparameters**.



## Objective / Score / Loss / Error function
Not every guess in the family of $\{\hat{f}\}$ will be good and we need a way to measure how good a guess $\hat{f}$ is. For example, 
 * Square loss: $L(\hat{f})(x) = (f(x) - \hat{f}(x))^2$. 
 * Absolute error: $L(\hat{f})(x) = \lvert f(x) - \hat{f}(x) \rvert$. 
 * Cross-entropy
 * log-loss


(some of the above can only applied for specific kinds of $f$, e.g. log-loss is when we are trying to predict probability). Wait, but we just say that we don't  have access to $f$! So we can't actually evaluate any of the above. But we do have access to data where $f(x_i)$ is **approximately** $y_i$. So we replace that with 

$$L(\hat{f})(x_i) = (y_i - \hat{f}(x))^2$$

But we have lots of data points $\{(x_i, y_i)\}$, well, just sum them up!

$$L(\hat{f}) = \sum_i  (y_i - \hat{f}(x))^2$$

and we have arrived at the **training error** for a particular guess $\hat{f}$. 

But our guesses $\hat{f}$ are actually parametrised by $\theta$, so we can actually express training loss as a function of $\theta$, 

$$
L(\hat{f}_\theta) = L(\theta) = \sum_i (y_i - \hat{f}_\theta(x))^2
$$



## Training / fitting
With what we have set up above, training a model translate to 
> Find the best parameter $\theta$ that minimises **training** loss $L(\theta)$. 

This becomes purely a problem in **optimisation**. 

## Testing and generalisation error
Once a good parameter and therefore a good $\hat{f}$ is found, we can ask 
> "Ok, so I know that $\theta$ do well in my dataset, but how do I know that it will do well in the real world?"

Ideally we can just compare $\hat{f}_\theta$ against the truth $f$ and account for all possible value of $x$ (by taking expectation for example)
$$
L(\hat{f}) = \sum_{\text{all possible values of $x$}} (\hat{f}_\theta(x) - f(x))^2 dx
$$
(I have ignored the fact that this should be an integral and you might want to weight against distribution of $x$ to make things converge). This is the **generalisation** score / loss / error. 

However, again, this whole exercise only occur because we don't know $f$!! What we can do is 
 * Go and collect more data and try $\hat{f}_\theta$ on them and recalculate the loss. 
 * Don't use all the data in $D_N$ for training, reserve some to simulate "collecting more data later on". (train-test split)
 * Do the previous train-test split, except we do that multiple times and then average the resulting scores. (cross validations)
The hope is that the performance measure generated with these methods will be a good predictor for the actual performance "in real life". 
