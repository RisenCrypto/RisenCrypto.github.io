---
layout: post
mathjax: true
title: Cofactor clearing in composite order Elliptic Curves

---

{% include mathjax.html %}

Many cryptographic protocols use elliptic curves of prime order. However there are composite order curves like the Edwards Curve, Montgomery Curve etc which easily provide faster group operations with complete addition laws which are not vulnerable to a timing attack. So many modern elliptic curve implentations use a composite order curve of order $h.p$ where $p$ is a prime & $h$ is a small cofactor usually $8$ or lesser. The composite order curve has a subgroup of prime order $p$ which is used for the implementation. Though these curves have a lot of advantages, there are also some disadvantages, one of which we discuss below called as a small subgroup attack.

## Small Subgroup attack in non-prime order curves

The operations are usually implemented in the prime order subgroup of the full Curve. Let's take the example of Diffie Hellman to understand the small subgroup attack. Let's say our curve is of order $8p$ where $p$ is a prime & $8$ is the cofactor. Let $G$ be a generator of the prime order subgroup. Let $a$ & $b$ be Alice's & Bob's private key respectively. This is how a typical Diffie Hellman key exchange works.

$$
\text{Alice} \xrightarrow{\hspace{2cm} a G \hspace{2cm}} \text{Bob} \\
\text{Alice} \xleftarrow{\hspace{2cm} b G \hspace{2cm}} \text{Bob}
$$

Alice calculates $a.bG$, Bob calculates $b.aG$ and the shared secret is derived from $abG$ by both parties.

If Bob is a malicious participant, he can launch a small subgroup attack which can leak some information about Alice's private key. The protocol depends on operating in the prime order subgroup (i.e. the subgroup of order $p$). Since $G$ is a generator of the prime order subgroup, $abG$ also falls in the prime order subgroup (because of closure). But the full curve group also contains other subgroups - one of which is of order $8$. The points of the subgroup of order $8$ are not in the prime order subgroup (other than the identity). Let $H$ be a **generator** of the subgroup of order $8$. Now, if Bob instead of sending $bG$ to Alice sends just $H$ to Alice.  So Alice calculates $aH$ (instead of $abG$) & derives the share secret using that. Alice then encrypts a message using the shared secret & sends it to Bob. Now if Alice's shared secret had been derived from $abG$, then number of possible values $abG$ could have is $p$ which is a very large prime - for e.g. for $Curve25519$, $p = 2^{252} + 27742317777372353535851937790883648493$ so brute forcing the encrypted message to recover the shared secret is not possible. However, Bob has tricked Alice into deriving her shared secret from $aH$. Since $H$ is a generator of subgroup of order $8$, $aH$ is a point of the subgroup of order $8$ because of closure in the subgroup. So $aH$ can only be one of $8$ points instead one of $p$ points. Now brute forcing the secret key is much simpler for Bob. So Bob can recover $aH$. It's possible to recover the value of $a \bmod 8$ from $aH$ - which is the lower $3$ bits of $a$.

To understand this better, let's take the example of a simple additive group of order $8p$ where $p = 11$ and the cofactor is $8$. This is the group $\mathbb Z/{88\mathbb Z}$. This has the elements $ \lbrace 0, 1, 2, 4, .... 87 \rbrace$ (I am omitting the coset notation for simplicity)

This has multiple subgroups including 

- the prime order subgroup of order $11$ - $\lbrace 0, 8, 16, 24, 32, 40, 48, 56, 64, 72, 80 \rbrace$ 

- the cofactor subgroup of order $8$ - $\lbrace 0, 11, 22, 33, 44, 55, 66, 77 \rbrace$. 

Let the generator chosen for the DH exchange be $8$. So Alice would send $a.8$ & Bob would send $b.8$ & the shared secret would be derived from $ab8$. A malicious Bob instead of sending $b.8$, sends a generator of the cofactor subgroup - i.e he sends $11$. So now Alice computes $11a$. Irrespective of what $a$ is chosen by Alice, $11a$ always is restricted to 8 different elements than the $11$ different elements if Bob was a honest participant. Alice now derives the shared secret from $11a$ & she encrypts a message with it & sends it to Bob. Because of the attack, Bob now has to only bruteforce by doing an exhaustive search through $8$ different elements instead of $11$ different elements. In our toy example, reduction from $11$ to $8$ is not a significant reduction, but in the case of actual cryptographic curves, the reduction in search is huge - from searching through a really large set of size $p$ to searching through just $8$ points. So now Bob knows $aH$. If you iterate through all possible 88 values of $a$ & calculate $aH$, you will see that $aH$ falls into 8 different values depending on value of $a \bmod 8$. So if you know the shared secret, you know $a \bmod 8$ which is the lower $3$ bits of $a$. 

This is the small subgroup attack. 

There are also a couple of other attacks similar to the one we described. 

- Instead of sending $bG$, Bob can send $bG + H$ to Alice. So Alice now calculates $abG + aH$. Now since Bob knows $abG$, this again leaks $a \bmod 8$.

- Instead of Bob launching the above attack, a man in the middle (Mallory) intercepts $bG$ sent by Bob & replace it with $H$, then he can brute force the secret key generated by Alice ($aH$) because it can only be one of $8$ points.

In DH, leaking $a \bmod 8$ matters only if Alice reuses $a$. However, above attack is not limited to DH, but also applicable to many other Cryptographic protocols. In some protocols like like PAKE, MQV etc, the attack can have far worse effects.

## Mitigation for the small subgroup attack - cofactor clearing

Scalar multipliying any point by the cofactor ($8$ in this case) projects the point on to the prime order subgroup, irrespective of which subgroup it was in originally. This can be easily done by both parties - i.e. Alice after choosing a private key $a$, she multiplies it by $8$ & uses $8a$ as the private key. So when she receives $bG$ from Bob, the shared secret is derived from $8abG$ instread of from $abG$. Likewise Bob does the same. Now if Bob is a malicious player, then what is leaked is $8a \bmod 8$ instead of $a \bmod 8$. Since $8a \bmod 8$ is always 0, nothing of significance is leaked. Thus, mutliplying the private key by the cofactor mitigates the small subgroup attack.

Below is a sage program which takes all possible values of $a$ & scalar multiplies it by $8$. And you can see that after doing this, the output is always an element of the prime order subgroup. 

$djb$ uses a related method for $Curve25519$ - the lower 3 bits of the private key/scalar is cleared before use. Any element with $3$ least significant bits as $0$ is a multiple of $8$ & nothing is leaked by the small subgroup attack.

## Torsion safe cofactor clearing

The method of cofactor clearing by scalar multiplying by the cofactor works fine for many protocols but isn't suitable everywhere. In many implementations of $Ed25519$, the private key/scalar is persisted as is and before use the least significant 3 bits of the scalar are cleared. This creates a problem for some use-cases. 
Any point $Q$ on the full curve of order $8p$ can be written as the sum of 2 components i.e. the prime order component $P$ & a $8$-torsion component $T$ from the subgroup of order $8$ i.e $Q = P + T$. Clearing the lower 3 bits of $Q$ could change both the prime order component & also the 8-torsion component. This may not be appropriate in some places (like BIP-32 which uses hierarchical key derivation scheme). These use-cases require "torsion-safe" cofactor clearing. 

Let's again use the simple additive group we used earlier - i.e. $\mathbb Z/{88\mathbb Z}$ to look at this. 
Every element of this group can be expressed as sum of 2 elements, one from the prime order subgroup & one from the $8$-torsion subroup.
There are the 2 subgroups. 

$\lbrace 0, 8, 16, 24, 32, 40, 48, 56, 64, 72, 80 \rbrace$ - subgroup of order $11$ 
 
$\lbrace 0, 11, 22, 33, 44, 55, 66, 77 \rbrace$ - subgroup of order $8$

For e.g. 

$59$ from the main group can be written as sum of an element from each of the 2 subgroups.

$59 = (48 + 11) \bmod 88$

$61 = (72 + 77) \bmod 88$

To have torsion safe cofactor clearing, we have to clear out only the torsion component without disturbing the prime order component.

$Q = (P + T) \bmod 88$

We have to find a $k$ such that when $Q$ is multiplied by it, it zeroes out $T$ without disturbing $P$.

$k.Q = (k.P + k.T) \bmod 88$


All $P$ are generated by $8$ & are of the form $8.c$. 

$Q = (8.c + T) \bmod 88$

Hence multiplying it by a scalar of the form $k=11.d + 1$ will preserve it. 

$k.Q = ((11.d + 1).8.c + k.T) \bmod 88$    

$\qquad = 88.d.c \bmod 88 + 8.c \bmod 88 + k.T \bmod 88$
    
$\qquad = 8.c \bmod 88 + k.T \bmod 88$
    
$\qquad = (P + k.T) \bmod 88$
    
So $k$ can be expressed as

$k \equiv 1 \bmod 11$  [Congruence (1)]

All $T$ are generated by $11$ & are of the form $11.f$. $11$ is of order $8$. Hence multiplying by $8$ or a multiple of $8$ will zero it out. So $k$ has to be a multiple of $8$. This can be expressed as

$k \equiv 0 \bmod 8$ [Congruence (2)]

We can combine the (1) & (2) congruences using Chinese Remainder Theorem to find $k$

~~~
sage: CRT_list([0,1], [8,11])
56
~~~

So $k = 56$.

So multiplying by $56$ will clear the torsion component while leaving the prime order component undisturbed.

Let's take an example of private key/scalar $a = 141$. 

If we use a non-torsion safe clearing of lowest 3 bits, then $a1 = a \,\&\, 248 = 136$ (clearing lowest 3 bits)

If we use torsion safe clearing, then $a2 = 56.141 = 7896$

Let's test with a prime order element like $16$.

1) With original key $(a.16) \bmod 88 = (141.16) \bmod 88 = 56$

2) With non-torsion safe cleared key $(a1.16) \bmod 88 = (136.16) \bmod 88 = 64$

3) With torsion safe cleared key $(a2.16) \bmod 88 = (7896.16) \bmod 88 =56$

Now with a 8-torsion element like $33$


1) With Original key $(a.33) \bmod 88 = (141.33) \bmod 88 = 77$ 

2) With non-torsion safe cleared key $(a1.33) \bmod 88 = (136.33) \bmod 88 = 0$

3) With torsion safe cleared key $(a2.33) \bmod 88 = (7896.33) \bmod 88 =0$

So what we see is if the element (say $Q$) belongs to the prime order subgroup then $a2.Q = a.Q$, but if it belongs to torsion subgroup, then $a2.Q = identity$.

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frisencrypto.github.io%2FCofactorClearing%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
