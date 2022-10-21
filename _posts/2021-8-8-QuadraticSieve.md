---
layout: post
mathjax: true
title: The Quadratic Sieve algorithm for Integer Factorization

---

{% include mathjax.html %}  

The Quadratic Sieve is the second fastest algorithm for factoring large semiprimes. It's the fastest for factoring ones which are lesser than 100 digits long.   

Some Background first. 

**Fermat's Factorization**  

Most commonly used factorization methods today rely on a simple mathematical Identity    

$X^2 - Y^2 = (X+Y)(X-Y)$

To factor $N$, we look for an integer $b$ such that $N + b^2$ is a perfect square.

i.e. $N^2 + b^2 = a^2$  

So $N^2 = a^2 - b^2$

$N = (a + b)(a - b)$  

So we have factored $N$.

For e.g. Let $N = 8051$   

Try $8051 + 1^2, 8051 + 2^2, 8051 + 3^2 .....$, till we hit a result which is a square 

$8051 + 7^2 = 90^2$

So $8051 = (90 + 7)(90 - 7)$   

$8051 = 97$ x $83$

How quick we get to $a$ & $b$ which produces a square depends on how close the factors are to the square-root of $N$.

$\sqrt(8051) = 89.73 \approx 90$ 

So both factors in this case are quite close to the square root (i.e $90$).

The further away the 2 factors of $N$ are away from $\sqrt(N)$, the more difficult this method becomes. Only a small fraction of numbers have a factor close to their square root.

Let's take $N = 799$. It will take us trying from 1 to 15, till we find $799 + 15^2 = 32^2$

So $799 = 17$ x $47$

Obviously, with very large semiprimes, this method would take far too many iterations to be useful.

**Kraitchik's Factorization** 

Kraitchik reasoned that instead of trying to find a & b such that $a^2 - b^2 = N$, it may be sufficient to find a & b such that the difference of squares is equal to a multiple of N.

For e.g. try with $3$ x $799$ instead of 799.

$3$ x $799 = 2397$  

Try $2397 + 1^2, 2397 + 2^2$ etc.   

We get a square right at the 2nd iteration (instead of 15th when trying with 799).

$2397 + 2^2 = 49^2$

A small change in this method is that we need to compute $gcd(N, a + b)$ and/or $gcd(N, a - b)$ to find the factors

$gcd(799, 49 + 2) = 17$

So we get $799 = 17$ x $47$   

What we did here is $kN + b^2 = a^2$  

This can also be represented as a Congruence i.e. 

$a^2 \equiv b^2 \bmod N$  

So the same thing could be done starting with $ceil(\sqrt 799) = 29$ 

$29^2 \bmod 799$ - not a square 

$30^2 \bmod 799$ - not a square 

$31^2 \bmod 799$ - not a square 

$32^2 \bmod 799 \equiv 225 = 15^2$

$gcd(799, 32 - 15) = 17$

$799 = 47$ x $17$

However, even with this, you may not find easily a result which is a square. So Kraitchik tried to find several numbers whose squares are a product of prime powers. These are easier to find. Once you find several of them, you then combine them by multiplication to find a combination which is a square. 

$N = 63787$   

$439^2 \bmod 63787 \equiv 1360 = 2^4$ x $5$ x $17$  

$445^2 \bmod 63787 \equiv 6664 = 2^3$ x $7^2$ x $17$

$449^2 \bmod 63787 \equiv 10240 = 2^{11}$ x $5$

Multiply the above 3 congruences  

 $(439^2$ x $445^2$ x $449^2) \equiv (2^{18}$ x $5^2$ x $7^2$ x $17^2) \bmod 63787$

$7270^2 = 49492^2 \bmod 63787$

$gcd(63787, 49492 + 7270) = 281$

$63787 = 281$ x $227$  

The Factorisation is complete.   

Most modern factorization methods use the above procedure   

1. **Relation Building:** Find many integers such that each is a Quadratic Residue mod $N$. Find many $c_i$ such that $c_i \equiv {a_i}^2 \bmod N$.   

2. **Elimination:** Take a product of some of the $c_i$'s such that their product is a product of even powers of the primes. i.e ${c_i}_1 . {c_i}_2 . {c_i}_3 ... \equiv b^2 \bmod N$. 

3. **GCD Computation:** Use GCD to find the factors of $N$. There is a reasonable chance that the GCD is a factor of $N$   


In Step 1, we look for Quadratic residues $c_i$'s which are B-Smooth. To factor $N$, we can calculate an approximate value of $B$ before looking for B-smooth numbers. We can use this formula to find a suitable B.  

$L =  e^{\sqrt {\ln(N)ln(ln(N))}}$

$B \approx L^{\frac {1}{\sqrt 2}}$

If we want to factor $N = 87463$, we calculate $B \approx 42$. So we are looking for 43-Smooth Quadratic Residues $\bmod N$. We use the Quadratic Sieve Algorithm in the Relationship Building step to find the B-smooth numbers in our list. If you are not familiar with what 'Sieving' is, you can look up [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes). Unlike the Basic Sieve, in the Quadratic Sieve algorithm, we sieve all the way upto 1 so as to find Smooth numbers.

**Quadratic Sieve**   

We use a Quadratic Polynomial to do the sieving - hence the name Quadratic Sieve.  

One such polynomial is $F(T) = T^2 - N$. We start with a $T$ which is $ceil(\sqrt N)$ 

$\sqrt 87463 = 295.74$. So we start with a = 296.  

We sieve the numbers $F(a), F(a+1), F(a+2) ...$ till we find enough B-smooth numbers.    

$F(a) = 296^2 - N = 153$, $F(a+1) = 297^2 - N = 746$, $F(a+2) = 298^2 - N = 1341$ and so on & so forth. 

Let's call this list $LS$ = {153, 746, 1341, 1938, 2537, 3138, 3741, ....}

We try to find 43-smooth numbers in this list by sieving away with primes smaller than or equal to 43 and checking which numbers in the list get sieved all the way down to 1.

First we build what is called as the Factor Base - this is a list of all primes lesser than or equal to B.

{2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41 ,43}

However, there may be some numbers in our list which are not divisible by any of the primes our Factor Base. So we can remove them from the Factor Base. Euler's Criteria for Quadratic Residues can be used to check if $N$ is a Quadratic Residue for odd prime. If $N^{\frac {p-1}{2}} \bmod p \ne 1$, then $N$ is not a QR for that odd prime,  that prime is not going to divide any of the numbers in our list. Euler's criteria doesn't work for 2 (since it's an even prime), hence we check for 2 manually.

Using the above criteria, we can reduce the factor base to 

$FB$ = {2, 3, 13, 17, 19, 29, 41, 43} - these are all the primes we are going to sieve our list with.   

How many numbers would we need to sieve till we have a good chance of finding a combination? There is no formula to calculate this, but usually you should contine till you find atleast 5 more smooth numbers than the number of primes in the Factor Base. 

We start with $LS = {153, 746, 1341, 1938, 2537, 3138, 3741, ....}$  

Next we start with first prime in the FB i.e 2 & start sieving the first number in $LS$.

The First number in the list is 153. We cannot sieve it by 2 because it's not divisible by 2. Next we try with the next prime in the $FB$, i.e. 3 - it's divisible by 3 & gives 51, 51 again is divisible by 3 giving 17 - i.e. the original number got divided by 3 twice, i.e. one of it's factors is $3^2$. Next number in the FB is 13, but 17 is not divisible by 13, so we move on to the next prime in the $FB$, i.e. 17. 17 divides 17 & the result is 1. When we reach 1, it means we have found a Smooth number. If we don't reach 1 by the time we try the last prime in the $FB$ (43 in this case), then it means that number we are sieveing is not a Smooth number. So either way, we move on to the next number to sieve.   

$153 = 3^2$ x $17$  

One thing to notice here is that checking whether a number is divisible by the prime by using trial division may be expensive. We can avoid this by using the roots to figure out which numbers in the list are going to be divisible by a particular prime.  

For e.g. for the prime $p_i$, the roots of $N \bmod p_i$ will tell us which numbers in our list are divisible by $p_i$. The roots can be found using the Tonelli–Shanks algorithm

Let's try it for $p_i = 13$. I am using sagemath here to find the roots of $N \bmod 13$

**sage:** Mod(87463, 13).sqrt(all=True)

[5, 8]

This gives 2 roots - $5$ & $8$. Now, since we are starting from $296$, we need to adjust the roots to get the Index/Position of the number in our list which is divisible by 13. This can be done by $Mod (5 - 296, 13) = 8$ & $Mod(8-296, 13) = 11$. 

In our list LS if we start the indexing with 0, this means that the 8th & 11th number will be divisible by 13.  Also, 8+13th, 8+26th, 8+39th etc & 11+13th, 11+26th, 11+39th etc number will be divisible by 13. This can be used to avoid trial division & only sieve those numbers at that position.  
I have created a Google Spreadsheet to show the the Sieving process.  

Click to view the [**Google Spreadsheet**](https://docs.google.com/spreadsheets/d/1mWJSKojkZtnXiJ332plIN1UPdYHN5k5SCG9G7_Fld10/)

Row 3 is the Factor Base. Most of the primes have 2 roots for $N \bmod p_i$ & hence those appear twice in consecutive columns.

Row 4 has the roots for the prime above it.  

Row 5 is the Adjusted Root - Calculated by the formula we looked at e.g. for Cell D5 it's calculated as Mod(D4-B2,D3)

Column C contains the list of numbers we are going to sieve with the primes in the Factor Base.

I have written a script function in the Google sheet to do the sieving

~~~js   

function Sieve(no, p) 
{
  while (no % p == 0)
  {
    no = no/p;
  }
  return no;  
}

~~~

Cells D7:R58 contain a formula (all the cells contain the same formula adjusted for Row & Column Numbers). 

This is the Formula in D7 

=If(Mod(<span>$</span>A7,D<span>$</span>3) = D<span>$</span>5,Sieve(C7,D<span>$</span>3),C7)

For any cell if the position/index of the row matches with the Adjusted root, then we call the function Sieve on that cell passing the number to be Sieved (the cell to the left of the current cell) & also the prime to Sieve it with. The Sieve function doesn't stop at a single divison by the prime, it keep dividing repeatedly till it finds the exact prime power which divides the number. 

After sieving all the numbers, those numbers which got sieved all the way to 1 are our Smooth numbers 

We have found the following Smooth Numbers 

1. $296^2 \bmod 87463 \equiv 153$ and $153 = 3^2$ x $17$

2. $299^2 \bmod 87463 \equiv 1938$ and $1938 = 2$ x $3$ x $17$ x $19$

3. $302^2 \bmod 87463 \equiv 3741$ and $3741 = 3$ x $29$ x $43$

4. $307^2  \bmod 87463 \equiv 6786$ and $6786 = 2$ x $3^2$ x $13$ x $29$

5. $316^2 \bmod 87463 \equiv 12393$ and $12393 = 3^6$ x $17$   

6. $343^2 \bmod 87463 \equiv 30186$ and $30186 = 2$ x $3^3$ x $13$ x $43$

7. $347^2 \bmod 87463 \equiv 32946$ and $32946 = 2$ x $3$ x $17^2$ x $19$

The remaining steps of factorization (Elimination & GCD) are not part of the Quadratic Sieving algorithm itself. They are used in all Factorization methods once the Relationship Building step is completed

**Elimination & GCD:**  

Every positive integer m has an exponent vector v(m) that is based on the prime factorization of m

For e.g. the exponent vector for $6786 = 2$ x $3^2$ x $13$ x $29$ can be written as

$v(6786) = \lbrace 1\space 2\space 1\space	0\space	0\space	0\space	1\space	0\space	0\rbrace$

i.e. $6786 = 2^1$ x $3^2$ x $13^1$ x $17^0$ x $19^0$ x $21^0$ x $29^1$ x $41^0$ x $43^0$

When 2 numbers are multiplied, then their exponents get added. So the exponent vector for the result of the multiplication would be the sum of the exponent vectors. The result would only be a square if each position in the component vector of the result is an even number. We can simplify this further by taking mod of each element of the exponent vector by 2 & then adding them mod 2. Any product of the smooth numbers would be a square if each element of the product exponent vector is 0.  

This now reduces to a Linear Algebra problem. 

We have 
$A \vec{x} = \vec{0}$

where 

$$ A = 
\begin{pmatrix} 
    0&1&0&1&0&1&1 \\ 
    0&1&1&0&0&1&1 \\
    0&0&0&1&0&1&0 \\
    1&1&0&0&1&0&0 \\
    0&1&0&0&0&0&1 \\
    0&0&0&0&0&0&0 \\
    0&1&0&1&0&0&0 \\
    0&0&0&0&0&0&0 \\
    0&1&0&0&0&1&0 \\
\end{pmatrix}$$

Each column of A is the exponent vector of one Smooth Number we found through Sieving. And the equation is over $\mathbb{F}_2$  

We can find $\vec{x}$ by computing the Kernel. Gaussian Elimination can be used for finding a basis for the kernel.

Doing it in SageMath ![Sagemath](https://raw.githubusercontent.com/RisenCrypto/RisenCrypto.github.io/master/images/SageQS.png )

So we get the following vectors (written horizontally instead of vertically)  

$v1 = [1\,0\,0\,0\,1\,0\,0]$ (i.e. multiply the squares of 1st & 5th Number to get a Zero exponent vector - $296^2$ x $316^2$)   

$v2 = [0\,1\,0\,0\,1\,0\,1]$ (i.e. multiply the 2nd, 5th & 7th number - $299^2$ x $316^2$ x $347^2$)

$v3 = [0\,0\,1\,1\,0\,1\,0]$ (i.e. multiply the 3rd, 4th & 6th number - $302^2$ x $307^2$ x $343^2$)

Let's take the first one

$296^2$ x $316^2 \equiv (3^2$ x $17)(3^6$ x $17) \bmod 87463$   

$= {(3^4 \quad X \quad 17)} ^2$



$gcd(N, (296$ x $316) - (3^4$ x $17)) = 587$

So we have factorized $N$ using just the 1st vector 

$87463 = 587$ x $149$

The process actually ends here but let's try it again with the 2nd & 3rd vector also

$gcd(N, (299$ x $316$ x $347) - (2$ x $3^4$ x $17^2$ x $19)) = 587$

$gcd(N, (302$ x $307$ x $343) - (2$ x $3^3$ x $13$ x $29$ x $43)) = 587$ 

So all 3 zero product exponent vectors have given us the factorization (If not even one of them do, then we may have to sieve more numbers & redo the Elimination Step)   

---  

The above is a very simple version of the Quadratic Sieve without much optimizations. Also, our example factors a very small 5 digit number while the Quadratic Sieve is used for upto 100 digit numbers. 

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FQuadraticSieve%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
