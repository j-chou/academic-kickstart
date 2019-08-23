+++
date = "2019-08-06T13:41:33-05:00"
draft = false
title = "Bias from Unmeasured Confounding"

+++

I was thinking about the effects of unmeasured confounding on estimates of treatment effects and found some pretty interesting results. I have the following equations:

$$
\begin{eqnarray}
y &=& x + \alpha_1 w + \epsilon_1\\\\\\
x &=& \alpha_2 w + \epsilon_2
\end{eqnarray}
$$

where $\mathbb{E}(y | x, w) \sim N(x + \alpha_1 w, 1)$ and $\mathbb{E}(x | w) \sim N(\alpha_2 w, 1)$. I varied the strength of the confounder $w$ by letting $1 \leq \alpha_1, \alpha_2 \leq 10$ and then fitting the reduced model

$$
y = \beta_{r} x
$$

The graph below shows the bias $|\beta - \beta_{r}|$ in the estimate $\beta_r$ of the treatment effect as a function of $\alpha_1$ and $\alpha_2$.

{{< figure library="true" src="bias_linear_confounding_20190822.png" title="Bias due to linear confounding." lightbox="true" >}}

I was quite surprised by the highly nonlinear nature of the graph. It looks like for any $\alpha_1$, bias is the worst when $\\alpha_2 = 1$. I tried to derive the bias analytically as a function of the strength of confounding: $|\beta - \beta_r| = f(\alpha_1, \alpha_2)$, but it proved difficult. I think it's something like

$$
f(\alpha_1, \alpha_2) = \\frac{\alpha_1}{2 \alpha_2 e^{1/\alpha_2} - 1}
$$

{{< figure library="true" src="bias_linear_confounding_alpha12_20190822.png" title="Bias due to linear confounding." lightbox="true" >}}

I also made some visualizations of the bias under nonlinear confounding and the results were equally fascinating.


{{< figure library="true" src="bias_quadratic_confounding_alpha1_20190822.png" title="Bias due to quadratic confounding." lightbox="true" >}}

I'm sure someone has worked formulas for the bias in these scenarios since they are the most basic examples of confounding and I'd be very curious about the actual results.

But more generally these are problems of sensitivity analysis for causal inference.
