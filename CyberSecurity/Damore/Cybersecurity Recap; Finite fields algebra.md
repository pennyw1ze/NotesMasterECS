### Congruence

> 2 numbers $a$ and $b$ are said to be **congruent** if $a \equiv b(\bmod n)$ 

It means that $|a - b|$ is multiple of $n$, or that the integer division of $a$ and $n$ and the integer division of $b$ and $n$ has the same remainder.

### Quotient Set

>$Z_n$ is the quotient set of $i$ if it contains: $Z_n =\{[0],[1],[2],...,[n-1]\}$ where $[i]$ is the equivalence class of integers congruent to $i (\bmod n)$.

### Multiplicative group

> $Z_m$ is the multiplicative group of $m$ and contains all the natural numbers mod $m$ that are relatively prime (co-prime) to $m$.

In number theory, 2 integer $a$ and $b$ are **coprime**, **relatively prime** or **mutually prime** if the only positive integer that is a divisor of both of them is 1, so $GCD(a,b) = 1$.

### Euler totient function

> The Euler totient function $𝜑(n)$, is the number of positive integers not greater than $n$ are coprime with $n$.

So the totient function 𝜑(n) it's equivalent to $|Z_n|$ (cardinality of the multiplicative group of n).
### Euler theorem
> If $a$ ed $n$ are coprime  positive integers (i.e. $GCD(a, n) = 1$) and $𝜑(n)$ is the is Euler's totient function (how many positive integers not greater than $n$ are coprime with n) $a^{𝜑(n)} \equiv 1 (\bmod n)$.

### Bezout's identity

> Let $a$ and $b$ be a 2 non-zero integer numbers, with $GCD(a,b) = d$. Then we can say that $\exists x, y$ s.t. $ax + by = d$.