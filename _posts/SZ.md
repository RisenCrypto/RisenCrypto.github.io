
Let $f$ be a polynomial from $\mathbb F_p[x]$ of degree less than or equal to $d$. Let $p \approx 2^{256}$ & $d \le 2^{40}$. If we test $f$ at a random value $r \in \mathbb F_p$, then the probability that $f(r) = 0$ would be $\frac {d}{p}$ which for these values of $d$ & $p$ would be very, very small. So, if $f(r) = 0$, then with high probability, $f$ is a zero polynomial (i.e., zero at all points). 

This also helps to prove that 2 polynomials are equal. If the 2 polynomials are $f$ & $g$, we create a new polynomial $p(x) = f(x)\space - \space g(x)$.

If we test $p$ at a random $r \in \mathbb F_p$ & $f(r)$ turns out to be $0$, then $p$ is a zero polynomial with very high probability. If $p$ is a zero polynomial, then it obviously means $f = g$
