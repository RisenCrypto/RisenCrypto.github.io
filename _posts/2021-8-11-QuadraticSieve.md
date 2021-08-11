---
layout: post
mathjax: true
title: The Quadratic Sieve algorithm for Integer Factorization

---

{% include mathjax.html %}

Some Background first. 

Most commonly used factorization methods today rely on a simple mathematical Identity    

$X^2 - Y^2 = (X+Y)(X-Y)$

To factor $N$ we look for an integer $b$ such that $N + b^2$ is a perfect square.

i.e. $N^2 + b^2 = a^2$  

So $N = a^2 - b^2$

$N = (a + b)(a - b)$  

So we have factored $N$.

For e.g. Let $N = 8051$   

Try $8051 + 1^2, 8051 + 2^2, 8051 + 3^2 .....$, till we hit

$8051 + 7^2 = 90^2$

So $8051 = (90 + 7)(90 - 7)$   

$= 83$ x $97$

$\sqrt(8051) = 89.73$ Both are factors are quite close to the square root.

The further away the 2 factors of $N$ are away from $\sqrt(N)$, the more difficult this method becomes. Only a small fraction of numbers have a factor close to their square root.

Let's take $N = 799$. It will take us trying from 1 to 15, till we find $799 + 15^2 = 32^2$

So $799 = 17 * 47$

Hoever, this can be simplified by using $3 * 799$ instead of 799.

$3 * 799 = 2397$  

Try $2397 + 1^2, 2397 + 2^2$ etc.   

We get a square right at the 2nd try

$2397 + 2^2 = 49^2$

A small change in this method is we now need $gcd(N, a + b)$ & $gcd(N, a - b)$ to find the factors

$gcd(799, 49 + 2) = 17$

So we get $799 = 17 * 47$   





