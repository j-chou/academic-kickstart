+++
date = "2019-08-06T13:41:33-05:00"
draft = true
title = "The ABRACADABRA Problem"

+++

Consider the following problems:

1. *How many times do you have to flip a fair coin on average to get 3 heads (HHH) in a row? to get (HHTT)?*
2. *If a monkey is sitting at a typewriter and hits each letter of the alphabet with equal probability 1/26, how long on average will be take to type the sequence "ABRACADABRA"?*

There's a really cool technique called Martingales that can be used to solve problems of this sort. A martingale is a sequence of random variables with the following property:

$$
\mathbb{E}(X\_n | X\_1, ..., X\_{n-1}) = \mathbb{E}(X\_n | X\_{n-1})
$$
