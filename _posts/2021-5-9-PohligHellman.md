---
layout: post
mathjax: true
title: The Pohlig-Hellman Algorithm for solving a special case of the Discrete Log Problem

---

{% include mathjax.html %}

The *Pohlig-Hellman Algorithm* helps solve the *Discrete Log Problem* for Finite Fields whose order can be factored into prime powers of smaller primes. The algorithm reduces the computation of the discrete log in the Finite Field $G$ to the computation of the discrete log in prime order subgroups of **⟨G⟩**

For e.g. Order of $GF(p)=p−1=p_1^{n_1}.p_2^{n_2}.p_3^{n_3}…$

The PH algorithm allows your solve the DLP in the smaller subgroups of order $p_1^{n_1}, p_2^{n_2}, p_3^{n_3}$ etc and then combine the solutions using the *Chinese Remainder Theorem* to get the solution for the original DLP.    

---   

## The Algorithm

Let $g$ be a generator in $GF(p)$

$y \equiv g^x \bmod p$

$y, p$ & $g$ are known. The discrete log problem here is to find $x$

Order of $GF(p)=p−1=p_1^{n_1}.p_2^{n_2}.p_3^{n_3}…$

Let’s take the prime power factors one by one.  

**First consider $p_1^{n_1}$***

Let $x$ be the solution for the subgroup of order $p_1^{n_1}$

First we expand $x$ as a base $p_1$ number.

Note: For e.g. if $p_1=2$, then we have to expand $x$ in base 2 (i.e. do a binary expansion). If x is 13, then in binary (base 2), we can write it as $1101$.

So the binary expansion of $x=13$ will be

$x=13=1101=1 . 2^0 + 0. 2^1 + 1.2^2 + 1.2^3$

If we are solving for $\bmod 2^n$, then the max value of $x$ can only be $2^n −1$. The number $2^n$ in binary representation needs $n+1$ bits. So the number $2^n −1$ will need $n$ bits. Hence when we expand $x$ in base 2, we will have $n$ co-efficients from $0$ to $n−1$

For any base, we do it similarly

$x=\sum_{i=0}^{n−1} a_i{p_1}^i$ where $a_i \in {0,…,p−1}$

$x=a_0+ a_1p_1 + a_2{p_1}^2 +…$

$y \equiv g^{a_0+ a_1{p_1}+ a_2{p_1}^2+…} \bmod p$ [substituting x by its expansion]

$y^{\frac{p−1}{p_1}} \equiv (g^{a_0+a_1{p_1}+a_2{p_1}^2+…})^{\frac {p−1}{p_1}} \bmod p$ [Raise both sides to $\frac {p−1}{p_1}$]

$y^{\frac {p−1}{p_1}} \equiv g^{(a_0+a_1{p_1}+a_2{p_1}^22+…)(\frac {p−1}{p_1})} \bmod p$

$y^{\frac {p−1}{p_1}} \equiv g^{a_0 \frac {p−1} {p_1}} . g^{a_1{p_1}\frac{p−1}{p_1}}.g^{a_2{p_1}^2 \frac{p−1}{p_1}}… \bmod p$   


All the other terms after $g^{a_0 \frac {p−1}{p_1}} \bmod p$ have a coefficient which is a multiple of $p_1$. So after cancelling out the $p_1$ from the denominator, these terms are of the form $g^{k(p−1)}$. By *Fermat’s Little Theorem*, all these terms evaluate to 1 & can be removed from the expression.

We are then left with

$y^{\frac{p−1}{p_1}} \equiv g^{a_0 \frac {p−1}{p_1}} \bmod p$

*In the above equation, the only unknown is $a_0$ and $a_0 \in {0,p−1}$. It can be solved to get $a_0$ (using the BabyStep-GiantStep, Pollard’s rho etc)*.

Now onto finding the other coefficients

Start again with $y \equiv g^x \bmod p$  



$y \equiv g^{a_0+a_1{p_1}+a_2{p_1}^2+…} \bmod p$

$y \equiv g^{a_0} . g^{a_1{p_1} + a_2{p_1}^2+ …} \bmod p$

$g$ is a generator, so $g^{a_0} is an element of $GF(p)$. So $g^{a_0}$ will have an inverse in $GF(p)$. So we can multiply both sides by the inverse.

$y.g^{−a_0} \equiv g^{a_1{p_1}+a_2{p_1}^2+…} \bmod p$

Now raise both sides to $\frac {p−1}{p^2}$ & follow a procedure similar to one we used for $a_0$ to find $a_1$

Keep repeating this till all the coefficients are known. Once all the coefficients are known, that solves $x$ for $\bmod {p_1}^{n_1}$

$x \equiv x_1 \bmod {p_1}^{n_1}$  

Repeat the procedure for $p_2$,$p_3$ etc.

So we have

$x \equiv x_1 \bmod {p_1}^{n_1}$

$x \equiv x_2 \bmod {p_2}^{n_2}$

$…$

$x \equiv x_i \bmod {p_i}^{n_i}$

The *Chinese remainder theorem* can be used to combine the above to find $x$ for $\bmod p$

So we have solved the DLP for the group by solving the DLP for smaller subgroups & combining them.   


---   

## A numerical example

Note: Some textbooks describe a handy method to use Pohlig-Hellman by creating a table to solve for the coefficients. However, in this post, I am not using that because the reason for this write-up is to explain the algorithm’s steps in detail. Also, I use sage math to do the intermediate steps (finding the factors, finding the inverse, calculating pow etc). I don’t show those calculations here.

**The Discrete Log Problem:**

$7531 \equiv 6^x \bmod 8101$

Find $x$

**Solution:**

Order of $GF(8101)=p−1=8100$

$8100=2^2.3^4.5^2$

Solving for subgroup $2^2$

$p_1 =2, n_1=2$

Expanding $x$ in base 2, we write

$x=a_0+2a_1$ where $a_0 \in {0,1}$

$7531 \equiv 6^{a_0+2a_1} \bmod p$

$7531^{ \frac {p−1}{2}} \equiv (6^{a_0+2a1})^{\frac {p−1}{2}} \bmod p$ [Raise both sides to $\frac {p−1}{p_1}$]

$7531^{4050} \equiv 6^{a_0 \frac {p−1}{2}} . g^{2a_1 \frac{p−1}{2}} \bmod p$

$7531^{4050} \equiv 6^{4050a_0} \bmod 8101$

LHS = pow(7531, 4050, 8101) = 8100

$8100 \equiv 6^{4050a_0} \bmod 8101$

Only one unknown above, $a_0 \in {0,1}$

It can be solved easily to get  

$a_0=1$


Substituting value of $a_0$ in $x$, we get

$x= 1+2a_1$

Substituting this in the DLP, we get

$7531 \equiv 6^{1+2a_1} \bmod p$

$7531 . 6^{−1} \equiv 6 \bmod p$

$7531 . 6751 \equiv 6 \bmod p$ [6751 is the inverse of 6 in GF(p)]

$8006 \equiv 6 \bmod p$

$8006^{\frac{p−1}{4}} \equiv 6^{2a_1\frac {p−1}{4}} \bmod p$ [Raise both sides to $\frac{p−1}{p^2}$]

$8006^2025 \equiv 6 \bmod 8101$

$1 \equiv 6^{4050a_1} \bmod 8101$

$a1=0$


Substituting values of $a_1$ & $a_2$ in $x=a_0+2a_1$, we get $x=1$.

$x\equiv \bmod 4$ [Solution for the DLP in $2^2$]

Solving in subgroup $3^4$

$p_2=3$, $n_2=4$

Expanding $x$ in base 3, we get

$x=a_0+3a_1+9a_2+27a_3$ where a_i \in {0,1,2}$

$7531 \equiv 6^{a_0+3a_1+9a_2+27a_3} \bmod p$

$7531^{\frac {p−1}{3}} \equiv 6^{(a_0+3a_1+9a_2+27a_3)^{\frac {p−1}{3}}} \bmod p$

$7531^{2700} \equiv 6^{2700a_0} \bmod p$

LHS = pow(7531, 2700, 8101) = 2217

$2700 \equiv 6^{2700a_0} \bmod 8101$

$a0=2$

Substituting value of $a_0$ in $x$, we get

$x= 1+2a_1$

Substituting this in the DLP, we get

$7531 \equiv 6^{1+2a_1} \bmod p$

$7531 . 6^{−1} \equiv 6 \bmod p$

$7531 . 6751 \equiv 6 \bmod p$ [6751 is the inverse of 6 in GF(p)]

$8006 \equiv 6 \bmod p$

$8006^{\frac{p−1}{4}} \equiv 6^{2a_1\frac {p−1}{4}} \bmod p$ [Raise both sides to $\frac{p−1}{p^2}$]

$8006^{2025} \equiv 6 \bmod 8101$

$1 \equiv 6^{4050a_1} \bmod 8101$

$a1=0$

Substituting values of $a_1$ & $a_2$ in $x=a_0+2a_1$, we get $x=1$.

$x\equiv \bmod 4$ [Solution for the DLP in $2^2$]

Solving in subgroup $3^4$

$p_2=3$, $n_2=4$

Expanding $x$ in base 3, we get

$x=a_0+3a_1+9a_2+27a_3$ where a_i \in {0,1,2}$

$7531 \equiv 6^{a_0+3a_1+9a_2+27a_3} \bmod p$

$7531^{\frac {p−1}{3}} \equiv 6^{(a_0+3a_1+9a_2+27a_3)^{frac{p−1}{3}}} \bmod p$

$7531^{2700} \equiv 6^{2700a_0} \bmod p$

LHS = pow(7531, 2700, 8101) = 2217

$2700 \equiv 6^{2700a_0} \bmod 8101$

$a0=2$


$x=2+3a_1+9a_2+27a_3$ where $a_i \in {0,1,2}$

$7531\equiv 6^{2+3a_1+9a_2+27a_3} \bmod p$

$7531 \equiv 6^2 . 6^{3a_1+9a_2+27a_3} \bmod p$

$7531 . 6^{−2} \equiv 6^{3a_1+9a_2+27a_3} \bmod p$

$6735^{\frac {p−1}{9}} \equiv 6^{3a_1+9a_2+27a_3}. \frac {p−1}{9} \bmod p$

$6735^{900} \equiv 6^{2700a_1} \bmod p$

LHS = pow(6735, 900, 8101) = 1

$1 \equiv 6^{2700a_1} \bmod 8101$

$a1=0$

$x=2+9a_2+27a_3$ where $a_3 \in {0,1,2}$

$7531\equiv 6^{2+ 9a_2+27a_3} \bmod p$

$7531\equiv 6^2 .6^{9a_2+27a_3} \bmod p$

$7531 . 6^{−2} \equiv 6^{9a_2+27a_3} \bmod p$

$6735^{\frac {p−1}{27}} \equiv 6^{(9a_2+27a_3)} ∗ \frac{p−1}{27} \bmod p$

$6735^{300} \equiv 6^{2700a_2} \bmod p$

LHS = pow(6735, 300, 8101) = 2217

$2217\equiv 6^{2700a_2} \bmod 8101$

$a2=2$

$x=2+18+27a_3$ where $a_i \in {0,1,2}$

$7531 \equiv 6^{20+27a_3} \bmod p$

$7531 \equiv 6^{20}.6^{27a_3} \bmod p$

$7531 . 6^{−20} \equiv 6^{27a_3} \bmod p$

$6992^{\frac {p−1}{81}} \equiv 6^{27a_3} . \frac {p−1}{81} \bmod p$

$6992^100 \equiv 6^{2700a_3} \bmod 8101$

LHS = pow(6992, 100, 8101) = 5883

$5883 \equiv 6^{2700a_3} \bmod 8101$

$a3=1$

$x \equiv 47 \bmod 81$

Solving in subgroup $5^2$

If we solve for $5^2$, we get

$x\equiv 14 \bmod 25$

So we have 3 solutions for the 3 subgroups

$x\equiv 1 \bmod 4$

$x \equiv 47 \bmod 81$

$x \equiv 14 \bmod 25$

Using the Chinese Remainder Theorem, we can combine it to get

$x \equiv 6689 \bmod 8100$

We can write the above congruence also as

x = 8100k + 6689

Substituting the above in the original DLP, we get

$7531 \equiv 6^{8100k+6689} \bmod 8101$

$7531 \equiv 6^{8100k} . 6^{6689} \bmod 8101$

By Fermat’s Little Theorem, $6^{8100k} \bmod 8101$ is 1

$7531 \equiv 6^{6689} \bmod 8101$

**So $x=6689$ is the solution for the DLP**  

---   

## Pohlig-Hellman for Elliptic Curve Cryptography   


Pohlig-Hellman is also similarly applicable for the Discrete Log Problem in ECC.

The differences are mainly

1. Groups are additive groups & not Multiplicative Groups.

2.  We used Fermat’s Little Theorem for solving multiplicative Group DLP. Fermat’s Little Theorem is a special case of Lagrange’s Theorem. Lagrange’s Theorem implies (among other things) that for an additive Group $G$, if $m$ is the order of the group, then for every $g \in G$, $m∗g=0$

---  

**Numerical example for ECDLP**

$E:y2=x3+1001x+75 \bmod 7919$

Order of the Curve = $m=7889$

$P=E(4023,6036)$

$Q=E(4135,3169)$

$xP=Q$

Find $x$

Solution

7889 can be factored as

$7889=7^3 . 23$

Solving in subgroup $7^3$

Expand $x$ in base 3

$x=a_0+7a_1+7^{2}{a_2}$

$Q=(a_0+7a_1+7^{2}{a_2})P$ [Substitute x by it’s expansion]

Multiply both sides by $\frac {m}{7}$

$\frac {m}{7}Q=(a_0+7a_1+7^2{a_2}) \frac{m}{7}P$

The 2nd & 3rd terms are factors of 7. So after multiplication by $\frac {m}{7}$, they are of the form mP. As we saw earlier, from Lagrange’s Theorem, this is equal to 0 & hence can be removed from the equation.

$1127Q=(1127a_0)P$ where $a_0 \in {0,1,2,3,4,5,6}$

Only one variable here $a_0$. Can be solved to get

$a0=1$

$Q=(1+7a_1+7^{2}a_2).P$

$Q−P=(7a_1+7^{2}a_2).P$

Multiply both sides by $\frac{m}{7^2}$

$\frac{m}{7^2}(Q−P)=(7a_1+7^2a_2).\frac{m}{7^2}P$

Again, by Lagrange’s Theorem we can remove the $7^2$ term.

$161(Q−P)=1127a_1P$

We solve this to get
$a_1=3$

$Q=(1+7∗3+{7^2}a_2)P$

$Q=(22+7^2{a_2})P$

$Q−22P=7^2a_2P$

Multiply both sides by $\frac{m}{7^3}$

$\frac {m}{7^3}(Q−22P)=7^2a_2 \frac {m}{7^3}P$

$23(Q−22P)=1127a_2P$

Solving, we get

$a2=4$

So $x=1+7 . 3+49 . 4=218$

$x\equiv 218 \bmod 7^3$

Solving in subgroup $23$

$x=a_0$

$Q=a_0P$

Multiply both sides by $\frac{m}{23}$

$\frac{m}{23}Q=23a_0P$

$343Q=343a_0P$

Solving, we get

$a0=10$

So $x=10$

$x \equiv 10 \bmod 23$

So we have solutions for the 2 subgroups

$x \equiv 218 \bmod 7^3$

$x \equiv 10 \bmod 23$

Using Chinese Remainder Theorem, we can combine this to get

$x \equiv 4334 \bmod 7889$

So the ECDLP is solved

---  

**Note:** the factors in both the numerical problems were very small, so just brute-forcing would be enough to get the coefficients for the subgroups. However, for larger factors, the BabyStep-GiantStep or Pollard’s rho algorithms would be used.

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FPohligHellman%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
