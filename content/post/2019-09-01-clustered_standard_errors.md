+++
date = "2019-08-06T13:41:33-05:00"
draft = true
title = "Clusters and what to do about them"

+++


### Background

Pretty much all datasets have clusters. In A/B tests, clusters may be arbitrarily defined by the researcher, or they may actually constrain the treatment assignment.

For example, in a store test one might only be able to assign treatments to geographic regions, so that all stores in the same region receive the same treatment. At the same time, we could also create arbitrary clusters of stores based on historical sales volume, square footage of the building, etc.--variables which may not be used in the treatment assignment at all. In this post we'll explore how to handle clusters when estimating treatment effects.

### PATE vs CATE
Part of the confusion surrounding how to analyze clustered data comes from a lack of clarity around the treatment effect that's actually being estimated by the test statistic. There are often several quantities of interest in an experiment, including the most common Population Average Treatment Effect (PATE), the Cluster Average Treatment effect (CATE), and versions of these on the treated and untreated subgroups. Let's start with an example to illustrate these differences.

Suppose there are $K$ clusters of size $N_i$ with cluster-specific means $\mu_i$ for an outcome $y$ for $i = 1, ..., K$. The population mean is the weighted average

$$
\mu\_{p} = \\sum\_{i=1}^{K}\frac{N\_i}{N}\mu_{i}
$$

where $N = \sum_{i=1}^{K}N_i$ is the total population size. In general this is different from the cluster mean, which is the unweighted average

$$
\mu\_{c} = \frac{1}{K}\sum_{i=1}^{K}\mu_i
$$
