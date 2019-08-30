+++
date = "2019-08-06T13:41:33-05:00"
draft = true
title = "Clustering"

+++

In an A/B test, there's often some type of clustering of the units that may or may not constrain the treatment assignment. For example, in a store test one might only be able to assign treatments to geographic regions, so that all stores in the same region receive the same treatment. At the same time, we could also create arbitrary clusters of stores by historical sales volume, square footage of the building, etc. which may not be used in the treatment assignment at all but may be useful for analysis.

Clustering in data gives the researcher a dizzying number of degrees of freedom to analyze the data. In this post we'll address the questions of *do we need to take the clusters into account when estimating the treatment effects and how do we rigorously account for clustering effects?*

Part of the confusion comes from a lack of clarity around the treatment effect that's actually being estimated by the test statistic. There are often several quantities of interest in an experiment, including the most common Population Average Treatment Effect (PATE), the Cluster Average Treatment effect (CATE), and versions of these on the treated and untreated subgroups.

Suppose there are $K$ clusters of size $N_i$ and cluster-specific means $\mu_i$ for an outcome $y$ for $i = 1, ..., K$. The population mean is the weighted average

$$
\\sum\_{i=1}^{K}\frac{N\_i}{N}\mu_{i}
$$

where $N = \sum_{i=1}^{K}N_i$ is the total population size. In general this is different from the cluster mean, which is the unweighted average

$$
\frac{1}{K}\sum_{i=1}^{K}\mu_i
$$

If there is significant heterogeneity between different clusters, then
