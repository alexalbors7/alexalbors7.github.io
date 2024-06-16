---
layout: post
title: splitting algorithms
date: 2024-06-16 19:22:00
description: The following introduces common, proximal algorithms and two interesting applications. 
tags: math 
categories: sample-posts
related_posts: true
---

We develop algorithms aiming to solve the problem 
$$    
    \mathrm{min}_{x \in \mathcal{H}} f(x) + g(x)
$$

we do not place restrictions on the smoothness of $f$ or $g$ aside from convexity and continuity. 


\begin{equation}
\label{eq:cauchy-schwarz}
\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
\end{equation}

and by adding `\label{...}` inside the equation environment, we can now refer to the equation using `\eqref`.

Note that MathJax 3 is [a major re-write of MathJax](https://docs.mathjax.org/en/latest/upgrading/whats-new-3.0.html) that brought a significant improvement to the loading and rendering speed, which is now [on par with KaTeX](http://www.intmath.com/cg5/katex-mathjax-comparison.php).
