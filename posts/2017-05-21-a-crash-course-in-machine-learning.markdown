---
title: A crash course in machine learning (part I)
---

This document is intended to be the first part of a fast-paced, whirlwind tour through the essential mathematics underlying modern machine learning. I've been very selective in the topics chosen for discussion, with the aim being to derive material from simpler principles whenever possible. Thus, we begin with some basic information-theoretic concepts and proceed to analyze classical models using them, with no arbitrary selection of loss functions.

Mathematical prerequisites are modest and neither extreme nor negligible. In this first post, we quickly discover logistic regression from first principles and conclude with a description of (but not a solution to) the numerical optimization problem that it poses.

#### Cross-entropy

Suppose you wish to communicate with a third-party via coded messages, using a coding scheme you've both agreed upon ahead of time. More precisely, there is an underlying set \\(D\\) of data, and the content of a message is the result of encoding an element \\(d\\in D\\) with the coding scheme.

Suppose that over the course of your communications the raw data elements \\(d \\in D\\) are drawn from \\(D\\) according to some probability distribution \\(P\\). In other words, the probability that you want to encode \\(d \\in D\\) and send the encoded message to your friend is \\(P(d)\\). Unfortunately, the distribution \\(P\\) is not known to you. In fact, you mistakenly believed that the distribution was some other distribution, say \\(Q\\), and you designed your coding scheme to minimize the expected length of an encoded message under the assumption that data elements are sampled according to \\(Q\\), not \\(P\\).

Because the coding scheme was optimized according to a distribution that does not accurately reflect which data elements you will encode, it stands to reason that the encoded messages you actually send will be longer than if you'd designed the coding scheme based on \\(P\\) instead. The expected length of an encoded message sampled randomly according to the true distribution \\(P\\) but encoded in a scheme optimized for \\(Q\\) is the **cross-entropy** of \\(P\\) and \\(Q\\), often denoted \\(H(P,Q)\\).

#### The information content of an event

In order to give a more rigorous definition of cross-entropy, we need the notion of the **information content** of an event drawn from an underlying sample space according to some probability distribution. The standard definition is

\\[I(x) = -\\log(P(x)).\\]

In other words, for an event \\(x\\), the information content (or *self-information*) of \\(x\\) is the negative natural logarithm of the probability of drawing \\(x\\) according to our chosen distribution. This might seem strange and arbitrary, but it's actually very natural.

First, the logarithm makes information content *additive*. Given independent events \\(x,y\\), the information content derived from witnessing both \\(x\\) and \\(y\\) is 

\\[-\\log(P(x\\cup y)) = -\\log(P(x)P(y)) = (-\\log(P(x))) + (-\\log(P(y))),\\]

which is the sum of the information content of \\(x\\) and \\(y\\) considered separately.

Second, the minus sign (combined with the fact that the logarithm is an increasing function) implies that events of higher probability have lower information content and, conversely, events of lower probability have higher information content. This reflects the intuitive idea that witnessing a very unlikely event is more informative to the observer than witnessing an extremely likely event.

#### Cross-entropy and training machine learning models

We can now revisit the notion of cross-entropy more rigorously. We can define

\\[H(P,Q) = -\\mathbb{E}_{d\\sim P}\\log Q(d).\\]

In other words, the cross-entropy is the expected amount of information necessary to encode a message according to \\(Q\\) but with the underlying data drawn according to \\(P\\).

How does any of this relate to machine learning? In fact, it's very natural. Traditionally, one thinks of a machine learning algorithm as taking a set of training data and outputting a deterministic function. However, we can also think of a machine learning algorithm as outputting a conditional probability distribution \\(Q(\\mathbf{y}|\\mathbf{x})\\). Given an input vector \\(\\mathbf{x}\\), this distribution tells us the probability that the associated output is the vector \\(\\mathbf{y}\\). This flexible formulation also allows the algorithm to deal with inconsistent training examples (e.g., where the same input vector has been associated with more than one output vector).

Of course, there is also the *true* distribution \\(P(\\mathbf{y}|\\mathbf{x})\\). Given the choice of many machine learning algorithms, we would like to pick the one that tends to minimize the cross-entropy \\(H(P,Q)\\) between these distributions.

Formulating machine learning algorithms in this way is very general and frees us from the burden of deciding (perhaps arbitrarily) what loss function we should use during training. Minimizing cross-entropy often corresponds to very natural loss functions, such as the mean squared error. As we will see, the cross-entropy is also very naturally related to a technique called maximum likelihood estimation, which provides another theoretical justification for its use.

#### Maximum likelihood estimation

Suppose there is a true data-generating distribution \\(P(\\mathbf{x})\\), and we have a parametric family of hypothesis distributions \\(Q(\\mathbf{x};\\mathbf{\\theta})\\). Maximum likelihood estimation is a generic procedure for picking a value of \\(\\mathbf{\\theta}\\) when we have a sequence of examples \\(\\{\\mathbf{x}_1,\\mathbf{x}_2,\\dots,\\mathbf{x}_m\\}\\) drawn from \\(P\\). The idea is to pick the value of \\(\\mathbf{\\theta}\\) that maximizes the product

\\[\\prod_{i=1}^m Q(\\mathbf{x}_i;\\mathbf{\\theta})\\]

taken over the example sequence.

This product is subject to numerical underflow, so it's very common to take the logarithm, so that we're trying to maximize the sum

\\[\\sum_{i=1}^m \\log Q(\\mathbf{x}_i;\\mathbf{\\theta}).\\]

This is known as **maximum likelihood estimation**, as it tries to maximize the sum of the **log-likelihoods**. It's the same as minimizing

\\[\\sum_{i=1}^m -\\log Q(\\mathbf{x}_i;\\mathbf{\\theta}).\\]

Suddenly this is looking closely related to the cross-entropy. In fact, minimizing this is exactly the same as minimizing the cross-entropy between the empirical data-generating distribution and the chosen hypothesis distribution \\(Q(\\mathbf{x};\\mathbf{\\theta})\\).

#### Linear regression

This is arguably the simplest machine learning model. Let us consider the case where the input is a vector and the output a single real number. We begin with a true data-generating distribution \\(P(y|\\mathbf{x})\\), and we consider the parametric family of hypothesis distributions

\\[Q(y|\\mathbf{x};\\mathbf{w}) = N(y;f(\\mathbf{x};\\mathbf{w});\\sigma^2)\\]

where \\(N\\) specifies a normal distribution, \\(\\sigma^2\\) is a fixed variance chosen ahead of time, \\(\\mathbf{x}\\) is a vector of weights acting as the parameters to the model, and \\(f(\\mathbf{x},\\mathbf{w}) = \\mathbf{w}^T \\mathbf{x}\\) is a linear function of the vectors \\(\\mathbf{x}\\) and \\(\\mathbf{w}\\). Here \\(f\\) plays the role of predicting the mean of the Gaussian (normal) distribution. It's the linearity of \\(f\\) that gives this model its name.

We can select a weights vector \\(\\mathbf{w}\\) using maximum likelihood estimation. In particular, a calculation (omitted) shows that, given \\(m\\) training examples \\(\\{\\mathbf{x}_1,\\dots,\\mathbf{x}_m\\}\\) with associated outputs \\(\\{y_1,\\dots,y_m\\}\\), we have

\\[\\sum_{i=1}^m \\log Q(y_i|\\mathbf{x}_i;\\mathbf{w}) = -m\\log \\sigma - \\frac{m\\log(2\\pi)}{2} - \\sum\_{i=1}\^m\\frac{\\|f(\\mathbf{x}_i,\\mathbf{w}) - y_i\\|\^2}{2\\sigma^2}.\\]

Minimizing this is the same as minimizing the simpler quantity

\\[\\frac{1}{m}\\sum\_{i=1}^m \\| f(\\mathbf{x}\_i,\\mathbf{w}) - y\_i\\|^2,\\]

which you might recognize as the **mean squared error**. We've derived the standard mean squared error loss function from simpler, more fundamental principles.

#### Logistic regression

Linear regression predicts a real-valued output given a vector-valued input. Suppose we want to classify inputs into one of just two classes, a problem called *binary classification*. We can achieve this with a simple modification of linear regression. Specifically, if we have a smooth function \\(g:\\mathbb{R}\\rightarrow\\mathbb{R}\\) taking values in the interval \\(\[0,1\]\\), then we can replace \\(f\\) with \\(g\\circ f\\). These values can be interpreted as probabilities, with a lower probability suggesting the input belongs to one class and a higher probability suggesting the input belongs to the second class.

The most common choice of \\(g\\) is the **logistic sigmoid** function \\(\\sigma\\), defined as

\\[\\sigma(x) = \\frac{1}{1 + \\text{exp}(-x)}.\\]

The resulting model is known as **logistic regression**. As before, we can train the model by selecting the weights vector \\(\\mathbf{w}\\) that minimizes the cross-entropy.

In the cases of both linear and logistic regression, we've so far sidestepped the question of how to select the optimal weights vector. As it happens, in linear regression this optimization problem has a closed-form solution, but the nonlinearity introduced by \\(g\\) in logistic regression means that in general we have to rely on numerical optimization in that case. We conclude this post at this point with this conceptual framework that poses the numerical optimization problem that we will rapidly solve in the next part of the series.