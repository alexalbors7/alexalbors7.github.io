---
layout: distill
title: A variational formulation of the Cheeger cut problem 
date: 2024-08-26 19:22:00
description: While reading about graph-based image processing I stumbled upon a variational formulation of the Cheeger cut problem. This blog discusses some of the concepts I encountered while looking into this small rabbit hole, which surprisingly lead me to operator splitting methods and fractional programming to solve TV regularized problems.  
tags: optimization  
related_posts: true
bibliography: 2024-06-26-splitting-algs.bib
---

## Variational formulation of Cheeger Cut 

During the summer, I explored Semi-Supervised Learning methods for Reinforcement Learning in extremely scarce reward settings (0.1-0.5%). During this time I had the opportunity to learn about the theoretical aspects of Graph-based SSL and really enjoyed the underlying theory behind the Laplacian. The seminal paper by Zhu et al (cite) introduced the idea of minimizing the graph Dirichlet energy for a graph $G = (V, E)$ with weight matrix $W$
$$
\mathcal{E}(u) = \frac{1}{2}\sum_{x, y \in V}^{|V|} w_{xy}(u(x) - u(y))^2
$$

subject to label conditions on certain nodes of the graph. This solution is approximately constant on similar regions on the graph, yielding information about possible graph clusters. A nice property of this problem is that the solution is harmonic, which  enjoys nice properties such as the maximal principle or the mean value property, and by setting the derivative of the functional to 0 we see that $\mathcal{L}u = 0$ on the interior of the domain, where $\mathcal{L} = D - W$ is the combinatorial Laplacian. The Laplacian matrix encodes a lot of information about the underlying graph, such as its connectivity properties (by looking at its second smallest eigenvalue and multiplicity of the respective eigenvectors). These eigenvectors can be used to find good clusters within the graph.

I write this post to force myself to learn about an adjacent method used for graph clustering based on what is known as a Cheeger cut. If instead of the Graph Dirichlet energy (which can be thought as a weighted 2-norm), we focused on the Total variation energy (which can be thought of as a weighted 1-norm), we encounter the concept of a Cheeger cut. In this blog I describe how to formulate this partitioning problem in a variational form, and basic proximal splitting methods used to approximate it, since the original problem is NP-hard (and is a wonderful opportunity to learn about fractional programming in optimization).


The graph Total Variation is 
$$\mathrm{TV}_G(u) = \sum_{x, y \in G} w_{xy}|u(x) - u(y)| =  \sum_{x, y \in G} w_{xy}| (\nabla u)_{xy}|$$ and the Cheeger cut of a subset $S \subset V$ in a graph $G$ is given by 
$$\textrm{CheegerCut}(S) := \frac{\mathrm{Cut}(S, S^C)}{\min\{|S|, |S^C|\}}$$where $\mathrm{Cut}(S, S^C) = \sum_{x \in S} \sum_{y \in S^C} w_{xy}$. Note in the definition of Cheeger cut we divide by the minimum to avoid having the trivial solution $S = \emptyset$, that is we take into consideration of "balanced" $S$ and $S^C$ are cardinality-wise. It is easy to see that if we take $u$ as the indicator function of a such a subset $S$, then $$\mathrm{TV_G}(\chi_S) = \frac{1}{2}\mathrm{Cut}(S, S^C)$$ since the differences of vertices in the same partition cancel out, and the $1/2$ factor comes from overcounting. The problem again, is that minimizing either quanitity is trivial and doesn't provide useful information in the graph, so we ask if it's possible to express the Cheeger cut instead in terms of the Total Variation function.  

We express the term $\min\{|S|, |S^C|\} = \min_c \sum_{i \in V} |(\chi_S)_i - c| =: B(u)$. Indeed, it is easy to see this is minimized by  $$
c^\star = \begin{cases}
    1 & \mathrm{if} \hspace{5pt} |S| > |S^C| \\
    0 & \mathrm{otherwise} 
    \end{cases}$$ Else we obtain a weighted, convex combination of the form $\lambda |S| + (1-\lambda)|S^C|$ which is easy to see is non-optimal unless $|S| = |S|^C$. 
With this in hand, we will show that
$$ \min_u \frac{\mathrm{TV}_G(u)}{B(u)} = 2\min_{S \subset V}\mathrm{CheegerCut}(S)$$ and minimizers for the first problem can be taken as indicators of the optimal partition $S$, i.e. $u = \chi_{S}$. We start by discussing the case for the Graph dirichlet energy, which is more tractable and provides intuition for the TV case. Using the Rayleigh quotient, the second smallest eigenvector of the graph Laplacian has the variational characterization 
$$ \lambda_2 = \min_{1^\top f = 0} \frac{\langle  f, \mathcal{L}f \rangle}{\|f\|_2}
$$ where the orthogonality condition follows from the fact the second eigenvector should be orthogonal to the first one. 
It is possible toremove the explicit constraint by noticing that $$\lambda_2 = \min_{1^\top f = 0} \frac{\langle  f, \mathcal{L}f \rangle}{\|f\|^2_2} = \min_f \hspace{2pt} \frac{\langle  f, \mathcal{L}f \rangle}{\min_{c \in \mathbb{R}} \|f - c \mathbf{1}\|^2_2} = \min_f \hspace{2pt} \frac{\sum_{i, j} w_{ij}(f_i - f_j)^2}{\min_{c \in \mathbb{R}} \|f - c \mathbf{1}\|^2_2} $$ instead. Indeed, looking at the second expression, the solution to the subproblem in the denominator is given by the projection $\mathbf{1} c = \mathbf{1}\mathrm{Proj}_{\mathrm{Span}(\mathbf{1})} f = \frac{1}{n}\langle \mathbf{1}, f\rangle$, and so in case $f$ is already orthogonal to the constant vector, nothing changes. Otherwise, since both the Laplacian operator and the numerator are invariant under shifting by a constant vector, we may always shift a minimizer to obtain orthogonality without affecting the objective. 



