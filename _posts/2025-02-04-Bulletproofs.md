---
layout: post
mathjax: true
title: Bulletproofs & Range Proof in Monero using Bulletproofs
---

{% include mathjax.html %}

##### Notes
$\mathbb F_p$ is a Finite Field. We use an Elliptic Curve $E(\mathbb F_p)$ over this field. The order of the curve is $q$ & the discrete log problem is hard in the Group of the Curve. When we sample any random numbers in this post, please assume it's sampled from $\mathbb F_q$. 

The Prover is $\mathcal P$ & the Verifier $\mathcal V$.
### Prerequisites

#### Pedersen Commitments
The basic Pedersen Commitment has been covered [here](/Monero/#pedersen-commitments) in the earlier Monero post. 

##### Pedersen Vector Commitments
I use the following notations for vectors. The arrow on top denotes that it is a vector & not just a single value

$\overrightarrow m = [m_0, m_1, m_2, m_3]$

For convenience, I represent vectors horizontally in this post except in case of vector polynomials where I represent the vector coefficients vertically.

I represent a vector of exponentiated scalars as 

$\overrightarrow {an} = [a^0, a^1, a^2, ..., a^n]$ 

Pedersen Commitments can be extended to commit a vector of elements. 

The Pedersen Commitment for the vector  $\overrightarrow m$ is 

$C_m = m_0 G_0 + m_1 G_1 + m_2 G_2+ m_3 G_3 + bB$

Here $b$ is the blinding factor & $B, G_0, G_1, G_2, G_3$ are all generators & the relation between the different Generators is not known.

The above commitment can also be written as  

$C_m =  \overrightarrow m  \overrightarrow G + bB$

where $\overrightarrow G = [G_0, G_1, G_2, G_3]$ & is called the "basis" committing $\overrightarrow m$

This can also be extended for committing multiple vectors in a single commitment

$C_{mn} = \overrightarrow m \overrightarrow H + \overrightarrow n \overrightarrow G + bB$

##### Pedersen Polynomial Commitments
Throughout this post, I present protocols & proofs as interactive ones, but they would be used with the [Fiat-Shamir heuristic](https://en.wikipedia.org/wiki/Fiat%E2%80%93Shamir_heuristic) to make them non-interactive. 

$p(x) = c_0 + c_1 x + c_2 x^2$

In order to commit to the polynomial, $\mathcal P$ makes a separate commitment to each of the coefficients & sends them to $\mathcal V$.

$C_0 = c_0G + b_0 B$

$C_1 = c_1G + b_1 B$

$C_2 = c_2G + b_2 B$

where $b_0, b_1, b_2$ are randomly sampled blinding factors

$\mathcal P$ can do a zero knowledge opening of this polynomial commitment with the following Evaluation Proof

- $\mathcal V$ randomly samples $u$ & sends it to $\mathcal P$.
- $\mathcal P$ evaluates $p(x)$ at $u$, $p_u = p(u) = p_0 + p_1u + p_2u^2$.

- $\mathcal P$ also computes $b =  b_0 +  b_1 u +  b_2 u^2$.

- $\mathcal P$ sends $p_u$ & $b$ to $\mathcal V$.
- $\mathcal V$ checks if $C_0 + C_1u + C_2u^2   \stackrel {?}{=} p_uG + bB$.

If it is, then $\mathcal V$ accepts that the evaluation $p(u)$ is $p_u$ & that $\mathcal P$ knows the polynomial $p(x)$ by the [Schwartz-Zippel Lemma](/Plonk/#the-schwartzzippel-lemma).

This works because 

$p_uG + bB\space= (p_0 + p_1u + p_2u^2)G + (b_0 +  b_1 u +  b_2 u^2)B$

$\qquad\qquad =  p_0G + (p_1G + b_1B)u + (p_2G + b_2B )u^2 + b_0B)$

$\qquad\qquad =  C_0+ C_1u+C_2u^2$

#### Zero Knowledge Polynomial Product Proof

$l(x) = l_0 + l_1x$

$r(x) = r_0 + r_1x$

$t(x) = l(x) r(x) = (l_0 + l_1x)(r_0 + r_1x)$

$\qquad = l_0 r_0  + (l_0 r_1 + l_1 r_0)x + l_1 r_1 x^2$ 

Let $t_0  = l_0 r_0, t_1 = (l_0 r_1 + l_1 r_0), t_2 = l_1 r_1 $

$t(x) = t_0 + t_1x + t_2x^2$

$\mathcal P$ wants to prove that $t(x) = l(x)r(x)$

To prove this, she provides these proofs.

$1)$ Evaluation Proofs for $t(x), l(x)$ & $r(x)$ separately using the method used in the previous section.

- $\mathcal P$ sends commitments to the coefficients of each of the 3 polynomials 
(total 7 commitments).

- $\mathcal V$ sends random $u$.

- $\mathcal P$ evaluates $l_u = l(u), r_u = r(u), t_u = t(u)$. $\mathcal P$ sends these values to $\mathcal V$.

- $\mathcal V$ checks the evaluation proof for each of the 3 polynomials using the method described in the earlier section & rejects the proof it doesn't verify.

$2)$ Proof that evaluation of $t$ at a random point is equal to the product of the evaluations of $l$ & $r$ at the same point.

- $\mathcal V$ checks if $t_u \stackrel {?}{=} l_u r_u$ and accepts the proof if it is.

This involves $\mathcal P$ sending 7 commitments & $\mathcal V$ checking 4 equalities. This can be optimized as below.

##### Optimized Proof

In the earlier section, $\mathcal V$ verified $l(x)$ & $r(x)$ separately, but this can be optimized by verifying both in a single step.

Commitment to $l(x)$ can be looked at as a commitment to the constant term (the term $x^0$ - i.e. the co-efficient with no $x$ terms) linearly combined with the linear term (term with $x^1$) combined in a [linearly independent](/PLONKWHY/#linear-independence) way.

$C_{l(x)} = C_{l_0} + C_{l_1}x$

$C_{r(x)} = C_{r_0} + C_{r_1}x$

Both can be combined as 

$S = C_{l(x=u)} + C_{r(x=u)} = C_{l_0} +C_{r_0} + C_{l_1}u + C_{r_1}u = C_0 + C_1u$ 

So instead of 4 commitments, $\mathcal P$ makes 2 commitments for $l$ & $r$.

$C_0 = l_0 G + r_0 H + b_0 B$

$C_1 = l_1 G + r_1 H + b_1 B$

Commitments for $t(x)$ remain like before.

$C_{t_0} = l_0 r_0 G + b_{t_0}B$

$C_{t_1} = (l_0 r_1 + l_1 r_0) G + b_{t_1}B$

$C_{t_2} = l_1 r_1 G + b_{t_2}B$

$\mathcal P$ sends 5 commitments to $\mathcal V$.

$\mathcal V$s sends $u$ like before & $\mathcal P$ evaluates $l, r, t$ at & $u$ and sends the evaluations $l_u, r_u$ & $t_u$ to $\mathcal V$. She also sends $b_t = b_{t_0} + b_{t_1}u + b_{t_2}u^2$ & $b_{lr} = b_0 + u$.

$\mathcal V$ computes 

$S = C_0 + C_1\space u$

$\mathcal V$ checks if $S \stackrel {?}{=} l_u G +r_eH + b_{lr}B$ - which is equivalent to checking both $l(x)$ & $(x)$.

This works if $\mathcal P$ is honest because  

$C_0 + C_1\space u = l_0G + r_0H + b_0B+ l_1uG + r_1uH + b_1uB$

$\qquad\qquad = (l_0 + l_1u)G + (r_0 + r_1u)H + (b_0 + b_1u)B$

$\qquad\qquad = l_u G +r_uH + b_{lr}B$

Other checks remain the same as before i.e. evaluation proof of $t(x)$ & proof that $t_u = l_u r_u$.

#### Inner Product

Two vectors, 

$\overrightarrow m =[ m_0, m_1, m_2]$

$\overrightarrow n =[ n_0, n_1, n_2]$

The Inner Product of these 2 vectors is

$<\overrightarrow m, \overrightarrow n> = m_0 n_0 + m_1 n_1 + m_2 n_2  $

i.e. the corresponding elements of the 2 vectors are multiplied with each other & then added up. Note that the output of an inner product is a scalar/single value & not a vector. An inner product is also called a dot product in some contexts.

A Vector Commitment can also be represented as an Inner Product.

$C_m = bB +  m_0\space G_0 + m_1\space G_1 + m_2\space G_2+ m_3\space G_3$

The above can be written as an inner product.

$C_m= bB + <\overrightarrow m, \overrightarrow G>$

A Polynomial $p(x) = p_0 + p_1x + p_2x^2 + ... + p_nx^n$

can be written as $p(x) = <\overrightarrow p, \overrightarrow {x^n}>$ where $\overrightarrow p$ is the vector of coefficients $[p_0, p_1, p_2, ..., p_n]$.

The evaluation of $p$ at $u$ would be $<\overrightarrow p, \overrightarrow{u^n}>$.

#### Hadamard Product
A Hadamard product of 2 vectors is represented as 

$\overrightarrow m \circ \overrightarrow n = [m_0  n_0, \space\space  m_1  n_1, \space\space m_2  n_2]$

Note that unlike the inner product, the output of the Hadamard product is a vector. When the elements of the Hadamard product output are added together, you get the Inner Product.

Since the output a Hadamard Product is a vector, a Hadamard product can be an input to an inner product, i.e. 

$< \overrightarrow m \circ  \overrightarrow n, \space \space \overrightarrow a>$

If the hadamard product of $ \overrightarrow m$ & $\overrightarrow n$ gives us the vector $\overrightarrow p = [m_0  n_0, \space\space m_1  n_1, \space\space m_2  n_2 ]$, then the Inner Product $<\overrightarrow  m \circ \overrightarrow n , a>$ would be the same as $<\overrightarrow p, \overrightarrow a>$.

#### Product Algebra

Some rearranging of Inner Products & Hadamard Products which we will be using later.

- $\quad <\overrightarrow m,\space\space \overrightarrow p+\overrightarrow q> =  m_0 (p_0+q_0) + m_1 (p_1+q_1) + m_2 (p_2+q_2) $

$\qquad = m_0 p_0+m_0 q_0 + m_1 p_1+m_1 q_1 + m_2 p_2+m_2 q_2 $ 

$\qquad = (m_0 p_0 + m_1 p_1 + m_2 p_2 ) + (m_0 q_0 + m_1 q_1 + +m_2 q_2)$

$\qquad = <\overrightarrow m, \overrightarrow p> + <\overrightarrow m, \overrightarrow q>$

- The above also means 

$\quad <\overrightarrow m+\overrightarrow n, \overrightarrow p + \overrightarrow q> = <\overrightarrow m+\overrightarrow n,\overrightarrow p> + <\overrightarrow m+\overrightarrow n,\overrightarrow q>
= <\overrightarrow m, \overrightarrow p> + <\overrightarrow n,\overrightarrow p> + <\overrightarrow m, \overrightarrow q> + <\overrightarrow n,\overrightarrow q>$

- We can also rearrange an Inner product as an Inner Product containing a Hadamard Product.

$\quad <\overrightarrow m,\overrightarrow n> = m_0 n_0 + m_1 n_1 + m_2 n_2$

$\qquad = 1^0 ( m_0 n_0) + 1^1 ( m_1 n_1) + 1^2 ( m_2 n_2) $

$\qquad = <\overrightarrow {1^n}, \overrightarrow m\circ \overrightarrow n>$

- Consider a scalar $s$,

$\qquad s <\overrightarrow m, \overrightarrow n> = s  (m_0 n_0 + m_1 n_1 + m_2 n_2 )$

$\qquad = (s m_0)n_0 + (s m_1) n_1 + (s m_2) n_2$

$\qquad = <s\overrightarrow m, \overrightarrow n> = <\overrightarrow  m, s\overrightarrow n>$ 

- $\overrightarrow m = [m_0, m_1, m_2, m_3] = [m_0.1^0, m_1.1^1, m_2.1^2, m_3.1^3] = \overrightarrow m \circ \overrightarrow 1^4$

$\qquad$ So, $\overrightarrow m=\overrightarrow m \circ \overrightarrow 1^4$

- $\overrightarrow m \circ s1^4 = s\overrightarrow m\circ 1^4 = s\overrightarrow m$

- Consider $\overrightarrow m$ is made of two halves $\overrightarrow m_L$ & $\overrightarrow m_R$ 

$\qquad$ i.e. $\overrightarrow m = [m_0,m_1, m_2, m_3]$ 

$\qquad \overrightarrow m_L = [m_0, m_1]$ & $\overrightarrow m_R = [m_2, m_3]$

$\qquad$ and likewise $n_L$ & $n_R$ are the two halves of $n$, 

$\qquad$ If concatenation is represented by $\oplus$, then (Note that $\oplus$ is different than $+$)

$\qquad \overrightarrow m = \overrightarrow m_L \oplus \overrightarrow m_R$ & $\overrightarrow n = \overrightarrow n_L \oplus \overrightarrow n_R$ 

then 
$<\overrightarrow m, \overrightarrow n> = m_0 n_0 + m_1 n_1 + m_2 n_2 + m_3 n_3  = <\overrightarrow m_L, \overrightarrow n_L> + <\overrightarrow m_R, \overrightarrow n_R>$

#### Vector Polynomial Commitments

A vector polynomial is a polynomial each of whose coefficients is a vector.

$\overrightarrow p(x) = \begin{bmatrix} 2 \cr 3 \cr 4 \end{bmatrix} + \begin{bmatrix} 4 \cr 5 \cr 6 \end{bmatrix} x + \begin{bmatrix} 6 \cr 7 \cr 8 \end{bmatrix} x^2$

Let us evaluate $\overrightarrow p(x)$ at a point $x=11$.

$\overrightarrow p(11) = \begin{bmatrix} 2 \cr 3 \cr 4 \end{bmatrix} + \begin{bmatrix} 4 \cr 5 \cr 6 \end{bmatrix} 11 + \begin{bmatrix} 6 \cr 7 \cr 8 \end{bmatrix} 11^2$

$\qquad\qquad = \begin{bmatrix} 2 \cr 3 \cr 4 \end{bmatrix} + \begin{bmatrix} 411 \cr 511 \cr 611 \end{bmatrix}  + \begin{bmatrix} 6 11^2 \cr 711^2 \cr 811^2 \end{bmatrix} $

$\qquad\qquad = \begin{bmatrix} 139 \cr 61 \cr 194 \end{bmatrix} $

<div class="boxed">

The evaluation of a vector polynomial is a vector.

</div>

<hr style="clear:both;">

$\overrightarrow l(x) = \overrightarrow l_0  + \overrightarrow l_1x$

$\overrightarrow r(x) = \overrightarrow r_0+ \overrightarrow r_1x$

$t(x) = <\overrightarrow l(x), \overrightarrow r(x)>$

$\qquad = <\overrightarrow l_1, \overrightarrow r_1> + <\overrightarrow l_0, \overrightarrow r_1>x + <\overrightarrow l_1, \overrightarrow r_0>x + <\overrightarrow l_0, \overrightarrow r_0>x^2$ 

Since the inner product of vectors is a scalar, each of the co-efficients of $t(x)$ would also be a scalar & hence $t(x)$ is a regular polynomial & not a vector polynomial.

$\overrightarrow l(x) = \begin{bmatrix}3 \cr 5\end{bmatrix} + \begin{bmatrix}2\cr 4\end{bmatrix}x $

$\overrightarrow r(x) = \begin{bmatrix} 2\cr 7\end{bmatrix} + \begin{bmatrix} 1 \cr 3\end{bmatrix} x$

$t(x) = <\overrightarrow l(x), \overrightarrow r(x)> = <\begin{bmatrix}3\cr 5\end{bmatrix},\begin{bmatrix} 2\cr 7\end{bmatrix}> + (<\begin{bmatrix}2\cr 4\end{bmatrix}, \begin{bmatrix} 2\cr 7\end{bmatrix}> + <\begin{bmatrix}3\cr 5\end{bmatrix},\begin{bmatrix} 1\cr 3\end{bmatrix}>)x + <\begin{bmatrix}2\cr 4\end{bmatrix},\begin{bmatrix}1\cr 3\end{bmatrix}>x^2$

$t(x) = 41  + 50x + 14 x^2$

Since each of the co-efficients of a Vector Polynomial is a vector, a $\mathcal P$ can make a Pedersen Vector Commitment to each of the Vector Co-efficients of the Polynomial and use same method we used for  proving a Vector Polynomial Product. 

### Bulletproofs

Bulletproofs are a short proof without a trusted setup to prove the Inner Product Argument (IPA).

Bulletproofs lets $\mathcal P$ convince $\mathcal V$ that she knows 2 vectors  $\overrightarrow m, \overrightarrow n $,  each of length $N$ such that their commitment is 

$S = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H$

and

$c = <\overrightarrow m, \overrightarrow n>$

$\overrightarrow G$ & $\overrightarrow H$ are the bases for $\overrightarrow m$ & $\overrightarrow n$ respectively

<div class="boxed">

The commitment $S$ is a binding commitment but not a hiding commitment because there is no blinding factor. 
</div>

<hr style="clear:both;">

Instead of proving these 2 statements separately, $\mathcal P$ wants to combine them into one statement & prove it such that proving the one statement will prove both of the above. Since $S$ is an Elliptic Curve point, we will convert $c$ also to a point before combining them.

Let $V$ be another unrelated Generator. We both sides of the 2nd statement by $V$.

$cV = <\overrightarrow m, \overrightarrow n>V$

$\mathcal P$ has to prove 2 statements

- $S = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H$

- $cV = <\overrightarrow m, \overrightarrow n>V$

$\mathcal P$ sends $S$ & $c$ to $\mathcal V$. $\mathcal V$ samples a random value $r$ & sends it to $\mathcal V$. 

The 2 statements can be combined in a [linearly independent way](/PLONKWHY/#linear-independence) using the randomness ($r$).

$S + crV = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H + <\overrightarrow m, \overrightarrow n>rV$

Because the 2 terms are linearly independent, if $\mathcal P$ proves that the Left Hand Side is equal to the Right Hand Side, then it would prove that with a very high probability that $S = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H$ and $c = <\overrightarrow m, \overrightarrow n>$.

Let $U = rV$ ($U$ is now a new generator which is unrelated to the 2 sets of generators denoted by $\overrightarrow H$ & $\overrightarrow G$) & let $P = S + cU$.

Since $\mathcal V$ knows $S$ & $c$, he can compute $P$.

So $\mathcal P$ now has to prove that 

$P = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H + <\overrightarrow m, \overrightarrow n>U$

Let us say we didn't include the random $r$ sent by $\mathcal V$ while combining the 2, then the proof wouldn't be sound. $\mathcal P$ could prove that $c_{fake} = <\overrightarrow m, \overrightarrow n>$ even though the actual value is c. Let's see how. 

Instead of sending the real commitment $S$, $\mathcal P$ could construct a value

$S_{fake} = S + cV - c_{fake}V$

& send $S_{fake}$ to $\mathcal V$ along with $c_{fake}$

Now when $\mathcal V$ constructs $P$, it would be constructed thus

$P = S_{fake} + c_{fake}V$

which would become

$P = S + cV - c_{fake}V + c_{fake}V$

i.e. 

$P = S + cV$ which can be proven true even though $c_{fake} \ne <\overrightarrow m, \overrightarrow n>$. Hence after $\mathcal P$ sends $S$ & $c$ to $\mathcal V$, $\mathcal V$ sends him random $r$ which is used to combine the 2 parts in a linearly independent way.

#### The Protocol

$\mathcal P$ has to prove 

$P = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H + <\overrightarrow m, \overrightarrow n>U$

where the length of each of the vectors is $N$.

$\mathcal P$ can send $\overrightarrow m$ & $\overrightarrow n$ to $\mathcal V$ & $\mathcal V$ can compute the Inner Product & verify that it's equal to $v$. However, she would to have send $2N$ elements. The algorithm aims to reduce the number of elements sent for the proof.

$\mathcal P$ splits the 2 vectors into half, 
$\overrightarrow m_L$ is the left half of $\overrightarrow m$ & $\overrightarrow m_R$ is the right half. Each of these 2 vectors is of size $N_1 = \frac {N}{2}$. Likewise, $\overrightarrow n_L$ & $\overrightarrow n_R$.

Similarly, each of the Generator vectors is  split into $2$ i.e.  $\overrightarrow G_L \text{ and }\overrightarrow G_R, \overrightarrow H_L \text{ and } \overrightarrow H_R$.

$P = \overrightarrow m_L \overrightarrow G_L + \overrightarrow m_R  \overrightarrow G_R + \overrightarrow n_L \overrightarrow H_L + \overrightarrow n_R \overrightarrow H_R+ <\overrightarrow m, \overrightarrow n>U$

$<\overrightarrow m,\overrightarrow n> = <m_L, n_L> + <m_R, n_R>$ 

<div class="boxed">

$Note: \overrightarrow m = \overrightarrow m_L \oplus \overrightarrow m_R$ and  $\overrightarrow m \ne \overrightarrow m_L + \overrightarrow m_R$

$\oplus$ & $+$ are different.

</div>

<hr style="clear:both;">

So,
$P = \overrightarrow m_L \overrightarrow G_L + \overrightarrow m_R  \overrightarrow G_R + \overrightarrow n_L \overrightarrow H_L + \overrightarrow n_R \overrightarrow H_R+ (<m_L, n_L> + <m_R, n_R>)U$

$\mathcal P$ computes $L_1$ & $R_1$ & sends them to $\mathcal V$.

$L_1 = \overrightarrow m_L \overrightarrow G_R + \overrightarrow n_R \overrightarrow H_L + <\overrightarrow m_L, \overrightarrow n_R>U$

$R_1 = \overrightarrow m_R \overrightarrow G_L + \overrightarrow n_L \overrightarrow H_R + <\overrightarrow m_R, \overrightarrow n_L>U$

$\mathcal V$ samples a random $x_1$ & sends it to $\mathcal P$.

Let $P_1 = x_1^2 L_1 + P + x_1^{-2} R_1$

We will now substitute the values of $L_1, R_1$ and $P$ into $P_1$

$P_1 = x_1^2 (\overrightarrow m_L \overrightarrow G_R + \overrightarrow n_R \overrightarrow H_L + <\overrightarrow m_L, \overrightarrow n_R>U) $

$\qquad + \overrightarrow m_L \overrightarrow G_L + \overrightarrow m_R  \overrightarrow G_R + \overrightarrow n_L \overrightarrow H_L + \overrightarrow n_R \overrightarrow H_R+ (<\overrightarrow m_L, \overrightarrow n_L> + <\overrightarrow m_R, \overrightarrow n_R>)U $

$\qquad +  x_1^{-2} 
(\overrightarrow m_R \overrightarrow G_L + \overrightarrow n_L \overrightarrow H_R + <\overrightarrow m_R, \overrightarrow n_L>U)$

<br>

$\qquad =(\overrightarrow m_L + x_1^{-2} \overrightarrow m_R) \overrightarrow G_L +  (x_1^2 \overrightarrow m_L  + \overrightarrow m_R ) \overrightarrow G_R + (x_1^2  \overrightarrow n_R  + \overrightarrow n_L) \overrightarrow H_L+ (x_1^{-2} \overrightarrow n_L + \overrightarrow n_R) \overrightarrow H_R$

$\qquad +(x_1^2 <\overrightarrow m_L, \overrightarrow n_R> + <\overrightarrow m_L, \overrightarrow n_L> + <\overrightarrow m_R, \overrightarrow n_R> + x_1^{-2}  <\overrightarrow m_R, \overrightarrow n_L>)U$ 

<br>

$\qquad = (x_1 \overrightarrow m_L + x_1^{-1} \overrightarrow m_R) x_1^{-1} \overrightarrow G_L+ (x_1 \overrightarrow m_L + x_1^{-1}\overrightarrow m_R)x_1 \overrightarrow G_R+ ( x_1 \overrightarrow n_R  + x_1^{-1}\overrightarrow n_L) x_1 \overrightarrow H_L+ (x_1^{-1} \overrightarrow n_L + x_1\overrightarrow n_R) x_1^{-1} \overrightarrow H_R$

$\qquad +(x_1^2 <\overrightarrow m_L, \overrightarrow n_R> + <\overrightarrow m_L, \overrightarrow n_L> + <\overrightarrow m_R, \overrightarrow n_R> + x_1^{-2}  <\overrightarrow m_R, \overrightarrow n_L>)U$ 

<br>

$\qquad = (x_1 \overrightarrow m_L + x_1^{-1} \overrightarrow m_R) (x_1^{-1} \overrightarrow G_L + x_1 \overrightarrow G_R) + ( x_1 \overrightarrow n_R  + x_1^{-1}\overrightarrow n_L)(x_1 \overrightarrow H_L + x_1^{-1} \overrightarrow H_R) $

$\qquad +( <x_1 \overrightarrow m_L, x_1 \overrightarrow n_R> + <x_1 \overrightarrow m_L, x_1^{-1}\overrightarrow n_L> + <x_1^{-1}\overrightarrow m_R, x_1 \overrightarrow n_R> +   <x_1^{-1}\overrightarrow m_R, x_1^{-1}\overrightarrow n_L>)U$ 

<br>

$ P_1 = (x_1 \overrightarrow m_L + x_1^{-1} \overrightarrow m_R) (x_1^{-1} \overrightarrow G_L + x_1 \overrightarrow G_R) + ( x_1 \overrightarrow n_R  + x_1^{-1}\overrightarrow n_L)(x_1 \overrightarrow H_L + x_1^{-1} \overrightarrow H_R)  +( <x_1 \overrightarrow m_L  + x_1^{-1} \overrightarrow m_R>, <x_1^{-1} \overrightarrow n_L + x_1 \overrightarrow n_R>)U$ 

Let 

$\overrightarrow m_1 = x_1 \overrightarrow m_L + x_1^{-1} \overrightarrow m_R$

$\overrightarrow n_1 = x_1 \overrightarrow n_R  + x_1^{-1}\overrightarrow n_L$

Both $\mathcal P$ & $\mathcal V$ also compute new bases

$\overrightarrow G_1 = x_1^{-1} \overrightarrow G_L + x_1 \overrightarrow G_R$

$\overrightarrow H_1 = x_1 \overrightarrow H_L + x_1^{-1} \overrightarrow H_R$

So, proving $P = \overrightarrow m \overrightarrow G + \overrightarrow n \overrightarrow H + <\overrightarrow m, \overrightarrow n>U$ has reduced to proving 

$P_1 = \overrightarrow m_1 \overrightarrow G_1 + \overrightarrow n_1 \overrightarrow H_1 + <\overrightarrow m_1, \overrightarrow n_1>U$

$\mathcal P$ can prove the proof for $P_1$ by sending $\overrightarrow m_1$ & $\overrightarrow n_1$ & $\mathcal V$ can check the inner product. The number of elements to be sent has reduced to $2N_1 = N$ (i.e. $N_1$ elements in each vector) which is half of what she would have to send for proving the proof with $P$.

Instead, she can repeat the steps after dividing $\overrightarrow m_1$ & $\overrightarrow n_1$ again into left & right vectors each & $\mathcal V$ sending a new $x_1$. She keeps repeating these steps till in the final round (i.e. round $f = log (N)$), she has to prove

$P_{f} = m_f G_f +  n_f  H_f + m_f  n_fU$

Here, each of $m_f$ & $n_f$ are single elements & not vectors & hence she has to send only 2 elements to $\mathcal V$. $\mathcal V$ can compute the inner product & verify if $P_f$ is as claimed. If it is, that would mean $P_{f-1}$ is as claimed by $\mathcal P$ in the previous round. As we continue unrolling to the beginning, it would prove that $P$ is as claimed by $\mathcal P$, thus ending the proof.

So, the instead of sending 2 vectors $\overrightarrow m$ & $\overrightarrow n$ each of size $s$, $\mathcal P$ only sends 
$L_1, R_1, L_2, R_2, ..., m_f, n_f$ & the final product $c_f = m_f n_f$.

So, Bulletproofs has reduced the number of elements $\mathcal P$ needs to send to $\mathcal V$ from $2N$ to $2log(N)$.

#### Optimization

In the protocol, we said that $\mathcal V$ computes new bases in each round. For e.g. in round 1

$\overrightarrow G_1 = x_1^{-1} \overrightarrow G_L + x_1 \overrightarrow G_R$

& similarly for computing $\overrightarrow H_1$ from $\overrightarrow H_L$ & $\overrightarrow H_R$.

Similarly in round 2, $\overrightarrow G_2$ would be computed from $\overrightarrow G_{1L}$ & $\overrightarrow G_{2R}$. And $\overrightarrow H_2$ also similarly.

However, $\mathcal V$ doesn't really do anything with these updated bases in each round because he doesn't do any checks till the final round. He needs the final updated bases only in the final round. So, he saves his $x_i$s from each round & finally computes the final $G_l$ & $H_l$ which will be a function of our starting $\overrightarrow G$ & $\overrightarrow H$ & all the $x_i$s.

##### Soundness 

In each round, we used a random $x_i$ & scaled up the left halves by $x_i$ & we scaled down the right half by $x_i^{-1}$. This is done so that $\mathcal P$ cannot fake a proof. We saw earlier how without using a random $r$ to create $U = rV$, $\mathcal P$ could prove $c_{fake} = <\overrightarrow m, \overrightarrow n>$ by crafting a $S_{fake}$. Similarly in each round, $\mathcal P$ instead of sending the correctly computed $L$ & $R$ could craft $L_{fake}$ & $R_{fake}$ such that everything matches & if he does this in each round, the final $<m_l, n_l>$ can be made to equal $c_{fake}$. To prevent this, in each round, after $\mathcal P$ sends $L_i$ & $R_i$, $\mathcal V$ samples a random $x_i$ sends it to $\mathcal P$. And then $\mathcal P$ scales the left & right halves of that round by $x_i$ & $x_i^{-1}$ respectively.

### Range Proof in Monero

In the [Monero Post](/Monero/#ringct), we saw how validators can verify that the sum of the inputs of a transaction is greater than the sum of the outputs even though the amounts of the values are hidden using Pedersen Commitments. This check by itself is not enough because of wraparound which happens in Finite Fields. Consider a transaction with 2 inputs & 2 outputs. Alice uses 2 UTXOs of value $10$ & $15$ to send Bob $17$ moneros & sends back change of $7$ to herself. 

$in_1 = 10, in_2 = 15, out_1 = 17, out_2 = 7$

Because Pedersen Commitments are homomorphic, we can check if $C_{in_1} + C_{in_2} > C_{out_1} + C_{out_2}$ & the above transaction would verify. 

However, the scalars in Monero & other blockchains operate in a finite field & not in the field of reals or integers. The actual field is a very large field but for ease of understanding, let's assume they operate in the field $\mathbb F_{7841}$

Now, if Alice sets $out_1 = 30$ & $out_2 = 7835$. In the field $\mathbb F_{7841}$, $30 + 7835 = 24$, so the validation would work! So, Alice spent 2 UTXOs of value $10$ & $15$ & sent Bob $17$ & also managed to get back $7835$ Moneros as change - i.e. she now possesses a UTXO worth $7835$ - she has created Moneros out of thin air.

So, we need to verify if each amount hidden by a commitment is in a particular range of values or not. On the blockchain, each amount needs to be between $0$ and $2^{64} - 1$.

#### Constructing the Inner Product

$\mathcal P$ has provided a Pedersen commitment to an output value $v$. She also has to prove that $v \in \lbrace 0, 1, ..., 2^{64} -1 \rbrace$.

To prove this, she first represents $v$ as a binary vector. Let's take an example of $v = 101$ (in decimal).

This can be represented as a vector of bits

$12^0 + 0 2^1 + 12^2 + 0 2^3 + 0 2^4 + 1 2^5 + 1 2^6 = 101$

$\overrightarrow a_L = [1,0,1,0,0,1,1]$ 

Let's create another vector $\overrightarrow a_R$ which is the complement of $\overrightarrow a_L$ - i.e. we create $\overrightarrow a_R$ by subtracting each element of $ \overrightarrow a_L$ from a bit vector of all $1$s (denoted as $\overrightarrow{1^n}$).

$\overrightarrow a_R = \overrightarrow{1^n} - \overrightarrow a_L$

$\overrightarrow a_R = [0, 1, 0,1,1,0,1,1]$

To prove $v \in \lbrace 0, 1, ..., 2^{64} -1 \rbrace$, we have to prove 3 things.

$1)$ Prove $\overrightarrow a_L$ is a vector representing the decomposition of $v$.

i.e. $v = \overrightarrow a_L[0]2^0 + \overrightarrow a_L[1] 2^1 + ...$

This can be represented as an inner product of 2 vectors. So, we have to prove this inner product

$<\overrightarrow a_L, \overrightarrow {2^n}> = v$

$2)$ Prove each element of $\overrightarrow a_L$ is a $0$ or a $1$ - i.e. the decomposition was a bitwise decomposition.

If you compare the vectors $\overrightarrow a_L$ & $\overrightarrow a_R$, you will see that if you multiply each element of $\overrightarrow a_L$ with the corresponding element of $\overrightarrow a_R$, then the product will be $0$ only if the element of $\overrightarrow a_L$ was either a $0$ or a $1$.

Let's say one of the elements of $\overrightarrow a_L$ was 5, then the corresponding element of $\overrightarrow a_R$ would be $-4$ & hence the multiplication of the two would not be $0$.

So, proving that each such product is $0$ will  prove that $\overrightarrow a_L$ is a bit vector.

Is it enough to check if $<\overrightarrow a_L, \overrightarrow a_R> = 0$?

No, because if one of the element-wise product is say $4$ & a different element wise product is $-4$, then they would cancel each other out & the inner product could end up as $0$ even if each individual product was not $0$.

Let's say each vector is of size $4$, i.e. 

$\overrightarrow a_L = [l_0, l_1, l_2, l_3]$

$\overrightarrow a_R = [r_0,r_1, r_2, r_3]$

We have already determined that it would not be enough to check if $l_0 r_0 + l_1 r_1 + l_2 r_2 + l_3 r_3  \stackrel {?}{=} 0$.

We have to first combine all individual product terms in a linearly independent way. After $\mathcal P$ commits to the vector, $\mathcal V$ samples a random variable $y$ & sends it to $\mathcal V$. The vector $\overrightarrow {y^4} = [y^0, y^1, y^2, y^3]$ is a linearly independent set. 

This makes the individual terms of $l_0 r_0 y^0,\space \space l_1 r_1 y^1,\space \space   l_2 r_2  y^2,\space \space  l_3 r_3 y^3$ also linearly independent, so if their sum is $0$, then it means each of the $l_i r_i$ is $0$.

The Hadamard Product $\overrightarrow a_R \circ \overrightarrow{y^n} = [r_0 y^0 \space\space, r_1  y^1\space\space, r_2  y^2\space\space, r_3  y^3]$.

So, the inner product

$<\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}> = l_0r_0 y^0 + l_1r_1 y^1 + l_2r_2 y^2 + l_3r_3y^3$

So, it's sufficient to check if $<\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}> \stackrel {?}{=} 0$. If it is, then it means that $v$'s decomposition into $\overrightarrow  a_L$ is indeed a bitwise decomposition.

$3)$ Prove $\overrightarrow a_R = \overrightarrow{1^n} - \overrightarrow a_L$
i.e. each element of $\overrightarrow a_R$ is a bitwise complement of the corresponding element of $\overrightarrow a_L$.

If each element of the vector $\overrightarrow a_L - \overrightarrow {1^n} - \overrightarrow a_R$ is $0$, then it means that $\overrightarrow a_R$ is the bitwise complement of $\overrightarrow a_L$. Again, we combine each element of this vector using the linearly independent set of $\overrightarrow {y^n}$ to create an inner product.

So, we have to prove that $<\overrightarrow a_L - \overrightarrow {1^n} - \overrightarrow a_R, \space\space \overrightarrow {y^n}> = 0$.

So, we have 3 things to prove

- $<\overrightarrow a_L, \overrightarrow {2^n}>  = v$

- $<\overrightarrow a_L - \overrightarrow {1^n} - \overrightarrow a_R, \space\space \overrightarrow {y^n}> = 0$

- $<\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}>= 0$

To transform 3 checks into one, we can again combine it in a linearly independent way. 

$\mathcal V$ samples a random $z$ & sends it to $\mathcal P$. $\mathcal P$ makes the 3 equations linearly independent by multiplying with the set $[z^0, z^1, z^2]$ and combines them

- $z^2 <\overrightarrow a_L, \overrightarrow {2^n}>  = z^2  v$

- $z^1  <\overrightarrow a_L - \overrightarrow{1^n} - \overrightarrow a_R, \space\space \overrightarrow{y^n}> = 0$

- $z^0  <\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}>= 0$

$$ z^2 <\overrightarrow a_L, \overrightarrow {2^n}>  + z  <\overrightarrow a_L - \overrightarrow{1^n} - \overrightarrow a_R, \space\space \overrightarrow{y^n}> + <\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}> = z^2  v$$

Simplifying, we get

$$z^2  v = z^2 <\overrightarrow a_L, \overrightarrow{2^n}>  + z  <\overrightarrow a_L, \overrightarrow{y^n}> -  z  <\overrightarrow {1^n}, \overrightarrow{y^n}> - z  <\overrightarrow a_R, \overrightarrow{y^n}> +  <\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}>  $$

$$ z^2  v +  z <\overrightarrow {1^n}, \overrightarrow {y^n} >= z^2 <\overrightarrow a_L, \overrightarrow {2^n}>  + z  <\overrightarrow a_L, \overrightarrow {y^n}> - z  <\overrightarrow {1^n}, \overrightarrow a_R \circ \overrightarrow {y^n}> +  <\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}>  $$

$$ z^2  v +  z <\overrightarrow {1^n}, \overrightarrow {y^n} >=  <\overrightarrow a_L, z^2 \space \overrightarrow {2^n}>  +  <\overrightarrow  a_L, z \overrightarrow { y^n}> +  <-z \overrightarrow {1^n}, \overrightarrow a_R \circ \overrightarrow {y^n}> +  <\overrightarrow a_L, \space\space \overrightarrow a_R \circ \overrightarrow {y^n}>  $$

$$ z^2  v +  z <\overrightarrow {1^n}, \overrightarrow {y^n} >=  <\overrightarrow a_L, \space\space z^2 \overrightarrow {2^n} +  z \overrightarrow {y^n} + \overrightarrow a_R \circ \overrightarrow {y^n}> + <-z\overrightarrow {1^n}, \overrightarrow a_R \circ \overrightarrow{y^n}>  $$

$$ z^2  v +  z <\overrightarrow {1^n}, \overrightarrow {y^n} >=  <\overrightarrow a_L, \space\space z^2 \overrightarrow {2^n} +  z \overrightarrow {y^n} + \overrightarrow a_R \circ \overrightarrow {y^n}> + <-z \overrightarrow{1^n}, \overrightarrow a_R \circ \overrightarrow{y^n}>  $$

Adding $< -z \overrightarrow{1^n}, z^2 \overrightarrow{2^n } +  z\overrightarrow {y^n}>$ to both sides,

$$ z^2  v +  z <\overrightarrow {1^n}, \overrightarrow {y^n} > + < -z\overrightarrow {1^n}, z^2 \overrightarrow{2^n } +  z\overrightarrow{y^n}> =  <\overrightarrow a_L, \space\space z^2 \overrightarrow{2^n } +  z\overrightarrow{y^n} + \overrightarrow a_R \circ \overrightarrow {y^n}> + <\overrightarrow -z\overrightarrow {1^n}, z^2 \overrightarrow{2^n } +  z\overrightarrow{y^n>} + <-z \overrightarrow {1^n}, \overrightarrow a_R \circ\overrightarrow{ y^n}>$$

The Right Hand Side of the above can be simplified as 

RHS $  =  <\overrightarrow a_L -z \overrightarrow{1^n},  \space\space \overrightarrow {y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n})+ z^2 \overrightarrow{2^n} > $

Let's check the Left Hand side 

LHS = $z^2  v +  z <\overrightarrow {1^n}, \overrightarrow {y^n} > + < -z\overrightarrow {1^n}, z^2 \overrightarrow{2^n } +  z\overrightarrow{y^n}>$

All terms except the $z^2 v$ consists of either constants or $y$ and $z$ terms. $\mathcal V$ knows $y$ & $z$ & the constants, so let's express it as a function $f$

$f(y,z) = z <\overrightarrow {1^n}, \overrightarrow {y^n} > + < -z\overrightarrow {1^n}, z^2 \overrightarrow{2^n } +  z\overrightarrow{y^n}>$

$f(y,z)$ can be computed by $\mathcal V$ on his own.

$$z^2  v + f(y,z) = <\overrightarrow a_L -z \overrightarrow{1^n},  \space\space \overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n})+ z^2 \overrightarrow{2^n }> $$

Let 

$\overrightarrow {ul} = \overrightarrow a_L -z \overrightarrow{1^n}$

$\overrightarrow {ur} = \overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n})+ z^2 \overrightarrow{2^n }$

So $\mathcal P$ has to prove that 

$<\overrightarrow {ul}, \overrightarrow {ur}> = z^2  v + f(y,z)$

#### Blinding

However, if $\mathcal P$ creates commitments to $\overrightarrow {ul}$ & $\overrightarrow {ur}$ & runs the Bulletproof protocol to prove the above, it wouldn't be zero knowledge because as we noted earlier, in the Bulletproofs protocol, the commitment to the vectors $\overrightarrow m$ & $\overrightarrow n$ didn't have a Blinding factor. Hence $\mathcal P$ first blinds $\overrightarrow {ul}$ & $\overrightarrow {ur}$.

$\mathcal P$ randomly samples 2 blinding vectors $\overrightarrow s_L$ & $\overrightarrow s_R$, same size as the unblinded vectors & linearly combines them with the unblinded vectors to form 2 blinded terms, $\overrightarrow a_L + \overrightarrow s_Lx$ & $\overrightarrow a_R + \overrightarrow s_Rx$.

We replace the vectors in $\overrightarrow {ul}$ & $\overrightarrow {ur}$ with the  blinded vectors. However, by doing this, they no longer remain vectors, they become  vector polynomials.

$\overrightarrow l(x) = \overrightarrow a_L + s_Lx -z \overrightarrow{1^n}$

$\overrightarrow r(x) = \overrightarrow{y^n}\circ (\overrightarrow a_R + s_Rx + z \overrightarrow{1^n})+ z^2 \overrightarrow{2^n }$

Let

$\overrightarrow l_0 = \overrightarrow a_L -z \overrightarrow{1^n}$

$\overrightarrow l_1 = \overrightarrow s_L$

So, $\overrightarrow l(x) = \overrightarrow l_0 + \overrightarrow l_1x$

$\overrightarrow r(x) = \overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n}) + z^2 \overrightarrow{2^n } + \overrightarrow{y^n}\circ s_Rx  $

$\overrightarrow r_0 = \overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n}) + z^2 \overrightarrow{2^n } $

$\overrightarrow r_1 = \overrightarrow{y^n}\circ s_R  $

So, $\overrightarrow r(x) = \overrightarrow r_0 + \overrightarrow r_1x$

Let $t(x) = <\overrightarrow l(x), \overrightarrow r(x)>$

(The inner product of 2 vector polynomials results in a regular polynomial.)

$t(x) = <\overrightarrow l_0 + \overrightarrow l_1x,\overrightarrow r_0 + \overrightarrow r_1x>$

$\qquad =<\overrightarrow l_0,\overrightarrow r_0> + (<\overrightarrow l_0,\overrightarrow r_1> + <\overrightarrow l_1,\overrightarrow r_0>)x + <\overrightarrow l_1,\overrightarrow r_1>x^2$

If $t(x) = t_0 + t_1x + t_2x^2$

$t_2 = <\overrightarrow l_1,\overrightarrow r_1>$

$t_1 = <\overrightarrow l_0,\overrightarrow r_1> + <\overrightarrow l_1,\overrightarrow r_0>$

$t_0 = <\overrightarrow l_0,\overrightarrow r_0> = <\overrightarrow a_L -z \overrightarrow{1^n},\overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n}) + z^2 \overrightarrow{2^n }>$

#### The Proof

$\mathcal P$ uses steps similar to that used in the optimized method showed in the [Zero Knowledge Polynomial Multiplication Proof](./#zero-knowledge-polynomial-product) section to prove that $t(x) = <\overrightarrow l(x), \overrightarrow r(x)>$. The Polynomials multiplied here are Vector Polynomials & not regular ones & hence Vector Pedersen commitments would be used. There are also other changes & optimizations.

$\mathcal P$ needs to provide the following proofs.

$1)$ Evaluation Proof that $t(x) =\overrightarrow t_0 + \overrightarrow t_1x + \overrightarrow t_2x^2$

$2)$ Evaluation Proof of $\overrightarrow l(x)$ & $\overrightarrow r(x)$

$3)$ Proof that $t_u = <\overrightarrow l_u, \overrightarrow r_u>$

$\mathcal P$ creates a hiding & blinding commitment to $\overrightarrow a_L$ & $\overrightarrow a_R$ & another one to $\overrightarrow s_L$ & $\overrightarrow s_R$ 

$C_a = \overrightarrow a_L \overrightarrow G + \overrightarrow a_R \overrightarrow H + b_a B $ 

$C_s = \overrightarrow s_L \overrightarrow G + \overrightarrow s_R \overrightarrow H + b_s B $ 

She creates commitments to $t_1$ & $t_2$

$C_{t_1} = \overrightarrow t_1 \overrightarrow G + b_{t_1} B$

$C_{t_2} = \overrightarrow t_2 \overrightarrow G + b_{t_2} B$

$\mathcal P$ sends $C_a, C_s, C_{t_1}$ & $C_{t_2}$ to $\mathcal V$. $\mathcal V$ of the Monero Transaction already has the [amount commitment](/jMonero/#amount-commitments) $C_v$ of the value ($v$) whose range we are proving 

$C_v = v G + b_vB $

$\mathcal V$ samples random $u$ & sends it to $\mathcal P$.

$\mathcal P$ evaluates $\overrightarrow l_u = \overrightarrow l(x=u), \overrightarrow r_u = \overrightarrow r(x=u)$ & $ t_u = t(x=u)$ & computes $b_{lr} = b_a + ub_s$ and $b_t = z^2 b_v + b_1u + b_2u^2$.

$\mathcal P$ sends $t_u, b_{lr}$ & $b_t$ to $\mathcal V$.

We write the evaluation of $l(x)$ & $r(x)$ at $u$ as $\overrightarrow l_u$ & $\overrightarrow r_u$ respectively & not as $l_u$ & $r_u$ because as we noted earlier, the evaluation of a vector polynomial is a vector & not a scalar.

#####  Evaluation Proof for $t(x)$

$\mathcal P$ has sent commitments $C_{t_1}, C_{t_2}$ & also $b_t, t_u$.

$\mathcal V$ can compute a commitment to $t_u$ himself

$C_{t_u} = t_uG + b_tB$

$\mathcal V$ checks if 

$C_{t_u} \stackrel {?}{=} z^2C_v + f(y,z)G + uC_{t_1} + u^2C_{t_2}$

$\mathcal V$ rejects the proof if the equality doesn't check out.

##### Proof of correctness of $\overrightarrow l(x)$ & $\overrightarrow l(x)$ & that $<t_u = \overrightarrow l(x), \overrightarrow r(x)>$

Like in the section about the optimized proof for [Regular Polynomial Products](#zero-knowledge-polynomial-product-proof), $\mathcal V$ has to construct $S = C_0 + uC_1$ & verify it ($C_0$ is a commitment to the constant terms of both the left & right vector polynomials & $C_1$ to the linear terms). However, unlike the earlier case, he doesn't already have the full $C_0$ or $C_1$ but only a commitment of some of the terms in them through $C_a$ & $C_s$. But he can create the commitments to the remaining terms himself so that $\mathcal P$ doesn't have to send those.

To enable $\mathcal V$ to compute commitments to some terms above like $(\overrightarrow {y^n} o \overrightarrow a_R)$ by himself, Bulletproofs uses a trick.

$\mathcal V$ creates a new vector of generators
$\overrightarrow {H'} = \frac {\overrightarrow H}{\overrightarrow {y^n}}  = [\frac {H_0}{y^0}, \frac {H_1}{y^1}, \frac {H_2}{y^2}, ...]$

So now, $\overrightarrow H = \overrightarrow {y^n} \overrightarrow {H'}$.

Substituting the above into $C_a$ & $C_s$,  we get

$C_a = \overrightarrow a_L G + \overrightarrow (a_R\space o\space \overrightarrow {y^n}) \overrightarrow {H'} + b_aB$

$C_s = \overrightarrow s_L \overrightarrow G + (\overrightarrow s_R \circ \overrightarrow {y^n})\overrightarrow {H'}-b_sB$

So,

$C_a - b_aB =  \overrightarrow a_L G + \overrightarrow (a_R\space o\space \overrightarrow {y^n}) \overrightarrow {H'}$

$C_s -b_sB = \overrightarrow s_L \overrightarrow G + (\overrightarrow s_R \circ \overrightarrow {y^n})\overrightarrow {H'}$

Now $\mathcal V$ constructs a commitment for $C_0$ & $C_1$ with bases $\overrightarrow G$ & $\overrightarrow {H'}$.

$C_0 = \overrightarrow a_L \overrightarrow G - z\overrightarrow{1^n} + (\overrightarrow a_R \circ \overrightarrow {y^n})\overrightarrow {H'} + (z\overrightarrow {1^n}\circ \overrightarrow {y^n})\overrightarrow {H'} + z^2 \overrightarrow{2^n } \overrightarrow {H'}$  

$C_1 = \overrightarrow s_L \overrightarrow G  +  ( \overrightarrow s_R \circ  \overrightarrow{y^n} ) \overrightarrow {H'}$

$\mathcal V$ computes $S = C_0 + uC_1$.

$S = C_0 + uC_1 = \overrightarrow a_L \overrightarrow G - z\overrightarrow{1^n} + (\overrightarrow a_R \circ \overrightarrow {y^n})\overrightarrow {H'} + (z\overrightarrow {1^n}\circ \overrightarrow {y^n})\overrightarrow H + z^2 \overrightarrow{2^n } \overrightarrow H + u(\overrightarrow s_L \overrightarrow G  +  ( \overrightarrow s_R \circ  \overrightarrow{y^n} ) \overrightarrow H$

Rearranging and also substituting $z\overrightarrow {1^n}\circ \overrightarrow {y^n} = z\overrightarrow{y^n}$

$S = (\overrightarrow a_L \overrightarrow G + (\overrightarrow a_R \circ \overrightarrow {y^n})\overrightarrow {H'}) - z\overrightarrow{1^n} + z\overrightarrow{y^n}\overrightarrow {H'} + z^2 \overrightarrow{2^n } \overrightarrow {H'} + u(\overrightarrow s_L \overrightarrow G  +  ( \overrightarrow s_R \circ  \overrightarrow{y^n} ) \overrightarrow {H'})$

Substituting $C_a - b_aB$ & $C_s -b_sB$ into the above

$S = C_a + uC_s - (b_a + ub_s)B - z\overrightarrow{1^n} + z\overrightarrow{y^n}\overrightarrow {H'} + z^2 \overrightarrow{2^n } \overrightarrow {H'}$ 

$\mathcal V$ knows $C_a, C_s$ & $\mathcal P$ has sent him $b_{lr} = b_a + ub_s$. The remaining terms can also be computed by $\mathcal V$ because he knows $z$ & $y$. So $\mathcal V$ computes $S$.

As we saw in the optimized method showed in the [Zero Knowledge Polynomial Multiplication Proof](./#zero-knowledge-polynomial-product) section, $S = C_{l(x=u)} + C_{r(x=u)}$.

So, $S$ is a commitment to $\overrightarrow l_u$ & $\overrightarrow r_u$.

$\mathcal V$ also sends a random $r$ & computes a new generator $U = rV$.

$\mathcal P$ & $\mathcal V$  construct

$P = S + t_uU$

i.e.

$P = \overrightarrow l_u \overrightarrow G + \overrightarrow r_u \overrightarrow H + t_uU$

So $\mathcal P$ can use the Bulletproofs protocol to prove to $\mathcal V$ that she knows a commitment to $\overrightarrow l_u$ & $\overrightarrow r_u$ and that $t_u = <\overrightarrow l_u, \overrightarrow r_u>$.

The simple proof for $t_u = <\overrightarrow l_u, \overrightarrow r_u>$ is to send $t_u$, $\overrightarrow l_u$ & $\overrightarrow r_u$ to $\mathcal V$ & $\mathcal V$ checks if $t_u \stackrel {?}{=} <\overrightarrow l_u, \overrightarrow l_r>$. However, each of the vectors contains $N$ elements i.e. $2N$ elements would need to be sent by $\mathcal P$ to $\mathcal V$, so it won't be a succinct proof. So $\mathcal P$ doesn't send the vectors, but uses Bulletproofs which is logarithmic.

$\mathcal P$ has proved that $t(x) = t_0 + t_1x + t_2x^2 = <\overrightarrow l(x), \overrightarrow r(x)>$. If 2 polynomials are equal, it means each of their co-efficients is equal.

The constant co-efficient of $t(x)$ is equal to $z^2 v + f(y,z) $. The constant co-efficient of $
<\overrightarrow l(x), \overrightarrow r(x) >$ is $<\overrightarrow l_0,\overrightarrow r_0> = <\overrightarrow a_L -z \overrightarrow{1^n},\overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n}) + z^2 \overrightarrow{2^n }>$

So, this proves that 

$z^2 v + f(y,z) = \overrightarrow l_0\overrightarrow r_0 = <\overrightarrow a_L -z \overrightarrow{1^n},\overrightarrow{y^n}\circ (\overrightarrow a_R + z \overrightarrow{1^n}) + z^2 \overrightarrow{2^n }>$

Which is what we set out to prove. This concludes the Range Proof for Monero.
