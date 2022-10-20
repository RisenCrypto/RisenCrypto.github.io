---
layout: post
mathjax: true
title: Polynomials and Elliptic Curves over Extension Fields

---

{% include mathjax.html %}

We look at 2 things


- Polynomial Basis Representation in Extension Fields  

- Elliptic Curves over an Extension Field   

--- 

## Polynomial Basis Representation in Extension Fields   

Let's look at Extension fields of the form $\mathbb F_{2^k}$ as an example - i.e. $\mathbb F_{p^k}$ where $p = 2$. These are called as Binary Fields.

Each element of $\mathbb F_{2^k}$ can be represented as a polynomials & each of these polynomials have their coefficients in the field $\mathbb F_2$ = {0,1}. The degree of the polynomial is less than or equal to $k − 1$. Each element $A(x)$ in $\mathbb F_{2^k}$ would be of the form  $a_{k−1}t^{k−1} +a_{k−2}t^{k−2} +···+ a_{2}t^{2} +a_{1}t + a_0$ with $a_i \in \mathbb F_2 = {0,1}$

Let's take $\mathbb F_{2^4}$ - here the polynomials will be of the form $a_{3}t^3 + a_{2}t^2 + a_{1}t + a_0$ with each coefficient $a_i$ being equal to either 0 or 1. Since each coefficient of the polynomial is either 0 or 1, it's similar to a bit & all the coefficients of one polynomial together can be considered as a bit-string or a vector space. 

For e.g. 

$0$ is $0000$ i.e. $0t^3 + 0t^2 + 0t^1 + 0t^0 = 0$

$1$ is $0001$ i.e. $0t^3 + 0t^2 + 0t^1 + 1t^0 = 1$

$2$ is $0010$ i.e. $0t^3 + 0t^2 + 1t^1 + 0t^0 = t$

....

$5$ is $0101$ i.e. $0t^3 + 1t^2 + 0t^1 + 1t^0 = t^2 + 1$

....

$10$ is $1010$ i.e. $1t^3 + 0t^2 + 1t^1 + 0t^0 = t^3 + t$

and so on & so forth.

So the 16 elements of $\mathbb F_{2^4}$ are 

$$
\left[
\begin{matrix}
0 & t^2& t^3 & t^3 +t^2 \\
1 & t^2 +1 & t^3 +1 & t^3 +t^2 +1 \\
t & t^2 +t & t^3 +t & t^3 +t^2 +t \\
t +1 & t^2 +t +1 & t^3 +t +1 & t^3 +t^2 +t +1
\end{matrix}
\right]
$$

corresponding to the polynomial basis representations of 

$$
\left[
\begin{matrix}
0 & 4 & 8 &12 \\
1 & 5 & 9 & 13 \\
2 & 6 & 10 & 14 \\
3 & 7 & 11 & 15 \\
\end{matrix}
\right]
$$

So, the finite field $\mathbb F_{p^k}$ can be viewed as bit-string or a vector space over its subfield $\mathbb F_p$. The 16 polynomials in $\mathbb F_{2^4}$ can be viewed as the bit representations of all the numbers {0, 1, ...., 15} & the polynomials corresponding to them.

### Group Operations  

**Addition**  

Addition of field elements is the usual addition of polynomials, with coefficient addition performed modulo 2 (which is also the same as XORing of the bit-strings)  

For e.g. in  $\mathbb F_{2^4}$, $(t^2 + t + 1) + (t^3 + t + 1) = t^3 +t^2 + (t + t) + (1 + 1)$

Now, $t + t = 2t \bmod 2 = 0$ and $1 + 1 = 2 \bmod 2 = 0$

So

$(t^2 + t + 1) + (t^3 + t + 1)  = t^3 + t^2$  

**Multiplication**  

For a field $\mathbb F_{p^k}$, an irreducible binary polynomial P(t) of degree k is chosen (such a polynomial etists for any k and can be efficiently found). Multiplication of field elements (which are polynomials of degree $k-1$ or lesser) is done modulo the irreducible polynomial. 

Again, lets take $\mathbb F_{2^4}$ - the irreducible polynomial for this Extension field is $t^4 + t + 1$

Let's multiply the elements $(t^3 + t + 1) * (t+1)$

This gives us $t^4 + t^3 + t^2 + 1$. We now have reduce this mod the irreducible polynomial.

i.e $t^4 + t^3 + t^2 + 1 \bmod t^4 + t + 1$ 

Doing polynomial long division we get a remainder $t^3 + t^2 -t$ 

In $\bmod 2$, $-t$ is the same as $+t$, so this can be written as $t^3 + t^2 + t$.

So we get $(t^3 + t + 1) * (t+1) = t^3 + t^2 + t$

We can check both our addition & multiplication operations in SageMath

~~~
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

Let's look at Elliptic Curves over Extension fields using this curve $E: y^2 + xy = x^3 + a_2x^2 + a_6$ over the extension field $$\mathbb F_{2^k}$$. (This curve equation is in the long Weierstrass form $E:y^2+a_1 xy+a_3 y=x^3+a_2 x^2+a_4 x+a_6$ with $a_1 = 1, a_3 = 0 and a_4 = 0$

### Group Operations       

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

### Construction

Let's construct this curve over $\mathbb F_{2^4}$.

$E: y^2 + xy = x^3 + ax^2 + b$

a & b are coefficients of Curve Equation. The coefficients are in the field $\mathbb F_{2^4}$ & hence they can be represented using polynomial basis representation.

Let a = 8, b = 9

a = (1000) i.e. $z^3$   
b = (1001) i.e. $z^3 + 1$

So the Curve Equation is $E: y^2 + xy = x^3 + {z^3}x^2 + (z^3 + 1)$

The x & y coordinates of each point on the Curve are also in the field $\mathbb F_{2^4}$. So x & y can be represented using polynomail basis representation. 

Using the group operations specified above, let's see how point addition & point doubling is done.

**Addition**   

$P = (2,15)$ i.e. $(0010, 1111)$     
$Q = (12,12)$ i.e. $(1100, 1100)$

The irreducible polynomial is $z^4 + z + 1$. 

We want to add $P + Q$. Let's use sagemath to do this.

We have 2 polynomial basis representations here - the coordinates of the Elliptic Curve equation are represented as polynomials & the x & y coordinates of the Curve points are also individually represented as polynomials. We use $z$ as the variable for both.

~~~

sage: BF.<z> = GF(2^4)
sage: BF.polynomial()
z^4 + z + 1
sage: x1 = BF(z)
sage: y1 = BF(z^3 + z^2 + z + 1)
sage: x2 = BF(z^3 + z^2)
sage: y2 = BF(z^3 + z^2)
sage: λ = (y1 + y2)/(x1 + x2)
sage: a = BF(z^3)
sage: b = BF(z^3 + 1)
sage: x3 = λ^2 + λ + x1 + x2 + a
sage: y3 = λ*(x1 + x3)+ x3 + y1
sage: x3
1
sage: y3
1

~~~
x3 & y3 are both 1 which is (0001)

So we get $P + Q = (1,1)$ i.e. $(0001, 0001)$

**Doubling** 

Next is Doubling i.e. 2P 

~~~

sage: λ = x1 + y1/x1
sage: x3 = λ^2 + λ + a
sage: y3 = x1^2 +λ*x3 + x3
sage: x3
z^3 + z + 1
sage: y3
z

~~~

$z^3 + z + 1$ is (1011) & $z$ is (0010)

So $2P = (11, 2)$ i.e. $(1011, 0010)$  

---  

However, there are some reasons why Elliptic Curves over Finite Fields are more commonly seen than those over Binary Fields. This [Q & A](https://crypto.stackexchange.com/questions/91610/why-are-elliptic-curves-over-binary-fields-used-less-than-those-over-prime-field) covers most of the reasons.

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FExtensionFields%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
