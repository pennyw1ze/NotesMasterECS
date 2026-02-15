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
> 7/10/2025
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
then $\forall v\in V, v = \sum_{i = 1}^n\alpha_i e_i$ given $\alpha_1,...,\alpha_n\in \mathbb{C}$.
The complex coefficients are unique.

We have a particular definition of scalar product:
$(<u|v>) = (u_1* u_2* ... u_n*)\begin{pmatrix}v_1\cr v_2\cr ...\cr v_n\cr\end{pmatrix}$
We call this scalar product BRAKET, and we denote "<u|" as BRA, and "|v>" as KET.

**Swarz's inequality**
	$$|<u,v>| \leq \sqrt{<u|u><v|v>}$$
**Orthogonal**
Two vectors are ortogonal iff:$$<u|v> = 0$$
**Orthonormal**
Two vectors are orthonormal iff:$$<u_i,u_j> = \delta_{ij} \forall i,j\in \{1,...,m\}$$
Orthonormal bases:
$\forall u\in V,\ u =\sum_{i=1}^n\alpha_i e_i,\ \alpha_i \in \mathbb{C}$
$<e_j|u>=<e_j|\sum \alpha_i e_i> = \sum \alpha_i <e_j|e_i> = \sum \delta_{ij} = \alpha _j$

**Cauchy sequence**
$$\forall \epsilon>0,\ \exists n_\epsilon | \forall n,m \ge n_\epsilon ||v_n-v_m|| \leq \epsilon$$
**Hilber space**
Given:
$v_m \in V$ converges strongly to $v \in V$.
$lim_{m\rightarrow \infty}||v-v_m|| = 0$
Weak converges: $lim{m \rightarrow \infty}||v_m|| = ||v||$
Weak converges implies: $<u|v> = lim_{m\rightarrow \infty} <u|v_m>$

We want:
- Complex vector space;
- All cauchy sequences converge strongly to an element of space;
All finite dimension vector spaces are **Hilber**.

**Adjoint** of operator A is $A^+$a and we have that:$$\forall u,v\in H\ <u,A^+v> = <Au|v>$$
and we call it self-adjoint if $A^+ = A$.

U is **unitary** iff $UU^+ = U^+U = I$
U is **surjective** (each element in the codomain is the image of at least an element in the domain)  and <Uu|Uv> = <u|v> (so basically ||Uu|| = ||Uv||).

DEF:
$\lambda \in \mathbb{C}$ is d-fold degenerate if there are d linearly independent eigenvectors.
How to obtain the adjoint operator ?
$A^+_{ij} = A^*_{ji}$
Then  I can write any vector:
|v> as (|$\sum e_i$><$e_i$|v>)
So we can write the Identity operator as: |$\sum e_i$><$e_i$

Other properties:
- $(AB)^+ = B^+A^+$;
- $(\lambda A^+) = \lambda^*A^+$;
- $A^{+^+} = A$;

**Orthogonal complement**
$V^\bot = \{v\in H:\ \forall u\in V\ <u,v>=0\}$
$V^{\bot^\bot} = V$

---
> 15/10/2025

**Tensor product between vectors**
Given $u,v\in \mathbb{C}^n$ we define:$$u\otimes v = \begin{pmatrix}u\times v_1\cr u\times v_2\cr ...\cr u\times v_n\cr\end{pmatrix}\in \mathbb{C}^{n^2}$$
Property of the tensor product:
Given 4 matrices, L,M,N,P, we have:$$(MN)\otimes(LP) = (N\otimes P)(M\otimes L)$$
### Spectral theory

Remember self-adjoint and unitary implies Normal.

**Thorem:**
1. The eigenvalues of a **self-adjoint** operation are **real numbers**;
2. The eigenvalues of a **unitary** operator are complex numbers of modulus 1;
3. The eigenvectors of **self-adjoint and unitary** operators extracted to different eigenvalues are **ortogonal**;

Spectral theorem:$$\forall v\in H,\ v=\sum_{i=1}^m(\sum_{j=1}^{d_i}\alpha_{ij}u_{ij})$$
where $u_{ij}$ are eigenvectors and $\alpha_{ij}$ are the parameters.
We have 2 indeces because every eigenvalue can be associated to every number of eigenvectors, so we have m eigenvalues ($\lambda_1,...,\lambda_m$) where:
$dim(H)=\sum _{i=1} ^n d_i$
where $d_i$ is the degeneracy of eigenvalue (is **degenerate** if it corresponds to two or more different measurable states of a [quantum system](https://en.wikipedia.org/wiki/Quantum_system "Quantum system") "La molteplicità degli autovalori").
We will represent with |$\lambda$> the eigenvector associated with the eigenvalue $\lambda$.

And for some reason:$$A = \sum_{i=1}^m\lambda_iP_i$$
### Axioms of quantum mechanics
1. The sate space of a quantum system is a Hilbert space;
	
2.  Measurements are represented by self-adjoint operators;
	
3. Given an observable (measurement) A and a state $v\in H$, the expected result of measure A is $$<v|Av>=\sum_{i=1}^m \lambda_i<v|P_iv>;$$
4. A **closed** system evolves over time according to:$$i\hbar {dv(t)\over dt} = Hv(t)$$
   where H is called Hamiltonian (Closed = no interaction with extern).

3'. Given observable A and a state $v\in H$:
- The only possible results for measuring A are its eigenvalues;
- $Prob(A=\lambda;v)=<v|P_\lambda v>$;

---
For $t_2 \ge t1$ we have $v(t_2) = e^{-{i\over \hbar}H(t_2 - t_1)}v(t_1) = \sum_{j=1}^m e^{-{i\over \hbar}\lambda_j(t_2 - t_1)}P_j$
m eigenvalues for H.

Every quantum computation in a closed system have to be reversable.

### Quantum oracle
Let's say we have a complicate function (like hash function) 
$f:B \rightarrow B$
$U_f : (x,y) \rightarrow (x,y\oplus f(x))$ for example we could call f(x) "oracle".
In Qbits world:
$U_f : (x\otimes y) \rightarrow (x \otimes (y\oplus f(x)))$
example:
$U_f(({1\over \sqrt 2}|0> + |1>)\otimes |0>) = {1\over \sqrt 2}(|0f(0)> + |1f(1)>)$

```circuit
|0> ---[H]---|====|---[H]---[MEASURE]___: 0
			 | Hf |                  \__: 1
|1> ---[H]---|====|
```

Applying $U_f$ in this case means to change phase (sign) to the complex number such that:
$$U_f|a>\otimes ({|0>-|1>\over \sqrt2}) = (-1)^{f(a)}|a>({|0>-|1>\over \sqrt2})$$

### Deutsh-Jozsa problem
Evaluating weather a function is constant or balanced (outputs always the same value, or outputs half of the times 0 and half of the time 1).
Given a function that is either balanced ot constant , we need $2^{n-1}+1$ evaluation of that funciton in order to establish weather it is constant or balanced.
We use the same circuit as before:
```circuit
|0^n> --/^n--[H^n] --/^n-- |====|---[H]---[MEASURE]___: |0^n> => f is constant
                           | Hf |                  \__: else  => f is balanced
|1> ----------[H]----------|====|
```
The amplitude (probability of measuring)  the state $|0^n>$ is +/- 1 if f is constant, is 0 if the function is balanced. So if the measurement output is $|0^n>$ we are sure that the function is constant.

---
# Finding a needle in a haystack (1996)
| 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 'A' | 'G' | 'I' | 'T' | 'T' | 'R' | '0' | '1' |
**Setting**
We have an array. Let's take the length of the array and say is a power of 2: $N = 2^n$
We have $M$ solution elements. Classically, the best we can do is random search $O({N\over M})$.
Quantum case: $O(\sqrt{n\over m})$ (we have a small probability of error).
$f:\{0,...,N-1\}\rightarrow B$
$f(x) = 1 if A[x]$ in SOL, $0$ otherwise.

Remeber our oracle flips amplitudes of the solutions (the indexes).
New oracle:
$O_f|x> = (-1)^{f(x)}|x>$
```circuit
|0^n> --- [H^n] ---|---|--- .... ---|---| --- [Measure] ___: pointer to solution;
				   | G |            | G |               \__: non solution;
Ancilia qubits --- |---|--- .... ---|---|
```
where G is the grover operator and is applied exactly $\sqrt{N\over M}$ times.
$\psi = \sum \alpha_x|x>$
$<\alpha> = {1\over N}\sum\alpha_x$
$W|x> = (-\alpha _x + 2<\alpha>)|x>$
$G=WO_f$
W maps the basic state into another basic state.

Execution of the algorithm

|                   | \|00> | \|01> | \|10> | \|11> |
| ----------------- | ----- | ----- | ----- | ----- |
| Actual amplitude: | 1     | 0     | 0     | 0     |
| $H\otimes H$      | 1/2   | 1/2   | 1/2   | 1/2   |
| $O_f$             | 1/2   | 1/2   | 1/2   | -1/2  |
| $W$               | 0     | 0     | 0     | 1     |
given $<\alpha> = -{1\over 4};$

## Fixed point quantum search 
We build a sequence of operation recursively, we will collapse to the goal state no matter how many iterations we perform (no 'cooking souffle' problem).
We define a unitary operator U such that:$$U_{ts} = |<t|Us>|^2 = 1 - \epsilon;$$
U drives s to t with probability 1-e;
If we apply:$$UR_sU^+R_tU|s>$$
We drive s $(1-r)^3$ closer to t.
And we have iterations such that:$$U_{m+1}=U_mR_sU_m^+R_tU_m$$
So we will get an operator which grows up a lot and will reach goal with ($1-e)^{3m}$.
where:
$R_s =  I - [1-e^{i\Theta}]|s><s|$
$R_t =  I - [1-e^{i\Theta}]|t><t|$
with $\Theta = {\pi\over 3}$;

where $s$ and $t$ are the $x$ and $y$ axis of our vector.
$t$ is our target, we want to be as close as possible to $t$.
We have that $$UR_sU^+R_tU|s> = [U|s>(e^{i\theta}+|U_{ts}|^2(e^{i\theta}-1)^2)+|t>U_{ts}(e^{i\theta}-1)]$$
$|<t|U_{m}s>|^2=1-\epsilon^{2q_m-1}$
where $q_m$ is the number of oracle calls at step m.
It increases beacause at each level the Rt operation which is the only one that calls the oracle is duplicated ad each iteration of the unitary operator.
Now let us call $P_s = |s><s|$ and the same for t:

# Shor's algorithm

#### Discrete fourier transform
Can be performed in polynomial time in quantum computing, and only exponentially in classical computing.
It's applications are applied to the polynomail multiplication.

$$p(x) = \sum_{i=0}^{n-1}a_ix^i$$
$$q(x) = \sum_{j=0}^{n-1}b_jx^j$$
same for bj.
$$p(x)q(x) = \sum_{i,j}^{n-1}a_ib_jx^{i+j} = \sum_{k=0}^{2n-2}x^k\sum_j^{n-1}a_jb_{k-j}$$

Continue on samsung notes
#SamsungNotes
### Quantum Fourier Transform
Most important unitary transformation on quantum computers.
We live on an Hilbert space of dimension N (|0> |1> ... |N-1> )
The j-th basis state happens to:
Continue on samsung notes
#SamsungNotes 

### Phase extimation
The problem:
For any unitary operator any eigenvalue is complex of modulus 1.
means if $\lambda = e^{2\pi i\psi},\psi \in\{0,1\}$
How to find the phase ?
Suppose that U is unitary, $U|u> = e^{2\pi i \psi}|u>$

Circuit **C**:
```circuit
|0>---[H]---------------------------x-----x-
.                                   |
.                                   |
.                                   |
|0>---[H]x---------|----------------|
         |         |                |
|u>---[U^2^1]---[U^2^2]---...---[U^2^t-1]----|u>
```

Where u is an eigenvector of size n.

Suppose $\psi = 0.\psi_1...\psi_t$:$${1\over \sqrt2^t}(|0> + e^{2\pi i 2^{t-1}\psi}|1>)\otimes ...\otimes(|0> + e^{2\pi i \psi}|1>) $$
Notice that the final state of the circuit to find the phase is the same of the quantum fourier transform.
So now we can obtain our phases by applying the inverse Quantum Fourier Transform.
This will get my phases.
We apply this full circuit (**QPE**) Quantum Phase Extimation:

```circuit
|0>---|===|---|======|---|phi_1>
...   | C |   |QFT^-1|--- ...
|0>---|===|---|======|---|phi_t>
```

If we want to describe phi with n bits of precision with probability $1-\epsilon$, we have to run QPE with ($n + log(2+{1\over 2^\epsilon})$) qubits.

# Shor algorithm
Braking DLog and RSA.
**Order-finding** problem
given x,N integer where x < N and coprime( gcd(x,N) = 1).

I need to find the smallest exponent of 4 such that 4 mod 7 = 1.
The complexity is exponential in the size of the input.

$U_x$|y> = |xy mod N> for $y \le N$
$L = logN, y\in \{0,1\}^L$
if y > N then $U_x$ does nothing.

>**Claim**
>$U_x$ is unitary.

Proof
$U_x$|y> = |xy mod N>  $\implies U_x$ = |xy mod N><y|
$(AB)^+ = B^+A^+$
$U_x^+=\ket y\bra{ xy \bmod N}$
$$U_x^+U_x = (\sum_y|y>< xy \bmod N|)(\sum_z<xz \bmod N|<z|)$$
$$= \sum_{y=z}|y><z| + \sum_{y\neq z}|y><...|..><z|$$
$$= I + \sum_{y+z\ge N}|y><...><z| + ...$$

Nice book Hardy theory of numbers

**Recap on shor:**
Order-finding problem:
$U_x\ket y = \ket{xy \bmod N}$ for $y \le N$
$L = logN, y\in \{0,1\}^L$
What are eigenvalues and eigenvectors of $U_x$?
For any $s \in \{0,R-1\}$ where R is the order of x, we have that:$$\ket {u_s} = {1\over \sqrt r} \sum_{k=0}^{r-1}e^{-2\pi isk\over r}\ket {x^k\bmod N}$$
is an eigenvector of $U_x$. To prove this, we have to find $\lambda\ s.t.\ U_x\ket{u_s} = \lambda\ket{u_s}$.
$\lambda$ is just a complex number with modulus one! (eigenvalue of our unitary quantum< transformation).
Notice that if y > N, then our eigenvalues are 1 ($U_x$ is the identity because the output of the modulus operation is y itself, does not get changed).
**Proof**:
$$U_x\ket{u_s} = U_x{1\over \sqrt r} \sum_{k=0}^{r-1}e^{-2\pi isk\over r}\ket {x^k\bmod N} =$$
$$= {1\over \sqrt r} \sum_{k=0}^{r-1}e^{-2\pi isk\over r}U_x\ket {x^k\bmod N} =$$
$$= {1\over \sqrt r} \sum_{k=0}^{r-1}e^{-2\pi isk\over r}\ket{ x (x^k\bmod N)\bmod N} =$$
$$= {1\over \sqrt r} \sum_{k=0}^{r-1}e^{-2\pi isk\over r}\ket{x^{k+1}\bmod N} =$$
$$= {1\over \sqrt r}e^{2\pi is\over r}e^{-2\pi is\over r}\sum_{k=0}^{r-1}e^{-2\pi isk\over r}\ket{x^{k+1}\bmod N} =$$
$$= {1\over \sqrt r}e^{2\pi is\over r}\sum_{k=0}^{r-1}e^{-2\pi is(k+1)\over r}\ket{x^{k+1}\bmod N} =$$

$$= {1\over \sqrt r}e^{2\pi is\over r}\sum_{k=0}^{r-1}e^{-2\pi is(k)\over r}\ket{x^{k}\bmod N} =$$
$$= {1\over \sqrt r}e^{2\pi is\over r}\ket{u_s}$$
where ${1\over \sqrt r}e^{2\pi is\over r}$ is an eigenvalues with modulus one (our $\lambda$).
To extimate the phase of the eigenvalues which corresponds to finding the $U_x$ transformation. To implement the circuit we need t qbits and t U gates. To build U (modular exponentiation) it can be done in $O(L^3)$.
This circuits acts in polynomial time.
Instead of inserting u_s in our circuit we will insert $\ket 1$ which is a super position of all the possible quantum state of our phase. To show this we have a proof:
$$\ket 1 = {1\over \sqrt r}\sum_{s=0}^{r-1}\ket{u_s}$$
**Proof**
$$\ket 1 = {1\over \sqrt r}\sum_{s=0}^{r-1}\ket{u_s} =$$
$$= {1\over \sqrt r}\sum_{s=0}^{r-1}{1\over \sqrt r}\sum_{k=0}^{r-1}e^{-2\pi i sk\over r} \ket{x^k\bmod N}= $$
$$= {1\over r}\sum_{s,k=0}^{r-1}e^{-2\pi i sk\over r} \ket{x^k\bmod N}= $$
$$= {1\over r}\sum_{s=0}^{r-1}\ket1 + \sum_{s=0,k=1}^{r-1}e^{-2\pi i sk\over r} \ket{x^k\bmod N}= $$
$$= \ket1 + \sum_{k=1}^{r-1}\ket{x^k\bmod N}
\sum_{s=0}^{r-1}{e^{-2\pi i sk\over r}} = $$
$$= \ket1 + \sum_{k=1}^{r-1}\ket{x^k\bmod N}
\sum_{s=0}^{r-1}{e^{({-2\pi i k\over r})^s}} = $$
geometric series:$$= \ket 1 + \sum_{k=1}^{r-1}\ket{x^k\bmod N}{1-e^{({-2\pi i k\over r})^r}\over 1-e^{-2\pi i k\over r}} = \ket 1$$
because $e^{({-2\pi i k\over r})^r} = e^{-2\pi i k} = 1$

**Integer factoring**
Any integer number N can be written as product of prime numbers.
Factory can be reduced to order finding.

# Quantum Crypto
...

---
#### Principle of deferred measurement
We can put measurements at the beginning or at the end of the circuit, the outcome does not change at all.
b = measure(q_0)
if b then q_1: = U(q1)

is equal to:
q0,q1: = Control-U(q0,q1)
b:= measure(q0)

ecc...
##### Base changing
How to change unitarly from a basis to another ? With projection.
Given 2 finite hilber space {e_i} and {f_i}, and we want to change from f to e.
Two orthonormal bases for our Hilber space.
$$\sum_{i=0}^n\ket{e_i}\bra{f_i}$$
For example we can turn from |0>, |1> into |+>, |- >.
Given $H|0> = |+>$ and $H\ket 1 = \ket - \ and\ H = H^+$.

#### Bell's inequalities
Alice measures Q or R (self adjoint operators on single qbit)
Bob measures Sor T (self adjoint operators on single qbit)
Q,R,S,T have +/- 1 as eigenvalues.
Alice and Bob decide what to measure after receiving their qbit (realism).
Alice and Bob measure at the same time (locality).

**Inequality**:
$QS + RS + RT - QT \le 2$
$E[QS + RS + RT - QT] = E[QS] + E[RS] + E[RT] - E[QT] \le 2$

We define: 
Q = Z (Pauli operator)
R = X (not)
S = -Z-X / sqrt(2)
T = Z-X / sqrt(2)

Expected value of measuring A on v (A self-adjoint) = <v, Av>;
The result of measuring Q and S on state $\psi$ is:$$E[QS] = \braket{\psi,QS\psi}= \braket{QS}_\psi = {1\over\sqrt2} = E[RS] = E[RT] = -E[QT]$$
The sum is $2\sqrt2$ which is higher then 2.
Applications of this theorem: Quantum key distribution.
This check can be used in comunication channel in order to check weather we are being spied by eavesdropper or if there is too much noise in the comunication channel.

How to compute $E[QS] ?$$$E[QS] = \braket{\psi|QS\psi} = \braket{\psi|Z\otimes{-Z-X\over\sqrt2}\times \psi}$$

---
## Programming lenguages for quantum computers

Some known lenguages:

- **Q#**: open source, by microsoft;
- **Quipper (Academia)**;
- **Qwire**;
- **Qiskit** (IBM);
- **CIRQ** (Google);

# Silq
Academic lenguage. It has the better abstraction with respect to others. Not so fast to run. You got to write quantum cicuits to program.
**Main features**:
1. Implements the **QRAM** model (System composed by CPU and quantum bits, and CPU manipulates this quantum bits);
2. **Type system**: silq has statical types. Types for data and for functions.
3. **Automatic uncomputations** of subroutines.

### Types
**B** for boolean values (actual qbit);
**N** for natural numbers;
**Q** for rational numbers;
int[n] for n bits integer (array of n bits, that are actually nqbits);
So our quantum types are **B** and **int[n]**.

We have cartesian product (s_1\*s_2), we have lists(s[]), we have an n vector of types(s^n).
!s is classical type for classic elements generation.
We also have to assign types to functions.

#### Type table

| Annotation | Applies    |                                                                                                                            |
| ---------- | ---------- | -------------------------------------------------------------------------------------------------------------------------- |
| mfree      | functions  | does not measure its input.                                                                                                |
| qfree      | functions  | does neither introduce nor destroy any superposition.<br>Example: an oracle. Hadamard is not qfree. Not gate is qfree.<br> |
| const      | parameters | function does not modify its input                                                                                         |
| lifted     | function   | qfree and all parameters are constant                                                                                      |
If your function does not modify the input, it's parameters **must** be const.

Silq is able to duplicate quantum bits which differs from clonig quantum bits because:
Duplication:$$\alpha\ket0+ \beta\ket1\rightarrow\alpha\ket{00} + \beta\ket{11}$$
Cloning:$$\alpha\ket0 + \beta\ket1 \rightarrow (\alpha\ket0 + \beta\ket1)\otimes(\alpha\ket0 + \beta\ket1)$$

---

# Quantum counting

An array of N elements with M solutions: find M.
Costs:
- $O(N)$ in classical computing;
- $O(\sqrt N)$ in quantum computing;

We need M in many applications such as grover algorithm (find number of solutions is needed in order to know how many times to "flip" the souffle).

Grover G is:$$G = \begin{bmatrix}cos\theta &-sen\theta\cr sen\theta&cos\theta\cr\end{bmatrix};$$
**Proof**
...

---
# Variational quantum algorithms

There is this wonderfull new algorithm, **VQE** variational quantum eigensolver.
A is an hermitian matrix. Find the extremal eigenvalues of A (the largets or the smallest).
An hermitian matrix has all real eigenvalues.
Optimization problem: c:X -> R cost function, and I have to find the min C(x) x in S.

Assume X finite.$$H_c=\sum_{x\in X}C(x)\ket x\bra x$$
is hermitian (projection is selfadjoint (|x><x|) ).$$H_c\ket a= C(a)\ket a \forall a \in X$$
Generate candidates: v
depending on parameters theta in R^p
Theta \in R^p 

---
# Quantum error correction

![[Pasted image 20251126142611.png]]

Suppose now that we are in a quantum environment and Alice wants to send α0 |0⟩ + α1 |1⟩.
Then she'll send: (α0 |0⟩ + α1 |1⟩) ⊗ |00⟩ = α0 |000⟩ + α1 |100⟩.
We can do a parity check on the received qbits.
In a single qbit flip how the detection happens?
Single bit-flip α0 |100⟩ + α1 |011⟩ or α0 |010⟩ + α1 |101⟩ or α0 |001⟩ + α1 |110⟩

Note that the four vectors are pairwise orthogonal. Bob can then build four
projectors that tell him which bit has flipped!

| Projector                   | Error occurred         |
| --------------------------- | ---------------------- |
| \|000⟩⟨000\| + \|111⟩⟨111\| | No error               |
| \|100⟩⟨100\| + \|011⟩⟨011\| | **First** bit flipped  |
| \|010⟩⟨010\| + \|101⟩⟨101\| | **Second** bit flipped |
| \|001⟩⟨001\| + \|110⟩⟨110\| | **Third** bit flipped  |

![[Pasted image 20251126143311.png]]

**Phase flip**
Applies $\sigma_z$. so does nothing on the 0 state and flip the phase (sign) on the 1 state.
How can we detect errors?
We use this translations:
- |0⟩ → |+ + +⟩;
- |1⟩ → |− − −⟩;

We apply the same method as before:

| Projector                           | Error occurred         |
| ----------------------------------- | ---------------------- |
| \|+ + +⟩⟨+ + +\| + \|- - -⟩⟨- - -\| | No error               |
| \|- + +⟩⟨- + +\| + \|+ - -⟩⟨+ - -\| | **First** bit flipped  |
| \|+ - +⟩⟨+ - +\| + \|- + -⟩⟨- + -\| | **Second** bit flipped |
| \|+ + -⟩⟨+ + -\| + \|- - +⟩⟨- - +\| | **Third** bit flipped  |
- To flip signs we apply $\sigma_z$;
- To go back to the original state we apply the Hadamard again;

### Arbitrary errors

THM:
If you can correct up to k pauli errors (on k different qbits), then u can correct arbitrary errors.
Proof: lol

# Hamming code
(Classical)
Maps 4 bits to 7 bits.
$$G = \begin{bmatrix}0&0&&0&1&1&1\cr0&1&1&0&0&1&1\cr1&0&1&0&1&0&1\cr1&1&1&1&1&1&1\cr\end{bmatrix}$$
This is called the generator matrix.

I insert a message and I get back a codeword. The message is 4 bits long. The codeword will be 7 bits long.
How to decode:
We take 4 linear independent columns of G in our new matrix G*. Then we compute $G^{*-1}$.


**Hamming weight** number of 1's in the bitstring.
**Hamming distance** between 2 codewords c1, c2 d(c1,c2) is the weight of c1 minus c2.

If a code has min weight d>0, then this code can correct t = $d-1\over2$ errors and detect d-1 errors.
We define:
$G = (I_k|-A^T)$
$H = (A|I_{n-k})$

THM:
First, we show that if a codeword w has at most t errors we can correct with the
unique codeword within distance t from w.

Parity check: H^T

Assume e = (0,0,0,0,1,0,0)
Where 1 is a bitflip error.
$r = mG \oplus e$.
to check for errors we compute:
$rH^t  = (mG \oplus e)H^T = mGH^T \oplus eH^T = eH^T$
If there is 1 error I get back  1 of the raws of $H^T$.

## Quantum Hamming

We map:
- $\ket0 \rightarrow {1\over\sqrt 8}\sum_{c\in[H]} \ket c$;
- $\ket1 \rightarrow {1\over\sqrt 8}\sum_{c\in[H]} \ket {c\oplus 1111111}$;

We compute a 3-bit syndrom similar to classical computing:
\[(b4 ⊕ b5 ⊕ b6 ⊕ b7),( b2 ⊕ b3 ⊕ b6 ⊕ b7),( b1 ⊕ b3 ⊕ b5 ⊕ b7)\]

![[Pasted image 20251126165040.png]]

---
**Remember**:$$\forall U \ unitary, \ Uu=\lambda u \implies |\lambda|=1 $$
Unitary transformations has eigenvalues with modulus 1 (complex).

## Quantum recurrence theorem
The Grover behaviour of cooking the souffle is a common behaviour in quantum computing algorithms.
Suppose we have a unitary operator U on a finite-dimension hilbert space.
$\forall v \in H, \forall \epsilon > 0, \exists k \ge 1 ||U^ku-v||< \epsilon$ 

---
# Solving linear equations HHL

NxN hermitian matrix A, unit values b. Find x: Ax=b
x = $A^{-1}b$

Condition number:
k =  |largest eigenvalue of A| over |smallest eigenvalue of A|

Assumption of HHL: 
1. The singular values of A are between 1/k and 1. Square root of the eigeinvalues of $A^+A$;
2. A is a s-sparse, A has at most s non-zero elements per row;
3. A is efficiently computed entries of A can be computed in O(s) time;

COST:
- **Classical**: $O(Nsklog({1\over \epsilon}))$ so we could arrive to $n^3$ complexity;
- **Quantum**: $O(logN)s^2k^2{1\over \epsilon}$;

Input matrix $$A = \sum_{j=1}^N \lambda_i\ket{u_j}\bra{u_j}$$


T large enough to store A's eigenvalues.
We define our unitary operator as:$$U = e^{iAt_0\over T} = \sum_{j=0}^{N-1}e^{i\lambda_jt_0\over T}\ket{u_j}\bra{u_j}$$
where $\lambda$ are the eigenvalues of A.
$$U\ket b$$
where $\ket b = \sum_{k=0}^{N-1}\beta _k \ket {u_k}$
so:$$U\ket b = \sum_{k=0}^{N-1}\beta _k U\ket {u_k} = \sum_{k=0}^{N-1}\beta _k \sum_{j=0}^{N-1}e^{i\lambda_jt_0\over T}\ket{u_j}\bra{u_j}\ket{u_k}$$

---
# Density operator

$\ket v$ is a pure state, a vector in a Hilbert space.
A **mixed state** is, given $n$ pure states and $\sum_{i=0}^{n-1}p_i = 1, p_i\ge 0$, then $\rho = \{\ket v_i, p_i\}$.
This mixed state is just a regular random variable.
A mixed state tells us that our quantum state is in a pure state with classical probability $w_i$.
$Prob(A=\lambda_i;\ket v) = <v,P_iv>$.
$Prob(A=\lambda_i;\rho) = \sum p_i Prob(A-\lambda _i;\ket v)$

DEF:
A trace of a matrix is defined as:
$tr(A) = \sum_{i=0}^n<e_i|Ae_i>$
This sum is actually a number.
where $e_i$ are orthonormal basis.

**Density matrix**
- $\rho$ is positive $\forall v \in H, <v,\rho v> >= 0$;
- $tr(\rho) =1$;
- $\rho = \rho^+$;

RULE 1) A state of a quantum system is a density matrix
RULE 2) Discrete time: a unitary evloution starting from state p and applied to U evolves into UpU^+
Remember that if we apply a unitary transformation to a state of a mixed state the probabilities assigned to the sates will not change.

a superoperator maps density matrixes to density matrixes.
A measurement is described by a collection of measurment operators M such that
$\sum MM^+ = I$

---
**Composite systems**

Systems with more then 1 qbit:
pure states -> tensor product of vectors;
mixed states -> tensor product of density matrix;

We will have entanglement both in pure and mixed states.
The CNOT operation for example: (1000
							0100
							0001
							0010)

Suppose we have our 2 qbits and 2 density matrix A and B that describes the states of those 2 qbits. We can describe the state of just one qbit with the **partial trace**.

Reduced density operator:
trB (|a1⟩⟨a2| ⊗ |b1⟩⟨b2|) = |a1⟩⟨a2|tr(|b1⟩⟨b2|)

**Superoperators** = linear functinos that maps operator to operators.

**DEF** any phisically allowed superop. maps density matrix to density matrix.
T is completely positive if T$\otimes$I is positive.

T:L($H_1$)$\rightarrow L(H_2)$.
T is CPTP (completely positive, trace preserving which means physically allowded) iff $\exists$ an hilber space F s.t. $dim(F) \le dimH_1dimH_2$
and unitary embedding V: $H_1 \rightarrow H_1$
$\forall p\in L(H_1) T(\rho) = tr_F(V\rho V^+)$

A quantum gate is a CPTP superop from k qbits to l qubits.

Total variation distance:
$|f_i - g_i| = \sum_j|f_{i,j} - g_{i,j}|$

$||f_i - g_i|| = max_i|f_i - g_i|$

Proposition:
Given 2 density matrix $\rho_1, \rho_2$, the distance between those 2 matrixes is:$$||\rho_1 -\rho_2|| = max_O|P_1^O-P_2^O|$$

---
**Shannon's enropy**
Heads: @ P
Tails:    @ 1-P
bernoulli random variable.

H(p) is the measurement of the entropy.
$H(p) = -plog_2(p) - (1-p)log_2(1-p)$
H(p) is max for p = 1/2 and min for p = 0 or 1.

Joint entropy:
$H(x,y) = \sum_{x,y}p(x,y)logp(x,y)$

THM: Holevo's bound
Alice prepares $\rho_x$ with probability $p_x$ where x = 1,.., n;
When Bob receives $\rho_x$, perform some measurments y. Then $H(X:Y) \le S(\rho) - \sum p_i ;pgS(p_i) \le H(x)$
where $S(\rho) = \sum\rho_ip_i$.

---
**Turing** machines are countable.
**Quantum circuits** are uncountable.

**Famous gates**:
$X = \begin{bmatrix}0&1\cr1&0 \end{bmatrix}$

$Y = \begin{bmatrix}0&-i\cr i&0 \end{bmatrix}$

$Z = \begin{bmatrix}1&0\cr0&-1 \end{bmatrix}$

$H = {1\over \sqrt2}\begin{bmatrix}1&1\cr1&-1 \end{bmatrix}$

$S = \begin{bmatrix}1&0\cr0&i \end{bmatrix}$

$T = \begin{bmatrix}1&0\cr0&e^{i{\pi \over 4}} \end{bmatrix}$

T gates are considered hard to build and their number must be minimized.

$R_x(\theta) = \begin{bmatrix}cos({\theta\over 2})&-isen({\theta\over 2})\cr-isen({\theta\over 2})&cos({\theta\over 2})\end{bmatrix}$

THM:
U unitary operation on a single qbit, then $\exists \alpha,\beta,\gamma,\delta\ s.t.\ U = e^{i\alpha}R_x(\beta)R_y(\gamma)R_z(\delta)$

Proof:
U is 2x2 matrix. The rows and columns of the unitary matrix are orthonormal. 
Suppose we take: $Ue_i = (u_{i,1},...,u_{i,n})$ so $||Ue_i|| = ||(u_{i,1},...,u_{i,n})|| = 1$, so we know that columns of U have norm 1.
Doing the same operation with $U^+$ we get the same for the rows of U.
For orthogonality, we already know that $\forall i,j,\ \delta_{i,j}=<e_i,e_j>$.
Since U is unitary, I can do this:  $\forall i,j,\ \delta=<e_i,e_j> = <e_i,UU^+e_j> = <Ue_i,Ue_j> = \delta_{i,j}$.
In this way we prooved that rows and columns of U are orthonormal.
Since rows and columns are orthonormal, and 
$U  = \begin{bmatrix}u_{1,1}&u_{1,2}\cr u_{2,1}&u_{2,2}\end{bmatrix} = \begin{bmatrix}e^{i(\alpha-{\beta\over 2}-{\delta\over 2})}cos({\delta \over 2})&-e^{i(\alpha-{\beta\over 2}-{\delta\over 2})}sin({\delta \over 2})\cr e^{i(\alpha+{\beta\over 2}+{\delta\over 2})}sin({\delta \over 2})&e^{i(\alpha+{\beta\over 2}+{\delta\over 2})}cos({\delta \over 2})\end{bmatrix} = e^{i\alpha}\begin{bmatrix}e^{(-{\beta\over 2}-{\delta\over 2})}cos({\delta \over 2})&-e^{i(-{\beta\over 2}-{\delta\over 2})}sin({\delta \over 2})\cr e^{(+{\beta\over 2}+{\delta\over 2})}sin({\delta \over 2})&e^{i(+{\beta\over 2}+{\delta\over 2})}cos({\delta \over 2})\end{bmatrix}$

**Corollary**
U unitary single qbit, then $\exists A,B,C\ s.t.\ ABC=I,\ U=e^{i\alpha}AXBXC$
where X is the not gate.
$A = R_z(\beta)R_y({\delta \over 2})$
$B = R_y({\delta \over 2})R_z({-(\delta + \beta)\over2})$
$C = R_z({\delta-\beta \over 2})$

Give XX = I.
How to represent U as a circuit:
```circuit
----x----       -----x------x-------[ 1  0    ]--------------
    |                |      |       [ 0  e^ia ]
	|        =       |      |          
--[ U ]--       ---[ C ]--[ B ]----[ A ]---------------------
```

We need a gate over at least 3 qbits in order to implement it in a reversible way.
The Toffoli's gates can be used to represent any quantum reversable circuit.

Exact implementation:
single-qbit gates + CNOT are universal

DEF: 
A 2-level unitary matrix acts non-trivially on max 2 components of the target vector to which the matrix is applied.

PROP:
Unitary U on n qbits U = product of $2^{n-1}(2^n-1)$ 2-level ($2^n\times 2^n$) matrixes.

THM:
Unitary on n qbits, then U = circuit with $O(n^24^n)$ single qubits + CNOTS

**Sloway Kitaev** THM:
U single qbit, $\epsilon > 0$, U can be written up to epsilon precision with a finite sequence of H,S,T gates only. The length of the sequence is  approximately $log({2\over \epsilon})^c$ where c is circa 2.

Clifford Gates = {H,S,CNOT, Single Q-measure}
This gates are easily simulatable in polynomial time on a classical machine with use of pseudo-randomness.

