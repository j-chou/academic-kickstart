+++
date = "2019-08-06T13:41:33-05:00"
draft = false
title = "Visualizing Omitted-Variables Bias"

+++

I was thinking about the effects of unmeasured confounding on estimates of treatment effects and decided to run some simulations. I have the following setup:

$$
\begin{eqnarray}
y &=& x + \alpha_1 w + \epsilon_1\\\\\\
x &=& \alpha_2 w + \epsilon_2
\end{eqnarray}
$$

where $\mathbb{E}(y | x, w) \sim N(x + \alpha_1 w, 1)$ and $\mathbb{E}(x | w) \sim N(\alpha_2 w, 1)$. I varied the strength of the confounder $w$ by letting $1 \leq \alpha_1, \alpha_2 \leq 10$ and then fit the reduced model

$$
y = \beta_{r} x
$$

The graph below shows the bias $|\beta - \beta_{r}|$ in the estimate $\beta_r$ of the treatment effect as a function of $\alpha_1$ and $\alpha_2$.

{{< figure library="true" src="bias_linear_confounding_20190822.png" title="Bias due to linear confounding." lightbox="true" >}}

I was quite surprised by the highly nonlinear nature of the bias. It looks like for any $\alpha_1$, bias is the worst when $\\alpha_2 = 1$.

<!-- $$
f(\alpha_1, \alpha_2) = \\frac{\alpha_1}{2 \alpha_2 e^{1/\alpha_2} - 1}
$$ -->

{{< figure library="true" src="bias_linear_confounding_alpha12_20190822.png" title="Bias due to linear confounding." lightbox="true" >}}

I also made some visualizations of the bias under nonlinear confounding and the results were equally fascinating.

{{< figure library="true" src="bias_quadratic_confounding_alpha1_20190822.png" title="Bias due to quadratic confounding." lightbox="true" >}}

These graphs motivated me to work out the at least bias for the linear case. For simplicity, let's assume $Var(W) = Var(\epsilon\_1) = Var(\epsilon\_2) = 1$.
Then the least squares estimates for the coefficient of $X$ in the regression $Y \sim X$ is

$$
\begin{eqnarray}
R_{yx} &=& \frac{1}{Var(X)}\sum\_{i=1}^{n}(x\_i - \bar{x})(y\_i - \bar{y}) \\\\\\
&=& \frac{1}{Var(X)}\sum\_{i=1}^{n}(x\_i - \bar{x})(x\_i + \alpha\_1 w\_i + \epsilon\_{1i} - \bar{x} - \alpha\_1 \bar{w} + \bar{\epsilon\_1}) \\\\\\
&=& 1 + \frac{\alpha_1 Cov(X, W)}{Var(X)} \\\\\\
&=& 1 + \frac{\alpha_1 \alpha_2}{\alpha\_{2}^2 + 1}
\end{eqnarray}
$$

The omitted-variables bias can be derived for more general cases in this way as a function of the covariance matrix of the endogenous variables, but quickly gets complicated for nonlinear equations.
