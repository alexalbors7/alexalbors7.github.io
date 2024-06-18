---
layout: post
title: Proximal mappings and the Douglas-Rachford algorithm 
date: 2024-05-16 19:22:00
description: I discuss basics on proximal splitting schemes for convex optimization. The main motivation is to provide background for the methods used in 'Optimal Transport with Proximal Spliting' by Papadakis et al to solve Benamou-Brenier's formulation of the Wasserstein-2 distance. 
tags: optimization  
related_posts: true
bibliography: 2024-06-16-splitting-algs.bib
---

## Proximal Algorithms

We develop algorithms aiming to solve the  problem

$$
\min_{x \in \mathcal{H}} f(x) + g(x)
$$

where $$f$$ and $$g$$ are assumed to be proper convex closed functions. The main motivation to survey these methods comes from reading the article 'Optimal Transport with Proximal Splitting' by Papadakis et al <d-cite key="Papadakis_2014"></d-cite>. Since these derivations are not discussed in the paper, I found the monograph of Parikh and Boyd <d-cite key="Parikh2013"></d-cite> as well as the Dirk Lorenz's [regularize blog](https://regularize.wordpress.com/), to be particularly useful.  


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

- It is clear that a non-expansive operator need not converge to a fixed point: consider a rotation matrix or the operator $$x \mapsto -x$$. However, it turns out we may modify the operator to ensure convergence *while conserving the same fixed points*! We do so by averaging operators.
  
__Definition 3__: 
  An operator $$T$$ is **averaged** if it can be written as a strict convex combination of the identity and a non-expansive operator $$A$$,

  $$T = \alpha I + (1 - \alpha)A \quad \alpha \in (0,1)$$

We now show an averaged operators converges to one of its fixed points, in case one exists.

__Theorem 1__ (Convergence of Averaged Operators):
  An averaged operator $$F$$ converges to a fixed point if one exists. 

_Proof_:

  Let $$F = \alpha I + (1-\alpha)G$$ with $$G$$ non-expansive and $$\alpha \in (0,1)$$. Then, using the identity $$\|\alpha a + (1-\alpha)b\|^2 = \alpha \|a\|^2 + (1 - \alpha)\|b\|^2 - \alpha(1 - \alpha) \|a - b\|^2$$,

  $$\begin{align}
    \|x^{k+1} - x^{\star}\|^2 
    &= \|\alpha (x^k - x^\star) + (1-\alpha) (Gx^k - Gx^\star)\|^2 \\ 
    &= \alpha \| (x^k - x^\star)\|^2 +(1-\alpha)\|(Gx^k - Gx^\star)\|^2 - \alpha (1 - \alpha) \|Gx^k - x^k\|^2 \\
    &\leq \alpha \|x^k - x^\star\|^2 + (1-\alpha) \|x^k - x^\star\|^2 - \alpha (1 - \alpha) \|Gx^k - x^k\|^2 \\
    &= \|x^k - x^\star\|^2 - \alpha(1 - \alpha) \|Gx^k - x^k\|^2
  \end{align}$$

  and by a telescoping argument on the recursive term , we have that

  $$\|x^{k+1}\| - x^\star\|^2  \leq \|x^0 - x^\star\| - \alpha(1-\alpha) \sum_{i=0}^k \|x^i - Gx^i\|^2 $$

  hence, 

  $$\sum_{i=0}^k \left\lVert x^i - Gx^i\right\rVert^2 \leq \frac{1}{\alpha(1-\alpha)}\|x^0 - x^\star\|$$

  which by non-expansiveness, shows that

  $$\|x^k - Gx^k\|^2 \leq \frac{1}{(k+1)(\alpha(1-\alpha))}\|x^0 - x^\star\|^2$$

  So convergence is linear, and we note that $$\alpha = 1/2$$ yields the tighest upper bound on the rate. 

So we're done! We may use the averaged operator $$T=\frac{1}{2}(I + C_{\partial f} \circ C_{\partial g})$$ to converge to a fixed point $$z^\star$$, and obtain a good approximation to the optimal by setting $$x^\star = R_{\partial g} (z^\star)$$. This is the core idea behind both Douglas-Rachford and ADMM. Since we will be working with subdifferential operators of convex functions, it is imperative to study the Cayley and Resolvent operators in those cases. This brings us to Proximal mappings.

### The Proximal operator
We finally introduce the operator that will play a key role in the derivation of Douglas-Rachford and ADMM. 
__Definition__:
  Given a function $$f$$ and a parameter $$\lambda > 0$$, the *proximal operator* of $$f$$ is defined as 

  $$\mathrm{Prox}_{\lambda f} (x) := \mathrm{argmin}_u \hspace{2pt} f(u) + \frac{1}{2\lambda} \|u - x\|_2^2 $$

- A function $$f$$ with an easy proximal operator is called either proximable or simple. 
  - Proximal of $$f(x) = 0$$ is identity
  - Proximal of $$f(x) = x^2$$ is Shrinkage. 
  - Proximal of $$f(x) = \|x\|$$ is soft-thresholding. 
  - Proximal of $$f(x) = \mathbf{1}_{\{x \geq 0\}}$$ is a projection onto $$C$$. 
  - Proximal of $$f(x) = \sum_{i=1}^d f_i(x_i)$$ is separable. 
  - Proximal of $$f(x) = \|x\|_1$$ is soft-thresholding on individual coordinates. 
  - Proximal of $$f(X) = \|X\|_*$$ is soft-thresholding on singular values. 

The key connection is the following:
__Theorem__:
Given a function $$f$$, the equality 

$$\mathrm{Prox}_{\lambda f}(x) = R_{\partial \lambda f} = (I + \lambda \partial f)^{-1}$$
 holds. 

_Proof_:

$$ u \in \mathrm{Prox}_{\lambda f}(x) \iff 0 \in \partial f(u) +\frac{1}{\lambda}(u - x) \iff x \in (\lambda \partial f + I)u \iff u \in (I + \partial \lambda f)^{-1}(x) = R_{\lambda \partial f}(x) $$

- Applying the proximal operator can be thought of as taking an implicit gradient step. Whereas gradient descent employs the update $$x^{k + 1} = x^k - \lambda \partial f (x^k)$$, the operation $$x^{k + 1} = \mathrm{Prox}_{\lambda f}(x^k)$$ results in a slight modification of the gradient. To see this, use the resolvent definition to get 

  $$x^{k+1} + \lambda \partial f(x^{k+1}) = x^k \implies x^{k+1} = x^k - \lambda \partial f(x^{k+1})$$

which is very similar to gradient descent, but the update step is implicit, so the proximal operator as minimization update is a backwards method (like Forward vs Backwards Euler). 

An important identity that generalizes the orthogonal decomposition and that makes computational of proximal mappings much simpler is the following. 

__Theorem (Moreau's Identity)__:
  The following identity holds. 
    $$\mathrm{Prox}_{\lambda f^*}(x) + \lambda \mathrm{Prox}_{f/\lambda}(x/\lambda) = x$$

_Proof_:
 We first prove the case where $$\lambda = 1$$: 
 $$\begin{align*}
    u = \mathrm{Prox}_(x) &\iff 0 \in \partial f(u) + u - x  \\ 
                        &\iff x - u \in \partial f(u) \\
                        &\iff  u \in \partial f^*(x - u) \\
                        &\iff  0 \in \partial f^*(x - u) + ((x - u) - x) \\
                        &\iff x - u = \mathrm{Prox}_{f^*}(x)
\end{align*}$$

For the general case, we first compute $$(\lambda f)^*$$ and then $${(\lambda f)^*}$$.  

  $$\begin{align*}
    (\lambda f)^* (y) &= \sup_x x^\top y - \lambda f^*(x) \\
                                &= \lambda \sup_x x^\top (y/\lambda) - f^*(x) \\
                                &= \lambda f^*(y/\lambda) 
  \end{align*}$$

  With this result, we have

  $$\begin{align*}
    \mathrm{Prox}_{(\lambda f)^*}(x) &= \mathrm{argmin}_u \hspace{2pt} \lambda f^*(u/\lambda) + \frac{1}{2}\|u - x\|_2^2  \\
                                          &= \mathrm{argmin}_u \hspace{2pt} \frac{1}{\lambda}f^*(u / \lambda) + \frac{1}{2}\|\frac{u}{\lambda}  - \frac{x}{\lambda}\|_2^2 \\
                                          &= \lambda \mathrm{argmin}_u f^*(u)/\lambda + \frac{1}{\lambda}\|u - x/\lambda\|^2_2 \\
                                          &= \lambda \mathrm{Prox}_{f^*/\lambda}(x/\lambda)
  \end{align*}$$

which leads to the desired result by using these equalities after applying the basic Moreau identity to $$(\lambda f)$$. 

### Douglas-Rachford
Consider again the optimization problem 

$$\min_{x}{f(x)+ g(x)}$$

where $$f$$ and $$g$$ are closed, convex functions. Initialize a $$y_0$$. Douglas-Rachford iteration looks at iterating 

$$\begin{align*}
    y_{k + 1} &= \frac{1}{2} (I + C_{\partial f} \circ C_{\partial g}) y_k \\
                  &= y_k/2 + \mathrm{Prox}_{g}(2\mathrm{Prox}_{g}(y_k) - y_k) - 2\mathrm{Prox}_g (y_k)/2 + y_k/2  \\
                  &= y_k + \mathrm{Prox}_{f}(2\mathrm{Prox}_{g}(y_k) - y_k)
\end{align*}$$

Set $$x_{k+1} = \mathrm{Prox}_g(y_k)$$. Then the final algorithm is 

$$\begin{align}
  \begin{cases}
    x_{k + 1} = \mathrm{Prox}_g (y_k) \\
    y_{k+1} = y_k + \mathrm{Prox}_f (2x_{k+1} - y_k) - x_{k+1} 
  \end{cases}
\end{align}$$

Then we see that $$y_k$$ tends to a fixed point $$y^\star$$, and that the final solution will be given by $$x^\star = R_{\partial g}(y^\star) = \mathrm{Prox}_g(y^\star)$$ which is an implicit step we take at the algorithm, so we perform function evaluations at no extra cost during our iterations. Douglas-Rachford is also commonly stated in the following form, which can be obtained by flipping the update order to $$y_{k+1} = y_k + \mathrm{Prox}_f (2x_{k} - y_k) - x_{k}$$, and then letting $$u_{k+1} = \mathrm{Prox}_g (2x_{k+1} - y_k)$$ and $$w_k = x_k - y_k$$, which leads to the updates 

$$\begin{align}
  \begin{cases}
    u_{k+1} = \mathrm{Prox}_g ( x_k + w_k) \\ 
    x_{k+1} = \mathrm{Prox}_f (u_{k+1} - w_k) \\
    w_{k+1} = w_k + x_{k+1} - u_{k+1}
  \end{cases}
  \end{align}$$

In a sense, we may think of $$w$$ as a running sum of residuals.