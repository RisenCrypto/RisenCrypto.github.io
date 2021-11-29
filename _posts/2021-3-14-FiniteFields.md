---
layout: post
mathjax: true
title: Why does Cryptography use Polynomial Modular Arithmetic in Finite Fields?

---

{% include mathjax.html %}

Polynomial Modular Arithmetic done in Finite Fields is used in a lot of places in Cryptography - for e.g. in AES/Rijndael, GHASH, POLYVAL etc. What is the reason for this?

## First, why Finite Fields at all?

Let's start with what is a Finite Field? For that, we will start with a Group & from there go all the way up to Finite Fields.  

#### A **Group** is a set G with a binary operation (let's call this operation '+') on G such that for all $a, b, c \in G$ 

* $a + (b + c) = (a + b) + c$  - This property is called **associativity** 

* There exists an element e $\in G $ (called the **identity element**) such that for all $a \in G, a + e = a = e + a$ 

* For all $a \in G$ there exists $a' \in G$ (called the **inverse of** a) such that $a + a' = e = a' + a$

* Any operation done on the group using the operator will always produce a result which is also a member of the group. This property is called **Closure**

#### An Abelian Group is a Group which is also **Commutative** - i.e. for all $a, b \in G, a + b = b + a$

#### A Ring has 2 operations. A ring is an Abelian Group under the first operation (let's say '+'). It has the following two properties for a second operation (let's say '*').

* Associative multiplication

    $a * ( b * c ) = ( a * b ) * c$

* Left & right distributive over multiplication

     Left:  $a * (b+c) = a * b + a * c$

     Right:  $(a+b) * c = a * c + b * c$

A ring is always commutative under the first operation. A ring which is also commutative under it's 2nd operation is called a commutative ring.

#### A field also has 2 operations. A field is an Abelian group under the first operation (let's call it addition). Under the second (let's call it multiplication), a field has all the properaties which a Commutative rung has under it's 2nd operation. A field also has an identity element under multiplication where every non-zero element has a multiplicative inverse. 

Finite Fields are also called **Galois Fields**

So, considering both the operations, these are all the different properties which a Finite Field must possess

**Under Addition**

* Closure

* Associativity

* Identity

* Inverse

* Commutativity

**Under Multiplication**

* Closure

* Associativity

* Distributivity

* Commutativity

* Identity

* Inverse

* No zero divisors

Many of the different properties which Finite Fields possess are very useful in cryptography.

Let's look at a few of them

1. When you are multiplying 2 numbers in a cryptographic operation, it's very useful to have the result also in the same closed set. For e.g. in AES, when you multiply 2-byte variables with each other, you want the result also to be a byte - the multiplication shouldn't result in something which is bigger than a byte. Finite Fields have **Closure**, so the result will always be a byte

2. Many operations in Cryptography need to be invertible. For e.g. if while encrypting, you do some mathematical operation, you want it to     invertible so that you can reverse the operation while decrypting. All elements in a Finite Field have an **Inverse** under addition & all non-zero elements have an **Inverse** under multiplication. In AES, in the MixColumns operation, we multiply by a matrix & during decryption we multiply by the inverse of the matrix.

3.  In AES's MixColumns, we need the **associativity property** - $a*(b+c) = a*b + a*c$

So, these are some of the reasons many cryptographic operations are done in Finite Fields.

## Why not regular modular arithmetic? 

We want to do arithmetic with bytes. A byte can be represented by using the bits as the coefficients of a polynomial.

For e.g. if we want to represent a byte with value *51*. In binary, *51* is 00110011.

This bit pattern can be represented as the coefficients of the polynomial $x^{5} + x^{4} + x + 1$

Likewise *249* = 11111001 = $x^{7} + x^{6} + x^{5} + x^{4} + x^{3} + 1$

So, if we are multiplying 2 bytes, then the 2 bytes can be represented as 2 polynomials $f(x)$ & $g(x)$ - the degree of each of the polynomial will be 7 or lower.

But when we multiply 2 polynomials of degree 7 or lower, we can get a result which can be of a degree higher than 7, so it can no longer be
represented in a byte. So we need closure. We can achieve closure by reducing by modulo in a finite field.

A finite field of order *q* exists if and only if *q* is a prime power - i.e. $q = p^{n}$

A finite field of order $p^{n}$ is written as $GF(p^{n})$

There are 2 cases here  
1) n = 1. This is written as $GF(p)$   
2) n > 1. This is written as $GF(p^{n})$    

Both these kinds of finite fields have different structures. $GF(p^{n})$ is called an extension field of $GF(p)$, because it can embed $GF(p)$. For e.g. $GF(2^{8})$ is an extension of $GF(2)$, $GF(2^{2})$, $GF(2^{4})$ 

We can do the reduction by modulo 8, which would ensure that the degree of the polynomial cannot be greater than 7. However, $\mathbb{Z_8}$ is not a finite field under regular modular arithmetic because some of the elements in $\mathbb{Z}_{8}$ do not have an inverse.

But, 2 is a prime & 8 is an integer, so it should be possible to form a finite field $GF(2^{8})$ even if not through regular modular arithmetic. $2^8$ is 256 which is the number of bits in one byte, so we can use $GF(2^{8})$ as the finite field. 

## Why Modular Polynomial Arithmetic? 

Both $f(x)$ and $g(x)$ are polynomials of degree 7 or less. So $f(x) * g(x)$ can result in a polynomial of degree greater than 7.

So, we reduce $f(x) * g(x)$ by modulo an irreducible polynomial of degree 8, so that the result $r(x)$ is a polynomial of degree 7 or less.

So in AES, if 2 bytes have to be multiplied, they are each represented as a polynomial (the bits of the bytes form the coefficients of the polynomial) of degree 7 or less. After multiplying the 2 polynomials, they are reduced by modulo an irreducible polynomial of degree 8, which results in a polynomial of degree 7 or lesser which will again fit in a byte.

Hence Modular Polynomial Arithmetic done in a Finite Field is used in Cryptography.

AES uses $x^{8} + x^{4} + x^{3} + x + 1$ as the irreducible polynomial

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FFiniteFields%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
