---
layout: post
mathjax: true
title: The Pohlig-Hellman Algorithm

---

{% include mathjax.html %}

The *Pohlig-Hellman Algorithm* helps solve the *Discrete Log Problem* for Finite Fields whose order can be factored into prime powers of smaller primes. The algorithm reduces the computation of the discrete log in the Finite Field $G$ to the computation of the discrete log in prime power order subgroups of $G$

For e.g. Order of $\mathbb F(p)=p-1=p_1^{n_1}.p_2^{n_2}.p_3^{n_3}...$

The PH algorithm allows your solve the DLP in the smaller subgroups of order $p_1^{n_1}, p_2^{n_2}, p_3^{n_3}$ etc and then combine the solutions using the *Chinese Remainder Theorem* to get the solution for the original DLP.    

---   

## The Algorithm


### The DLP 

Let $g$ be a generator in $\mathbb F(p)$

$y \equiv g^x \bmod p$

$y, p$ & $g$ are known. The discrete log problem here is to find $x$

**Reduce the DLP in the field into the DLPs in the prime power order subgroups**

The multiplicative operation of $\mathbb F(p)$ excludes the $0$ element & hence it's order is one less than the order of the field. So the order is $p-1$. Any composite number can be expressed as the product of prime powers. 

Order = $p-1=p_1^{n_1}.p_2^{n_2}.p_3^{n_3}...$

Since each of the prime powers ${p_i}^{n_i}$ divides the order of the group, by the Fundamental Theorem of Cyclic Groups, there will be a cyclic subgroup for each of the prime powers of order ${p_i}^{n_i}$ with the generator for the subgroup being $g^{\frac {p-1} {p_i^{n_i}}}$ 


Let's take the DLP & raise both sides by $\frac {p-1}{p_i^{n_i}}$. For the sake of simplicity, let $r_i = \frac {p-1}{p_i^{n_i}}$

$y^{r_i} \equiv ({g^x})^{r_i} \bmod p$


$y^{r_i} \equiv {(g^{r_i})}^x \bmod p$

$r_i = \frac {p-1}{p_i^{n_i}}$, so $g^{r_i}$ is the generator for the subgroup of order $p_i^{n_i}$.

Let this generator be called $g_i = g^{r_i}$. Also let $y_i = y^{r_i}$.

So,

$y_i = {g_i}^x \bmod p$.

Now since the order of $g_i$ is ${p_i}^{n_i}$, in the above equation, $x$ cannot have any value higher than ${p_i}^{n_i}$. We can write this is as 

$x_i = x \bmod {p_i}^{n_i}$

So, the DLP in the subgroup of order ${p_i}^{n_i}$ is

$y_i \equiv {g_i}^{x_i} \bmod p$.

We simplified the equation after raising using the new variables $y_i$, $g_i$ & $x_i$ to show that now it's a DLP problem in the subgroup. While solving however, we will use continue to use $y$ & $g$ along with the new variable $x_i$

i.e. $y^{\frac {p-1}{p_i^{n_i}}} = ({g^{\frac {p-1}{p_i^{n_i}}}})^{x_i} \bmod p$


**Expansion of $x_i$'s**

As we saw, $x_i$ is the solution for the subgroup of order ${p_i}^{n_i}$

$x_i = x \bmod {p_i}^{n_i}$

We expand $x_i$ as a base $p_i$ number.

Note: For e.g. if $p_i=2$, then we have to expand $x_i$ in base 2 (i.e. do a binary expansion). If $x_i = 13$, then in binary (base 2), we can write it as the bitstring $1101$.

So the binary expansion of $x=13$ will be

$x_i=13=1101=1 . 2^0 + 0. 2^1 + 1.2^2 + 1.2^3$

Since $x_i$ is $\bmod 2^{n_i}$, then the max value of $x_i$ can only be $2^n -1$. The number $2^n$ in binary representation needs $n+1$ bits. So the number $2^n -1$ will need $n_i$ bits. Hence when we expand $x_i$ in base 2, we will have $n_i$ co-efficients $\in \lbrace 0,1 \rbrace$

For any base other than 2, we do it similarly

$x_i=\sum_{j=0}^{n-1} a_j{p_i}^j$ where $a_j \in \lbrace 0,1, ...,{p_i}-1\rbrace$

$x_i=a_0+ a_1p_i + a_2{p_i}^2 +...+ a_{n-1}{p_i}^{n-1}$ where $a_j \in \lbrace 0,1,...,{p_i}-1\rbrace$

**Solving the DLP in the subgroup** 

Let's first solve it for the subgroup of order ${\frac {p-1}{p_1^{n_1}}}$

i.e. $y^{\frac {p-1}{p_i^{n_i}}} = ({g^{\frac {p-1}{p_i^{n_i}}}})^{x_i} \bmod p$

$y^{\frac {p-1}{p_i^{n_i}}} = g^{\frac {p-1}{p_i^{n_i}}{x_i}} \bmod p$

Raise both sides by $p_i^{n_i - 1}$


$y^{\frac {p-1}{p_i}} = g^{\frac {p-1}{p_i}{x_i}} \bmod p$

Substitute $x_i = {a_0+a_1{p_i}+a_2{p_i}^2+ ... + a_{n_i-1}{p_i}^{n_i-1} }$

$y^{\frac {p-1}{p_i}} = g^{\frac {p-1}{p_i}(a_0+a_1{p_i}+a_2{p_i}^2... + a_{n_i-1}{p_i}^{n_i-1})} \bmod p$

$y^{\frac {p-1}{p_i}} = g^{\frac {(p-1)a_0}{p_i}}. g^{(p-1)a_1}. g^{(p-1){a_2}{p_i}}...g^{(p-1){a_{n-1}}{p_i}^{n-2}}  \bmod p $

Other than the first term, all the remaining terms are of the form $g^{k(p-1).p^j}$. By *Fermat's Little Theorem*, all these terms evaluate to 1 & can be removed from the expression.

$y^{\frac {p-1}{p_i}} = g^{\frac {(p-1)a_0}{p_i}} \bmod p $


In the above equation, the only unknown is $a_0$ and $a_0 \in \lbrace 0,1,...,p-1 \rbrace$. It can be solved to get $a_0$ (using the BabyStep-GiantStep, Pollard's rho etc)

Now, that $a_0$ i known, let's start again to find other $a_i$s

$y^{\frac {p-1}{p_1^{n_1}}} = g^{\frac {p-1}{p_1^{n_1}}{x_1}} \bmod p$

Raise both sides by $p_1^{n_1 - 2}$


$y^{\frac {p-1}{p_1^{2}}} = g^{\frac {p-1}{p_1^{2}}{x_1}} \bmod p$

Substitute $x_1 = {a_0+a_1{p_1}+a_2{p_1}^2+ ... + a_{n_1-1}{p_1}^{n_1-1} }$

$y^{\frac {p-1}{p_1^2}} = g^{\frac {p-1}{p_1^2}(a_0+a_1{p_1}+a_2{p_1}^2... + a_{n_i-1}{p_1}^{n_1-1})} \bmod p$

$y^{\frac {p-1}{p_1^2}} = g^{\frac {(p-1)a_0}{p_1^2}}. g^{\frac {(p-1)a_1}{p_1}}. g^{(p-1){a_2}}...g^{(p-1){a_{n-1}}{p_i}^{n-3}}  \bmod p $

Let $m = g^{\frac {(p-1)a_0}{p_1^2}}$ ($a_0$ is now a known value).



So $m = (g^{\frac {p-1}{p_1^2}})^{a_0}$.



 Since $p_1^2$ is a factor of the order of the group, $g^{\frac {p-1}{p_1^2}}$ is a generator of a subgroup. Hence $m$ is an element of the field & has an inverse $m^{-1}$ in the field.


So,

$y^{\frac {p-1}{p_1^2}}.m^{-1} = g^{\frac {(p-1)a_1}{p_i}}. g^{(p-1){a_2}}...g^{(p-1){a_{n-1}}{p_i}^{n-3}}  \bmod p $

Other than the first term on the Right Hand Side, all the remaining terms are of the form $g^{k(p-1).p^j}$. By *Fermat's Little Theorem*, all these terms evaluate to 1 & can be removed from the expression.

$y^{\frac {p-1}{p_i^2}}.m^{-1} = g^{\frac {(p-1)a_1}{p_i}} \bmod p $

In the above equation, the only unknown is $a_1$ and $a_1 \in \lbrace 0,1,...,p-1 \rbrace$. Like before, it can be solved to get $a_1$

We can keep repeating these steps to get all values from $a_2$ to $a_{n-1}$, there by finding $x_i$.

So now we know $x_1$.

$x = x_1 \bmod p_1^{n_1}$


Repeat the procedure for subgroups of order $p_2^{n_2}$,$p_3^{n_3}$ etc.

We will then have all the $x_i$s

$x \equiv x_1 \bmod {p_1}^{n_1}$

$x \equiv x_2 \bmod {p_2}^{n_2}$

$...$

$...$

$...$

The *Chinese remainder theorem* can be used to combine the above to find $x$ for $\bmod p$

So we have solved the DLP for the group by solving the DLP for smaller subgroups & combining them.   

---  

We did an initial step of raising the original DLP by $\frac {p-1}{p_i^{n_i}}$ to change $x$ to $x_i$

$y^{\frac {p-1}{p_i^{n_i}}} = ({g^{\frac {p-1}{p_i^{n_i}}}})^{x_i} \bmod p$

And then for $i=1$, we raised it to $p_i^{n_i - 1}$ which got us

$y^{\frac {p-1}{p_i}} = g^{\frac {p-1}{p_i}{x_i}} \bmod p$

We can combine the above 2 steps into directly raising the original DLP to $\frac {p-1}{p_i}$ & changing $x$ to $x_i$ for finding $a_0$.

So let's summarise the steps in the Algorithm

1) Raise the original DLP to $\frac {p-1}{p_i}$ & changing $x$ to $x_i$ 

2) Remove all $a_i$ terms other than $a_0$ using Fermat's Little Theorem

3) Find $a_0$

4) And then for finding $a_1$, we raise the original DLP to $\frac {p-1}{p_i^2}$

5) Remove all $a_i$ terms after $a_1$ using Fermat's Little Theorem

6) Find $a_1$

7) Continue steps 4 to 6 to find all $a_i$ (You would need to raise the DLP to $\frac {p-1}{p_i^3}$, $\frac {p-1}{p_i^4} etc$) 

8) You now have found the first $x = x_i \bmod p$

9) Find similar $x_i$s for other prime power subgroups

10) Combine all the $x_i$s with CRT to find $x$ & solve the DLP.


## A numerical example

**The Discrete Log Problem:**

$7531 \equiv 6^x \bmod 8101$

Find $x$

**Solution:**

$7531 \equiv 6^x \bmod 8101$ 

Order = $8101 - 1 = 8100 = 2^2.3^4.5^2$. 

Let's first start with the 2nd prime power subgroup which is of order $3^4$ (you can start with any subgroup).

For this subgroup, $p_i = 3$ & $n_i = 4$

Raise the original DLP to $\frac {p-1}{p_i}$ i.e. $\frac{8100}{3} = 2700. And also replace $x$ by $x_1$ 


$7531^{2700} \equiv 6^{x_1} \bmod p$

Expanding $x_1$ in base 3, we get

$x_2=a_0+3a_1+9a_2+27a_3$ where $a_i \in \lbrace 0,1,2\rbrace$

After substituting this, we can cancel out the terms involving everything except $a_0$ using Fermat's Little Theorem.

$7531^{2700} \equiv 6^{2700a_0} \bmod p$

$LHS = 7531^{2700} = pow(7531, 2700, 8101) = 2217$

$2700 \equiv 6^{2700a_0} \bmod 8101$

Since the only variable here is $a_0$ & $a_0 \in \lbrace 0, 1, 3 \rbrace$, we can solve & get 

$a_0=2$

Now, $x_1=2+3a_1+9a_2+27a_3$ 

$7531\equiv 6^{2+3a_1+9a_2+27a_3} \bmod p$

$7531 \equiv 6^2 . 6^{3a_1+9a_2+27a_3} \bmod p$


$7531 . 6^{-2} \equiv 6^{3a_1+9a_2+27a_3} \bmod p$

Using sage, 

$pow(6, -2, 8101) = 7876$ and $mod(7876*7531,8101) = 6735$

So, $6735= \equiv 6^{3a_1+9a_2+27a_3} \bmod p$

We raise both sides to $\frac {p-1}{p_i^2}$ i.e. $\frac {8100}{9} = 900$ & also cancel out all $a_i$ terms other than $a_1$ using Fermat's little theorem.

$6735^{900} \equiv 6^{2700a_1} \bmod p$

$LHS = pow(6735, 900, 8101) = 1$

$1 \equiv 6^{2700a_1} \bmod 8101$

$a_1=0$


Now, $x_2=2+9a_2+27a_3$ 

Raising to $\frac {8100}{3^3}$ i.e.$300$, we can solve similarly to get 

$a_2 =2$

Raising by $\frac {8100}{3^4}$ i.e.$100$, we can solve similarly to get 

$a_3 = 1$

This gives us $x_2 = 47$. So 

$x \equiv 47 \bmod 81$  [Solution for the DLP in $3^4$]

Next, we solve in the subgroup or order $2^2$ to get 

$x\equiv 1 \bmod 4$ 

and subgroup of order $5^2$ to get 

$x\equiv 14 \bmod 25$

So we have a solution for each of the 3 subgroups we are interested in


$x \equiv 1 \bmod 4$

$x \equiv 47 \bmod 81$

$x \equiv 14 \bmod 25$

Using the Chinese Remainder Theorem, we can combine it to get

$CRT\text_list([1, 47, 14],[4,81,25]) = 6689$

So, 
$x \equiv 6689 \bmod 8100$

We can write the above congruence also as

$x = 8100k + 6689$

Substituting the above in the original DLP, we get

$7531 \equiv 6^{8100k+6689} \bmod 8101$

$7531 \equiv 6^{8100k} . 6^{6689} \bmod 8101$

By Fermat's Little Theorem, $6^{8100k} \bmod 8101$ is 1

$7531 \equiv 6^{6689} \bmod 8101$

**So $x=6689$ is the solution for the DLP**  

---   

## Pohlig-Hellman for Elliptic Curve Cryptography   


Pohlig-Hellman is also similarly applicable for the Discrete Log Problem in ECC.

The differences are mainly

1. Groups are additive groups & not multiplicative groups.

2.  We used Fermat's Little Theorem for solving multiplicative Group DLP. Fermat's Little Theorem is a special case of Lagrange's Theorem. Lagrange's Theorem implies (among other things) that for an additive Group $G$, if $m$ is the order of the group, then for every $g \in G$, $m*g=0$

---  

**Numerical example for ECDLP**

$E:y2=x3+1001x+75 \bmod 7919$

Order of the Curve = $m=7889$

$P=E(4023,6036)$

$Q=E(4135,3169)$

$xP=Q$

Find $x$

**Solution:**

7889 can be factored as

$7889=7^3 . 23$

Solving in subgroup $7^3$


Multiply both sides of the DLP by $\frac {m}{7}$, replace $x$ with $x_1$



$\frac {m}{7}Q=x_1 \frac{m}{7}P$

Expand $x_1$ in base 3

$x_1=a_0+7a_1+7^{2}{a_2}$

Substitute this in the equation & also remove all terms after $a_0$ by using Lagrange's Theorem


$1127Q=(1127a_0)P$ where $a_0 \in \lbrace 0,1,2,3,4,5,6 \rbrace$

Only one variable here $a_0$. Can be solved to get

$a_0=1$

$Q=(1+7a_1+7^{2}a_2).P$

$Q-P=(7a_1+7^{2}a_2).P$

Multiply both sides by $\frac{m}{7^2}$

$\frac{m}{7^2}(Q-P)=(7a_1+7^2a_2).\frac{m}{7^2}P$

Again, by Lagrange's Theorem we can remove the $7^2$ term.

$161(Q-P)=1127a_1P$

We solve this to get
$a_1=3$

$Q=(1+7*3+{7^2}a_2)P$

$Q=(22+7^2{a_2})P$

$Q-22P=7^2a_2P$

Multiply both sides by $\frac{m}{7^3}$

$\frac {m}{7^3}(Q-22P)=7^2a_2 \frac {m}{7^3}P$

$23(Q-22P)=1127a_2P$

Solving, we get

$a_2=4$

So $x_1=1+7 . 3+49 . 4=218$

$x\equiv 218 \bmod 7^3$

Likewise, solving in subgroup $23$

$x \equiv 10 \bmod 23$

So we have solutions for the 2 subgroups

$x \equiv 218 \bmod 7^3$

$x \equiv 10 \bmod 23$

Using Chinese Remainder Theorem, we can combine this to get

$x \equiv 4334 \bmod 7889$

So the ECDLP is solved

---  

**Note:** the factors in both the numerical problems were very small, so just brute-forcing would be enough to get the coefficients for the subgroups. However, for larger factors, the BabyStep-GiantStep or Pollard's rho algorithms would be used.



[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FPohligHellman%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
