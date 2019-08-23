+++
date = "2019-08-06T13:41:33-05:00"
draft = false
title = "Instrumental Variables"
diagram = true
+++

Instrumental variables are a super useful tool in causal inference for getting around the problem of confounding.

Suppose we'd like to estimate the average effect of a treatment $X$ like exposure to an ad on an outcome $Y$ like sales. For two weeks we track a group of people and whether they were exposed to the ad ($X = 1$) or not ($X = 0$) and what their sales were over the next month. We decide to fit a linear regression model to the data:
$$Y = \beta X + \epsilon$$
assuming $\mathbb{E}(\epsilon | X) = 0$.

*Is $\beta$ the average treatment effect of $X$ on $Y$?*

The answer is almost never, unless the data was collected from a carefully controlled A/B test. Basically this is because people in the treatment group ($X = 1$) are not the same as people in the control group ($X = 0$). People that saw the ad are more likely to buy than people who didn't see the ad because of ad targeting. Our least-squares estimate $\hat{\beta}$ is likely biased upwards, and the better the ad targeting is for this ad, the worse the bias is.

This problem is called *confounding* and can be described mathematically by letting $W$ represent
confounding variables

$$
\begin{eqnarray}
Y &=& \beta X + \gamma W + \epsilon \\\\\\
X &=& \alpha W + \nu
\end{eqnarray}
$$

where $\mathbb{E}(\epsilon | X, W) = 0$ and $\mathbb{E}(\nu | W) = 0$. If $\mathbb{E}(\epsilon | X, W) = 0$, then [conditional ignorability](https://en.wikipedia.org/wiki/Ignorability) is achieved, i.e. the potential outcomes $Y^{1}, Y^{0}$ are conditionally independent of $X$ given $W$. Then the least-squares estimate for $\beta$ in the $Y$-model above would be an unbiased estimate for the ATE.

Now in theory, if you were for example Google, you could achieve conditional ignorability by taking all the variables $W$ you fed into your algorithms for ad targeting and model $Y = f(X, W)$ and get an unbiased estimate of the average treatment effect. There are several issues with this approach. First, you're usually not Google and don't have the data to adequately control for confounding. Second, even if you are Google, the algorithms used for ad targeting are enormously complex and rely on a large number of variables. What else can you do?

In the happy case where you have an *instrumental variable*, you can still get unbiased estimates of the ATE without even modeling the confounding. An *instrumental variable* is just a variable that causes the treatment $X$ but doesn't affect $Y$ except through $X$:

$$
\begin{eqnarray}
Y &=& \beta X + \gamma W + \epsilon \\\\\\
X &=& \alpha W + \eta Z + \nu
\end{eqnarray}
$$

To simplify the discussion, we assume linear relationships between all the variables. The diagram below illustrates the instrumental variable.

```mermaid
graph TD;
  Z-->X;
  W-->X;
  W-->Y;
  X-->Y;

```

To restate the issue, without $Z$, we cannot get unbiased estimates for $\beta$ in the model, because we don't have good knowledge of $W$. The idea behind IV is that we can measure how a change in the instrument $Z$ induces a change in the outcome $Y$, independently of how $W$  

You can think about it like this: our target is the partial derivative of $Y$ with respect to $X$, $\beta = \partial Y / \partial X$. Let's think about $\partial Y / \partial W$ for a moment. From the chain rule we have

$$ \frac{\partial Y}{\partial Z} = \frac{\partial Y} {\partial X} \frac{\partial X}{\partial Z}$$

so rearranging terms we have

$$
\beta = \frac{\partial Y}{\partial X} \\
= \frac{ \frac{\partial Y}{\partial Z} }{ \frac{\partial X}{\partial Z} }
$$

This leads to the Wald estimator for the ATE of a binary treatment variable:

$$
\begin{eqnarray}
\hat{\beta}_{WALD} &=& \frac{\frac{\partial Y}{\partial Z}}{\frac{\partial X}{\partial Z}} = \frac{\mathbb{E}(Y|Z = 1) - \mathbb{E}(Y|Z = 0)}{\mathbb{E}(X|Z = 1) - \mathbb{E}(X|Z = 0)}
\end{eqnarray}
$$

The idea is the same when the treatment is continuous: learn regression models $\mathbb{E}(Y | Z)$ and $\mathbb{E}(X | Z)$ and estimate the treatment effect as

$$
\hat{\beta}_{2SLS} = \frac{\hat{\beta}\_{Y \sim Z}}{\hat{\beta}\_{X \sim Z}}
$$

where $\hat{\beta}\_{Y \sim Z}$ and $\hat{\beta}\_{X \sim Z}$ are the least-squares estimates for the coefficients of $Z$ in the regression $Y \sim Z$ and $X \sim Z$, and 2SLS stands for two-stage least-squares. Below we run a simulation comparing the 2SLS estimator against a naive regression estimator, where the true average treatment effect is $\beta = 2.5$.

```R
n <- 1000
beta <- 2.5
w <- rnorm(n, -4, 1)
z <- rnorm(n, 2, 3)
x <- -8*w + 4*z + rnorm(n, 0, 1)
y <- beta*x + 5*w + rnorm(n, 0, 1)
df <- data.frame('w' = w, 'z' = z, 'x' = x, 'y' = y)

lr_mod <- lm(y ~ x, data = df)
lr_est <- lr_mod$coefficients['x']

yz_mod <- lm(y ~ z, data = df)
xz_mod <- lm(x ~ z, data = df)

iv_est <- yz_mod$coefficients[2]/xz_mod$coefficients[2]
iv_est # 2.51
lr_est # 2.32
```

When we use a naive regression of $Y$ on $Z$ without accounting for the confounding $W$, our estimate is quite far off from the true effect of 2.5, but the 2SLS estimate is much closer to the truth.

### How to recognize an instrumental variable?
A variable is a valid instrument if it causes the treatment but has no direct effect on the outcome. For example, the randomization of treatment assignment in an A/B test is a powerful instrument because it determines treatment exposure but has no direct effect on the outcome.

Sometimes there are naturally occurring instruments as well. Distance to the nearest school is an instrument for the effect of college credits on future earnings because students that are further away from a college are likely to have less college credits, but otherwise distance to the nearest school shouldn't have a direct effect future earnings.

Even with this basic setup there are lots of interesting questions one could ask:  

1. How does the strength of the relationship between instrument and treatment affect the quality of IV estimates?   
2. Is it possible to test whether a variable is a valid instrument?
3. What if relationships between variables are not linear?  
4. What if I mistake an instrumental variable for a confounder?

These topics are beyond the scope of the this post, so we refer the interested reader to Morgan and Winship (2016). In summary, a strong instrumental variable makes the task of causal inference much more feasible. Without an IV, we would need to model confounding through careful variable selection and the use of flexible models.

<!-- However, the question of identifiability--*with the variables I have, is it possible to figure out the causal effect of interest if I had infinite data on each of my variables?*--has mostly been laid to rest by the work of Judea Pearl and others, who invented the [back-door criterion](https://en.wikipedia.org/wiki/Confounding) and developed identification algorithms for casual diagrams. There's also a lot of current research on estimation of casual effects when there's no IV. See for example V. Chernozhukov et. al.'s work on double machine learning and M. Oprescu et. al.'s work on orthogonal random forests which we may discuss in future posts. -->
