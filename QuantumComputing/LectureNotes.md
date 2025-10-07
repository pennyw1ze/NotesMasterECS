### Links
- [**Quiskit**](https://pypi.org/project/qiskit/): library to simulate quantum computing in python;
- [**Quiskit**](https://quantum.cloud.ibm.com/learning/en) link to lectures by IBM;

> **Not solving** NP-complete problems

> Quantum computers and classical computers can **solve** the **same problems** (but with different time complexity)


> **Reversable Turing machine**
   Quantum computers must be reversable (when you do computations, you can always recover previous state). So basically we do not have a one-way computation anymore.

---
> **second lecture**

## Review of complex numbers

**Syntax**
$$a + ib;$$
$C= \{a + ib| a,b \in R\}, Re(z) = a, Im(z) = b$

##### Operations

- **Sum**:
$$(a + ib) + (c + id) = a + c + i(b + d)$$
- **Multiplication**:
$$(a + ib)\times(c + id) = ac - bd + i(ad + bc)$$
- **Coniugate**:$$\bar{(a + ib)} = (a -ib)$$
- **Modulus**:$$|z| = \sqrt{a^2 + b^2}$$
- **Division**:$${z\over w} = {z\over w}\times {\bar{w}\over \bar{w}} = {z\bar{w}\over w \bar{w}}$$

##### Properties
- $$|z| = \sqrt{z\bar{z}}$$
- $$|zw| = |z||w|$$
- Remember how to use polar coordinates:
	- Length of the segment that connects the complex number to the center of the plane with polar coords is $|z|$ the modulus of the complex number;
	- Coords on the plane are real and immaginary part;
	- $\exists ! \Theta \in (-\pi, \pi]\ s.t.\ cos\theta = {a\over |z|}, sin\theta = {b\over |z|};$
	- $e^{j\theta} = cos\theta + jsin\theta$;
	- $z = |z|(cos\theta + jsin\theta) = |z|e^{j\theta}$;

#### Eigenvector and eignevalues
$v \neq 0$ is an eigenvector, and $\lambda \in C$ is an eigenvalue of matrix $A$ if:$$Av = \lambda v \implies (A - \lambda I)v = 0;$$
This system has some solution if $det(A-\lambda I) = 0$.
There might be more $v$ valid for the same $\lambda$.

## Qbits
We will see that quantum computers use **probabilistic algorithms**. Quantum computers implements Qbits which are like bits for the standard computers.
So for a given input, when the code is executed, you can have n different outputs, each with a given probability, so we have **non deterministic** computation.
Quantum computers actually behaves **randomly**, so they can generates **genuine randomness**.
We do not work with bit anymore, but we works with vectors.
bit $0$ becomes $\begin{pmatrix}1\cr0\cr\end{pmatrix} = |0>$
bit 1 becomes $\begin{pmatrix}0\cr1\cr\end{pmatrix} = |1>$
and many other states in the form:

$$\alpha |0> + \beta |1> = \begin{pmatrix}\alpha\cr\beta\cr\end{pmatrix}$$ where $$\alpha, \beta \in C\ \cap |\alpha|^2 + |\beta|^2 = 1$$
Bits 0 and 1 are called **basic states**.
$\alpha$ and $\beta$ are called **probability amplitudes**.
Even tough qbits can live in a wide range of states, you can only see qbits in a single state.
To see the actual state of a qbit, we need to **measure** it.
When I measure a qbit, I can see 2 possible states:
- State 0 with probability $|\alpha|^2$;
- State 1 with probability $|\beta|^2$;
The measurement process is random.
Quantum **transformations** must be **linear**.

##### Not gate
The not gate should reverse the probability of measuring a qbit and obtaining 1 or 0.
**Pauli matrix**
$Not = \sigma_x = \begin{bmatrix}0&1\cr1&0\cr\end{bmatrix}$
notice:$$\begin{bmatrix}0&1\cr1&0\cr\end{bmatrix}\times \begin{pmatrix}\alpha\cr\beta\cr\end{pmatrix} = \begin{pmatrix}\beta\cr\alpha\cr\end{pmatrix}$$

---

> **third lecture**

What can we do with a qbit ?
- **Measure**:$$\alpha |0> + \beta |1>\ \rightarrow \{Pr(0) = \alpha^2,\ Pr(1) = \beta ^2\};$$
- **Transformations (unitary)**: 
  Pauli matrices:
	- **Not** gate: $$Not = \sigma_x = \begin{bmatrix}0&1\cr1&0\cr\end{bmatrix};$$
	- $$\sigma_y = \begin{bmatrix}0&-i\cr i&0\cr\end{bmatrix};$$
	- $$\sigma_z = \begin{bmatrix}1&0\cr0&-1\cr\end{bmatrix};$$
	Hadamard transform
	- Hadamard matrix:$$H = {1\over \sqrt 2}\begin{bmatrix}1&1\cr1&-1\cr\end{bmatrix};$$
	Useful for:$$H|0> = {1\over \sqrt2}\begin{bmatrix}1&1\cr1&-1\cr\end{bmatrix}\begin{pmatrix}1\cr 0\cr\end{pmatrix} = {1\over \sqrt 2}\begin{pmatrix}1\cr 1\cr\end{pmatrix} = {1\over \sqrt 2}(|0> + |1>);$$
	So this process produces true randomness because we have 50% probability that the bit will be either 1 or 0.

DEF:
> A transformation is **unitary** iff it preserves the norm of input.

- Prove that hadamard transformation is unitary
$$|\psi> = cos{\theta \over 2}|0> + e^{i\phi}sin{\theta \over 2}|1>;$$
### Interesting quantum systems
$$\sum_{i = 1}^n \alpha _ie_i$$
General reperesentation for a column vector of entries $\alpha_1, \alpha_2, ..., \alpha _n$, where $e_i$ is a vector with all 0s except for the entry in position $i$.

When we operate with qbits, cartesian product is not enough. We will have to deal with **tensor** product.

##### Tensor product 
Symbol used: $\otimes$. Product rules:$$\begin{pmatrix}\alpha_0\cr\alpha_1\end{pmatrix}\otimes\begin{pmatrix}\beta_0\cr \beta_1\cr\end{pmatrix} = \begin{pmatrix}\alpha_0\beta_0\cr \alpha_0\beta_1\cr \alpha_1\beta_0\cr\alpha_1\beta_1\cr\end{pmatrix};$$
That's how the product rule works.
Sometimes symbol $\otimes$ is dropped and is written like:$$|0>\otimes|1> = |01>;$$
###### EX: show that the resulting vector has norm 1
$$\begin{pmatrix}{1\over \sqrt 2}\cr{1\over \sqrt 2}\end{pmatrix}\otimes\begin{pmatrix}{1\over \sqrt 2}\cr {1\over \sqrt 2}\cr\end{pmatrix} = \begin{pmatrix}{1\over 2}\cr{1\over  2}\cr{1\over 2}\cr{1\over 2}\cr\end{pmatrix} = {1\over 2}(|00> + |01> + |10> + |11>);$$
This is the random state for a tensor product of 2 random states, it keeps random and his norm is still 1.

Any combination of 2 qbits that satisfies the property that the norm is 1, is a valid quantum state of 2 qbits.

There are states that cannot be represented as a tensor products, which are called **entangled states**. This state of entaglement forces a qbit to be 0 or 1 if the other entangled qbit is found to be 1 or 0.

##### EPR "paradox" 1935
Einstein P** Rosen

---
> **4th lecture**

CNOT primitve:
If a = 1, then b = Not(b)

| a   | b   | CNot(a,b) |
| --- | --- | --------- |
| 0   | 0   | 0         |
| 0   | 1   | 1         |
| 1   | 0   | 1         |
| 1   | 1   | 0         |

How  to represent this operation on Quantum Computing?
We apply |10> transformation.
CNOT(|00ۧ>) = |00ۧ>;
CNOT(|01ۧ>) = |01ۧ>;
CNOT(|10ۧ>) = |11ۧ>;
CNOT(|11ۧ>) = |10ۧ>;

We can use CNOT to create entangled state in QC.

### Notation
|a> --- x ---> |$\neg a$>
|a>--- H ---> ${1\over \sqrt 2}(|0> + (-1)^a|1>)$
CNOT
|a> --- . --- |a>
	   |
|b> ---x-----|a$\oplus$b>


Circuit example
In tensor product matrix grows exponentially with the number of multpicliactions.

### No-cloning theorem 
You can't copy a qbit.
There is no quantum transformation that copies an unknown quantum state.
PROOF
Suppose we have our copy machine, that given in input a file performs a copy. 
$\exists y \forall x CP(x,y) = (x,x)$
$\exists y \forall x,a CP(x+a,y) = (x+a,x+a) = x+a \otimes x+a$
$\exists y \forall x CP(x,y) = CP(x\otimes y + a\otimes y) = CP(x\otimes y) + CP(a\otimes y) = x\otimes x = a \otimes a$
while on the other side we have:
$x\otimes x + x\otimes a + a\otimes x + a \otimes a$
if we take x = 0 and a =1, this is disproved.
But you can still copy some states.

### Teleport a qbit
We can't clone it. We need 2 qbits and 2 normal bits.
Let's take the following quantum states for 2 qbits:$${1\over \sqrt 2}(|00> + |11>)$$

---
# Algebra recap

## Groups
A group is a set with a "multiplication" operation personalized.
This "multiplication" has some properties, such as:
- a(bc) = (ab)c;
- Identity;
- Inverse;

## Complex vector space

We can have a linear map L s.t.:$$V^* = \{L:V\rightarrow \mathbb{C}: L\ linear\}$$
EX:
Given $V$ such that $S = \{e_1,...,e_n\},\ [S]=V$.
then $\forall v\in V, v = \Sigma_{i = 1}^n\alpha_i e_i$ given $\alpha_1,...,\alpha_n\in \mathbb{C}$.
The complex coefficients are unique.

