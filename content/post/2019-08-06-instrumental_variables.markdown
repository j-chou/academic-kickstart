+++
date = "2019-08-06T13:41:33-05:00"
draft = false
title = "Instrumental Variables"

+++

Instrumental variables are a super useful tool in causal inference for getting around the problem of confounding.

The setting is that we want to estimate the average effect of a treatment $X$ like exposure to an ad on an outcome $Y$ like sales. Suppose for two weeks we track a group of people and whether they were exposed to the ad ($X = 1$) or not (($X = 0$) and what their sales were over the next month. We decide to fit a linear regression model to the data:
$$Y = \beta X + \epsilon$$
and assume $\mathbb{E}(\epsilon | X) = 0$.

Is $\beta$ the average treatment effect of $X$ on $Y$?

Almost never, unless the data was collected from a carefully controlled A/B test. Basically this is because people in the treatment group ($X = 1$) are not the same as people in the control group ($X = 0$). People that saw the ad are more likely to buy than people who didn't see the ad because of ad targeting. Our least-squares estimate $\hat{\beta}$ is likely biased upwards, and the better the ad targeting is for this ad, the worse this problem becomes.

This problem is called *confounding* and can be described mathematically by letting $W$ represent
confounding variables

$$
\begin{aligned}
Y = \beta X + \gamma W + \epsilon \\
X = \alpha W + \nu
\end{aligned}
$$

where $\mathbb{E}(\epsilon | X, W) = 0$ and $\mathbb{E}(\nu | W) = 0$. The theorem of conditional ignorability says that if the potential outcomes $Y^{1}, Y^{0}$ are conditionally independent of $X$ given $W$, then assuming $\mathbb{E}(\epsilon | X, W) = 0$, the least-squares estimate for $\beta$ in the model above would be an unbiased estimate for the ATE.

Now in theory, if you were for example Google, you could achieve conditional ignorability by taking all the variables $W$ you fed into your algorithms for ad targeting and model just $Y = \beta X + \gamma W + \epsilon$ and get an unbiased estimate of the average treatment effect. There are two issues: you're usually not Google and don't have the data to adequately control for confounding. Second, even if you are Google, the algorithms used for ad targeting are enormously complex and rely on a large number of variables that may not even be clearly tracked. What else can you do?

In the happy case where you have an *instrumental variable*, you can still get unbiased estimates of the ATE without even modeling the confounding. An instrumental variable is just a variable that causes the treatment $X$ but doesn't affect $Y$ except through $X$:

$$
Y = \beta X + \gamma W + \epsilon \\
X = \alpha W + \eta Z + \nu
$$

The diagram below illustrates the instrumental variable.


To restate the issue, we cannot get unbiased estimates for $\beta$ in the model above, because we don't have good knowledge of $W$. The idea behind IV is that we can measure how a change in the instrument $Z$ induces a change in the outcome $Y$. You can think about it like this: we're trying to measure the partial derivative of $Y$ with respect to $X$: $\beta = \partial Y / \partial X$.
Let's think about $\partial Y / \partial W$ for a moment. From the chain rule we have

$$ \frac{\partial Y}{\partial W} = \frac{\partial Y} {\partial X} \frac{\partial X}{\partial W}$$

so rearranging terms we have

$$
\beta = \frac{\partial Y}{\partial X} \\
= \frac{ \frac{\partial Y}{\partial W} }{ \frac{\partial X}{\partial W} }
$$

This leads to the Wald estimator for the ATE of a binary treatment variable when you have an IV and the 2-stage least squares estimate for the ATE in the case of a continuous treatment variable.
