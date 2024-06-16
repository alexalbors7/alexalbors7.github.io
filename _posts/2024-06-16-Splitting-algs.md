---
layout: post
title: splitting algorithms
date: 2024-05-16 19:22:00
description: I discuss basics on proximal splitting schemes for convex optimization. The main motivation is to provide background in the methods used by (Papadakis et al, 2013) to solve Benamou-Brenier's formulation of the Wasserstein distance. 
tags: optimization  
related_posts: true
---

We develop algorithms aiming to solve the problem 
$$    
    \mathrm{min}_{x \in \mathcal{H}} f(x) + g(x)
$$

we do not place restrictions on the smoothness of $f$ or $g$ aside from convexity and continuity. 


