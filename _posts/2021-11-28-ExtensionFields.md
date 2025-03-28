---
layout: post
mathjax: true
title: Polynomials and Elliptic Curves over Extension Fields

---

{% include mathjax.html %}


## Extension Fields

Let $\mathbb F_p$ be a field. So $\mathbb F_p[x]$ is a Polynomial Ring. Let $f(x)$ be an irreducible polynomial of degree 2 or above in $\mathbb F_p[x]$ - i.e. the polynomial cannot be factored into 2 or more polynomials of degree 1 or more i.e. there is no $t$ in $\mathbb F_p$ such that $f(t) = 0$. However, there exists an extension field of $\mathbb F_p$ which contains a $t$ such that $f(t) = 0$. 


Let's consider the field $\mathbb F_2$ - the corresponding Polynomial Ring is $\mathbb F_2[x]$. An irreducible polynomial of degree 2 is $f(x) = x^2 + x + 1$. Let $t$ be the root of this polynomial in an extension field - so $f(t) = 0$ i.e. $t^2 + t + 1 = 0$. 

The elements of the extension field will be all terms in $t$ with co-efficients in $\mathbb F_2$ - i.e. $a_1t + a_0$ with $t$ taking values from $\mathbb F_2$. This extension field is called $\mathbb F_2(t)/\langle t^2 + t+1 \rangle$ and it would have 4 elements as below

$\mathbb F_2(t)/\langle t^2 + t+1 \rangle = \lbrace 0, 1, t, 1 + t\rbrace$

We need not consider polynomials with degree $2$ or higher because $t^2 + t + 1 = 0$, so

$t^2 = -t -1$

$t^2 = t + 1$ (because $-1 \bmod 2 \equiv 1)$

So any term with $t^2$, $t^3$ & higher could be reduced to degree less than 2.

For e.g. $t^2 + 1 = 1+t + 1 = 2 + t = t$ (because $2 \bmod 2 \equiv 0$)

$t^3 = t^2 \cdot t = (t+1)\cdot t = t^2 + t = 1 + t + t = 1$

So even if we consider terms of degree higher than 1, they will be equivalent to one of the 4 elements we have already listed.


This field can also be referred to as $\mathbb F_{2^2}$ or $\mathbb F_4$. We created $\mathbb F_{2^2}$ using the irreducible polynomial of degree 2 over $\mathbb F_2$. Likewise, we can construct any extension field $\mathbb F_{p^k}$ over $\mathbb F_p$ using the irreducible polynomial of degree $k$ over $\mathbb F_p$. Every element of the extension field $\mathbb F_{p^k}$ will be of degree $k-1$ or lessser i.e. of the form  $a_{k-1}t^{k-1} +a_{k-2}t^{k-2} +...+ a_{2}t^{2} +a_{1}t + a_0$ with $a_i \in \mathbb F_p$.

Let's list out the elements of the extension field $F_{2^4}$ - here the terms will be of the form $a_{3}t^3 + a_{2}t^2 + a_{1}t + a_0$ with each coefficient $a_i$ being equal to either 0 or 1 from $\mathbb F_2$. So there are $2^4 = 16$ possible permutations - taking the coefficients to be of the form "$a_3a_2a_1a_0$", there would 16 permutations. 

$0$ is $0000$ i.e. $0t^3 + 0t^2 + 0t^1 + 0t^0 = 0$

$1$ is $0001$ i.e. $0t^3 + 0t^2 + 0t^1 + 1t^0 = 1$

$2$ is $0010$ i.e. $0t^3 + 0t^2 + 1t^1 + 0t^0 = t$

....

$5$ is $0101$ i.e. $0t^3 + 1t^2 + 0t^1 + 1t^0 = t^2 + 1$

....

$10$ is $1010$ i.e. $1t^3 + 0t^2 + 1t^1 + 0t^0 = t^3 + t$

and so on & so forth.

So the 16 elements of $\mathbb F_{2^4}$ where the irreducible polynomial is $x^4 + x + 1$ are 

$\mathbb F_{2^4} = \lbrace 0 , t^2, t^3 , t^3 +t^2 , 1 , t^2 +1 , t^3 +1 , t^3 +t^2 +1, t , t^2 +t , t^3 +t , t^3 +t^2 +t , t +1 , t^2 +t +1 , t^3 +t +1 , t^3 +t^2 +t +1\rbrace$

corresponding to the bit representations of $\lbrace 0, 1, ...., 15 \rbrace$ in base 2.

Likewise $\mathbb F_{3^2}$ would be corresponding to the base 3 representations of $\lbrace 0, 1, 2, 3, 4, 5, 6, 7, 8\rbrace$ - i.e. $\lbrace 0, 1, 2,  t, t + 1, t+2, 2t, 2t +1, 2t+2\rbrace$


There is also another way of computing the elements of an extension field. Let's try it for $F_{2^4}$. An irreducible polynomial of degree 4 is $x^4 + x + 1$. So if $t$ is the root of this polynomial, then $t^4 + t + 1 =0 $

So,
$t^4 = -t -1= t + 1$ (because $-1 \bmod 2 \equiv 1$ ) 

So, $t^4 = t+1$

We can write out the list of elements 
of $\mathbb F_{2^4} = \lbrace 0, t, t^2, t^3, t^4, t^5, t^6, t^7, t^8, t^9, t^{10}, t^{11}, t^{12}, t^{13}, t^{14}, t^{15} \rbrace$

Now, we know $t^4 = t+1$

So,

$t^5 = t^4 \cdot t = (t+1)\cdot t = t^2 + t$

$t^6 = t^5 \cdot t = (t^2 + t) \cdot t = t^3 + t^2$

$t^7 = t^6 \cdot t = (t^3 + t^2)\cdot t =t^4 + t^3 = t+1 + t^3 = t^3 + t + 1$

We continue this way, till we get

$t^{14} = t^3 + 1$

& 

$t^{15} = (t^3 + 1)\cdot t = t^4 + t = (t + 1) + t = 1$

So,

$\mathbb F_{2^4} = \lbrace 0,
 t,
 t^2,
 t^3,
 t + 1,
 t^2 + t,
 t^3 + t^2,
 t^3 + t + 1,
 t^2 + 1,
 t^3 + t,
 t^2 + t + 1,
 t^3 + t^2 + t,
 t^3 + t^2 + t + 1,
 t^3 + t^2 + 1,
 t^3 + 1,
 1 \rbrace$

 Let's try this for $\mathbb F_{3^2}$

 $\mathbb F_{3^2} = \lbrace 0, t, t^2, t^3, t^4, t^5, t^6, t^7, t^8\rbrace$
 
The irreducible prolynomial here is $x^2 + 2x + 2$

So $t^2 + 2t + 2 = 0$

$t^2 = -2t -2 = t + 1 $ (because $-2 \bmod 3 \equiv 1$ ) 

So $\mathbb F_{3^2} = \lbrace 0, t, t^2, t^3, t^4, t^5, t^6, t^7, t^8\rbrace$

$t^2 = t +1$

$t^3 = t^2 + t = t + 1 + t = 2t + 1$

$t^4 = 2t^2 + t = 2t + 2 + t = 2$

$t^5 = 2t$

$t^6 = 2t^2 = 2t + 2$

$t^7 = 2t^2 + 2t = 2t + 2 + 2t = t + 2$

$t^8 = t^2 + 2t = t+1 + 2t = 1$


So,

 $\mathbb F_{3^2} =\lbrace 0, t, t + 1, 2t + 1, 2, 2t, 2t + 2, t + 2, 1\rbrace$

**Group Operations in Extension Fields**

**Addition**  

Addition of field elements is the usual addition of polynomials, with coefficient addition performed modulo 2 (which is also the same as XORing of the bit-strings)  

For e.g. in  $\mathbb F_{2^4}$, $(t^2 + t + 1) + (t^3 + t + 1) = t^3 +t^2 + (t + t) + (1 + 1)$

Now, $t + t = 2t \bmod 2 = 0$ and $1 + 1 = 2 \bmod 2 = 0$

So

$(t^2 + t + 1) + (t^3 + t + 1)  = t^3 + t^2$  

**Multiplication**  

For a field $\mathbb F_{2^k}$, an irreducible binary polynomial P(t) of degree k is chosen (such a polynomial etists for any k and can be efficiently found). Multiplication of field elements (which are polynomials of degree $k-1$ or lesser) is done modulo the irreducible polynomial. 

For e.g. the irreducible polynomial for the Extension field $\mathbb F_{2^4}$ is $t^4 + t + 1$

Let's multiply the elements $(t^3 + t + 1) * (t+1)$

This gives us $t^4 + t^3 + t^2 + 1$. We now have reduce this mod the irreducible polynomial.

i.e $t^4 + t^3 + t^2 + 1 \bmod t^4 + t + 1$ 

Doing polynomial long division we get a remainder $t^3 + t^2 -t$ 

In $\bmod 2$, $-t$ is the same as $+t$, so this can be written as $t^3 + t^2 + t$.

So we get $(t^3 + t + 1) * (t+1) = t^3 + t^2 + t$

We can check both our addition & multiplication operations in SageMath

~~~ruby
sage: F.<t> = GF(2^4) # Define our extension field
sage: F.modulus() # Irreducible polynomial
t^4 + t + 1
sage: Flist = F.list()
sage: Flist
[0,
 t,
 t^2,
 t^3,
 t + 1,
 t^2 + t,
 t^3 + t^2,
 t^3 + t + 1,
 t^2 + 1,
 t^3 + t,
 t^2 + t + 1,
 t^3 + t^2 + t,
 t^3 + t^2 + t + 1,
 t^3 + t^2 + 1,
 t^3 + 1,
 1]
sage: Flist[7]
t^3 + t + 1
sage: Flist[10]
t^2 + t + 1
sage: Flist[10] + Flist[7] # Add (t^2 + t + 1) + (t^3 + t + 1)
t^3 + t^2
sage: Flist[4]
t + 1
sage: Flist[7]*Flist[4] # (Multiply t^3 + t + 1) * (t + 1)
t^3 + t^2 + t
~~~

**Use of $\mathbb F_{2^8}$ in AES**

In AES, the extension field $\mathbb F_{2^8}$ is used with $t^{8} + t^{4} + t^{3} + t + 1$ as the irreducible polynomial. One byte is 256 bits (i.e. $2^8$). If 2 bytes have to be multiplied, each byte is represented as a polynomial (the bits of the byte form the coefficients of the polynomial) of degree 7 or less. After multiplying the 2 polynomials, they are reduced modulo the irreducible polynomial of degree 8, which results in a polynomial of degree 7 or lesser which will again fit in a byte, thereby providing closure. 

----  

## Elliptic Curves over Extension Fields  

Elliptic Curves over Finite fields (including ones over Extension Fields) have 2 algebraic structures involved. 

- When an Elliptic curve is defined over a Field $\mathbb F_p$ or $\mathbb F_{p^k}$, then the coordinates of the equation of the curve are elements of the field - this field is also called the underlying Field. 

- The points on the curve form a separate Group - the operation of the group is addition of points in the group. The x & y co-ordinates of the points in this group are elements from Field $\mathbb F_p$ or $\mathbb F_{p^k}$ though.

Let's look at Elliptic Curves over Extension fields using this curve $E: y^2 + xy = x^3 + a_2x^2 + a_6$ over the extension field $$\mathbb F_{2^k}$$. 
(This curve equation is in the long Weierstrass form $E:y^2+a_1 xy+a_3 y=x^3+a_2 x^2+a_4 x+a_6$ with $a_1 = 1, a_3 = 0$ and $a_4 = 0$)

**Group Operations** 

**Point Addition**

Let $P = (x_1, y_1)$ and $Q = (x_2, y_2) \in E(\mathbb F_{2^k})$, where $P \ne \pm Q$. 

Then $P + Q = (x_3, y_3)$, with   

$x_3 = \lambda^2 + \lambda + x_1 + x_2 + a$

$y_3 = \lambda (x_1 + x_3)+ x_3 + y_1$

with $\lambda = \frac {y_1 + y_2}{x_1 + x_2}$

**Point Doubling**

$P + P = 2P = (x_3, y_3)$

$x_3 = \lambda^2 + \lambda + a$

$y_3 = {x_1}^2 + \lambda x_3 + x_3$

with $\lambda = x_1 + \frac {y_1}{x_1}$

**Negative**  

$P = (x, y)$

$-P = (x, x + y)$

**Construction**

Let's construct this curve over $\mathbb F_{2^4}$.

$E: y^2 + xy = x^3 + a_2x^2 + a_6$ 


Let, 

$a_2 = t^3$ (i.e. the bitstring [1000])

$a_6 = t^3 + 1$ (i.e. the bitstring [1001])) 

So the Curve Equation is $E: y^2 + xy = x^3 + {t^3}x^2 + (t^3 + 1)$

The x & y coordinates of each point on the Curve are also in the field $\mathbb F_{2^4}$. So x & y can be represented using polynomail basis representation. 

The Elliptic Curve Group has 22 points. These are the 21 elements excluding the Point at Infinity.

$$
\left[
\begin{matrix}
(0 , t^3 + t + 1) & (t + 1 , t^3 + t^2 + t + 1) & (t^3 , t^3 + 1) & (t^3 + t^2 , t^3 + t^2) \\
(1 , 0) & (t^2 + 1 , 0) & (t^3 + 1 , t^2 + t) & (t^3 + t^2 + t + 1 , t^2) \\
(1 , 1) & (t^2 + 1 , t^2 + 1) & (t^3 + 1 , t^3 + t^2 + t + 1) & (t^3 + t^2 + t + 1 , t^3 + t + 1) \\
(t , t^3 + t^2 + 1) & (t^2 + t + 1 , t^3 + t + 1) & (t^3 + t + 1 , t) \\
(t , t^3 + t^2 + t + 1) & (t^2 + t + 1 , t^3 + t^2) & (t^3 + t + 1 , t^3 + 1) \\
(t + 1 , t^3 + t^2) & (t^3, 1) & (t^3 + t^2 , 0)
\end{matrix}
\right]
$$

Using the group operations specified above, let's see how point addition & point doubling is done.

**Addition**   

$P = E(t, t^3 + t^2 + t + 1)$     
$Q = E(t^3 + t^2, t^3 + t^2)$

The irreducible polynomial is $t^4 + t + 1$. 

We want to add $P + Q$. Let's use the Group Law formulas to add the points.  

We have 2 polynomial basis representations here - the coordinates of the Elliptic Curve equation are represented as polynomials & the x & y coordinates of the Curve points are also individually represented as polynomials. 

~~~ruby
sage: F1.<t> = GF(2^4)
sage: F1.polynomial()
t^4 + t + 1
sage: x1 = F1(t)
sage: y1 = F1(t^3 + t^2 + t + 1)
sage: x2 = F1(t^3 + t^2)
sage: y2 = F1(t^3 + t^2)
sage: ? = (y1 + y2)/(x1 + x2)
sage: a = F1(t^3)
sage: b = F1(t^3 + 1)
sage: x3 = ?^2 + ? + x1 + x2 + a
sage: y3 = ?*(x1 + x3)+ x3 + y1
sage: x3
1
sage: y3
1
~~~

So we get $P + Q = E(1,1)$

**Doubling** 

Next is Doubling i.e. $2P$ 

~~~ruby
sage: ? = x1 + y1/x1
sage: x3 = ?^2 + ? + a
sage: y3 = x1^2 +?*x3 + x3
sage: x3
t^3 + t + 1
sage: y3
t
~~~

So $2P = E(t^3 + t + 1, t)$  

We used the group law calculations to do the above to understand it better. But everything can be done using Sagemath's in-built EllipticCurve object as shown below

~~~ruby
sage: F1.<t> = GF(2^4)
sage: E1 = EllipticCurve(F1, [1, t^3, 0, 0, t^3 + 1])
sage:
sage: Elist = E1.points()
sage: P = Elist[5]
sage: P
(t : t^3 + t^2 + t + 1 : 1)
sage: Q = Elist[19]
sage: Q
(t^3 + t^2 : t^3 + t^2 : 1)
sage: P + Q
(1 : 1 : 1)
sage: 2*P
(t^3 + t + 1 : t : 1)
~~~
----
