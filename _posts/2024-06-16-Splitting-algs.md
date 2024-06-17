---
layout: post
title: splitting algorithms
date: 2024-05-16 19:22:00
description: I discuss basics on proximal splitting schemes for convex optimization. The main motivation is to provide background in the methods used by (Papadakis et al, 2013) to solve Benamou-Brenier's formulation of the Wasserstein distance. 
tags: optimization  
related_posts: true
---

## Proximal Algorithms

We develop algorithms aiming to solve the following problem:

$$
\min_{x \in \mathcal{H}} f(x) + g(x)
$$
where $$f$$ and $$g$$ are assumed to be proper convex closed functions. 

### Definitions

__Definition 1__: A function is said to be **closed** if its epigraph (area over the curve) is closed. 

__Definition 2__: A function is **proper convex** if it is convex, doesn't attain the value $$-\infty$$, and is not identically $$\infty$$.

### Operators
- An relation or operator $$T$$ is a subset of $$\mathbb{R}^n \times \mathbb{R}^n$$. In other words, it is a multivalued function from $$\mathbb{R}^n$$ to $$\mathbb{R}^n \times \mathbb{R}^n$$. When we say $$Tx$$ we mean the set $$\{y: (x, y) \in T\}$$. A function naturally induces a relation by the set $$(x, f(x))$$ with $$x \in \mathrm{dom}f$$.
- Convexity implies the operators $$\partial f$$ and $$\partial g$$ are **monotone**, meaning that for all $$y_1 \in \partial f(x_1)$$ and $$y_2 \in \partial f(x_2)$$, then
  
  $$ 
  (y_1 -  y_2)^\top (x_1 - x_2) \geq 0 
  $$
  
  cannot be covered as a set in $$\mathbb{R}^n \times \mathbb{R}^n$$.
- A continuous, monotone function is maximal. We say an operator $$T$$ is $$L$$-Lipschitz if
  
  $$
  \|Tx-Ty\| \leq  L\|x-y\|
  $$

  holds for all $$x,y \in \mathrm{dom}T$$.
- If $$L \leq 1$$, we say $$T$$ is *non-expansive*, and if $$L < 1$$, then we say $$T$$ is *contractive*.  
- It is easy to see that any contractive operator has a unique fixed point $$x^\star$$, and that for any initial $$x_0$$, the sequence formed by $$x_{k+1} = Tx_k$$ converges to $$x^\star$$ (Banach fixed point theorem). 
- We define the Resolvent of an operator $$A$$ with parameter $$\lambda > 0$$ to be 
 
  $$
  R_A := (I + \lambda A)^{-1}
  $$

  and the Cayley operator to be 

  $$
  C_A := 2R_A - I = 2(I + \lambda A)^{-1} - I
  $$

- We always work with subdifferential operators, that are maximally monotone, i.e. $$A  = \partial f$$ for a proper convex and closed function $$f$$.

The key connection between non-expansive operators and optimization is the following proposition.

__Proposition 1__: We have $$0 \in \partial (f + g)(x) = \partial f (x) + \partial g(x)$$ if and only if $$C_{\partial f} \circ C_{\partial g} (z) = z$$ where $$x = R_{\partial g}(z)$$.  

Indeed, if we can find an $$x$$ of the form $$R_B(z)$$ with $$z$$ being a fixed point of the composition of Cayley operators $$C_{\partial f} \circ C_{\partial g} (z)$$, then $$x$$ is a solution for our optimization problem. 

_Proof of Proposition 1_: By introducing the extra variables $$\tilde{x}, \tilde{z}$$, the fixed point condition is equivalent to:
$$
(x, z, \tilde{x}, \tilde{z}) \subseteq \{\ x \in R_B(z), \tilde{z} = 2x - z, \tilde{x} = R_A(\tilde{z}), z = 2\tilde{x} - \tilde{z} \ \}
$$
From this we see that $$\tilde{z} - z = 2x - z - (2 \tilde{x} - \tilde{z})  = 2(x - \tilde{x}) + \tilde{z} - z$$ which means that $$ x = \tilde{x} $$. Thus, we have that $$2x = \tilde{z} + z$$. Now, by definition we see that:

$$
\begin{align*}
  x &= R_{\partial g}(z) = (I + \lambda \partial g)^{-1}(z) \iff z \in x + \lambda \partial f (x).  \\
  \tilde{x} &= R_{\partial f} (\tilde{z}) = (I + \lambda \partial f)^{-1}(\tilde{z}) \iff \tilde{z} \in \tilde{x} + \lambda \partial f(\tilde{x}).  
\end{align*}
$$

adding both and using that $$2x = \tilde{z} + \tilde{z}$$, we end up with $$2x \in 2x + \lambda (\partial f + \partial g)(x)$$, or $$0 \in \partial (f + g)(x)$$.

- Thus, we may reduce the optimization problem to solving for a fixed point of the composition of the Cayley operators! Maybe we could do this by constructing an explicit sequence... 

Let us look at some useful properties of the Resolvent and Cayley operators that will allow us to do this. We assume $$\lambda > 0$$ which will be natural in all of the applications we look at. 

__Proposition 2__: The following properties for operators hold:
1. If $$A$$ is monotone, then $$R_A$$ and $$C_A$$ are non-expansive.
2. Moreover, if $$A$$ is maximal, $$\mathrm{dom}(R_A) = \mathrm{dom}(C_A) = \mathbb{R}^n$$.  

_Proof of Proposition 2_:
First is easy and follows by definition. Second is harder, called **Minty's surjectivity theorem**.
It turns out the converse of Minty's surjectivity theorem holds. Both proofs can be found in \cite{RyuYin2022}'s Stanford slides, available [here](https://large-scale-book.mathopt.com/slides/chapter10.pdf).
