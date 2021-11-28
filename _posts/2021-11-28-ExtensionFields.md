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

Let's look at Extension fields of the form $F_{2^k}$ as an example - i.e. $F_{p^k}$ where $p = 2$. These are called as Binary Fields.

Each element of $F_{2^k}$ can be represented as a polynomials & each of these polynomials have their coefficients in the field $F_2$ = {0,1}. The degree of the polynomial is less than or equal to $k − 1$. Each element $A(x)$ in $F_{2^k}$ would be of the form  $a_{k−1}x^{k−1} +a_{k−2}x^{k−2} +···+ a_{2}x^{2} +a_{1}x + a_0$ with $a_i \in F_2 = {0,1}$

Let's take $F_{2^4}$ - here the polynomials will be of the form $a_{3}x^3 + a_{2}x^2 + a_{1}x + a_0$ with each coefficient $a_i$ being equal to either 0 or 1. Since each co-efficient of the polynomial is either 0 or 1, it's similar to a bit & all the co-efficients of one polynomial together can be considered as a bit-string or a vector space. 

For e.g. 5 is the bit-string/vector space (0101)- which can be represented as the polynomial $x^2 + 1$. 

7 is (0111) i.e $x^2 + x + 1$. 

In general, the finite field $F_{p^k}$ can be viewed as bit-string or a vector space over its subfield $F_p$. The 16 polynomials in $F_{2^4}$ can be viewed as the bit representations of all the numbers {0, 1, ...., 15} & the polynomials corresponding to them.

### Group Operations  

**Addition**  

Addition of field elements is the usual addition of polynomials, with coefficient addition performed modulo 2   

**Multiplication**  
For a field $F_{p^k}$, an irreducible binary polynomial P(x) of degree k is chosen (such a polynomial exists for any k and can be efficiently found). Multiplication of field elements (which are polynomials of degree $k-1$ or lesser) is done modulo the irreducible polynomial. 

**Use of $F_{2^8}$ in AES**   
In AES, $F(2^8)$ is used with $x^{8} + x^{4} + x^{3} + x + 1$ as the irreducible polynomial. One byte is 256 bits (i.e. $2^8$). If 2 bytes have to be multiplied, each byte is represented as a polynomial (the bits of the byte form the coefficients of the polynomial) of degree 7 or less. After multiplying the 2 polynomials, they are reduced modulo the irreducible polynomial of degree 8, which results in a polynomial of degree 7 or lesser which will again fit in a byte, thereby providing closure. 

----  

## Elliptic Curves over Extension Fields  

Elliptic Curves over Finite fields (including ones over Extension Fields) have 2 group structures involved. 

- When an Elliptic curve is defined over a Field $F_p$, then the co-ordinates of the Weierstrass equation of the curve are elements of the field - this field is also called the underlying Field. 

- The points on the curve themselves form a separate Group Structure - an additive Group, where the elements of the Group (the points) can be added. 

Let's look at Elliptic Curves over Extension fields using this curve $$\#E: y^2 + xy = x^3 + ax^2 + b$$ over the extension field $$F_{2^k}$$. 

### Group Operations       

**Point Addition**

Let $P = (x_1, y_1)$ and $$Q = (x_2, y_2) \in \#E(F_2^{k})$$, where $P \ne \pm Q$. 

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

Let's construct this curve over $F_{2^4}$.

$$\#E: y^2 + xy = x^3 + ax^2 + b$$

a & b are coefficients of Curve Equation. The coefficients are in the field $F_{2^4}$ & hence they can be represented using polynomial basis representation.

Let a = 8, b = 9

a = (1000) i.e. $z^3$   
b = (1001) i.e. $z^3 + 1$

So the Curve Equation is $$\#E: y^2 + xy = x^3 + {z^3}x^2 + (z^3 + 1)$$

The x & y coordinates of each point on the Curve are also in the field $F_{2^4}$. So x & y can be represented using polynomail basis representation. 

Using the group operations specified above, let's see how point addition & point doubling is done.

**Addition**   

$P = (2,15)$ i.e. $(0010, 1111)$     
$Q = (12,12)$ i.e. $(1100, 1100)$

The irreducible polynomial is $t^4 + t + 1$. 

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

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FExtensionFields%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
