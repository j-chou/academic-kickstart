+++
date = "2019-08-06T13:41:33-05:00"
draft = true
title = "Double Machine Learning"

+++

Questions:
1. What


Double machine learning is a recent method from the econometrics literature that uses ML tools to get causal results. This method is good for situations where you have a low dimensional parameter of interest (typically a treatment effect), but a complex high-dimensional confounder. Formally, we have

$$
\begin{eqnarray}
Y &=& D \theta + g(X) + \epsilon\_{1}\\\\\\
D &=& m(X) + \epsilon\_{2}
\end{eqnarray}
$$

where $\mathbb{E}(\epsilon\_1 | D, X) = 0$ and $\mathbb{E}(\epsilon\_2 | X) = 0$.
