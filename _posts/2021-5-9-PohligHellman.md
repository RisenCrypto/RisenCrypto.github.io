---
layout: post
mathjax: true
title: The Pohlig-Hellman Algorithm for solving a special case of the Discrete Log Problem

---

{% include mathjax.html %}

The *Pohlig-Hellman Algorithm* helps solve the *Discrete Log Problem* for Finite Fields whose order can be factored into prime powers of smaller primes. The algorithm reduces the computation of the discrete log in the Finite Field $G$ to the computation of the discrete log in prime order subgroups of **⟨G⟩**

For e.g. Order of $GF(p)=p−1=p1^{n1}.p2^{n2}.p3^{n3}…$

The PH algorithm allows your solve the DLP in the smaller subgroups of order $p1^{n1}, p2^{n2}, p3^{n3} etc and then combine the solutions using the *Chinese Remainder Theorem* to get the solution for the original DLP.
