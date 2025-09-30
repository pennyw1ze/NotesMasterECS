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

> Complex numbers:

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
## Exercise
#### Ex 1)
Compute $(4 + {i \over 2})(1 + i) = 4 + 4i + {i\over 2} - {1\over 2} = {7\over 2} +  {9i\over 2}$
#### Ex 2)
${3 + 7i\over 2 + 5i}$
To get in standard form I multiply over and above for the complementar complex number.

