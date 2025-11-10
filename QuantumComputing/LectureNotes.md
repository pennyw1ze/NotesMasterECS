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

Graphically:
at the beginning we have scrotus.

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

**Order-finding** problem
given x,N integer where x < N and coprime(x,N = gcd(x,N) = 1.

I need to find the smallest exponent of 4 such that 4 mod 7 = 1.
The complexity is exponential in the size of the input.

# Shor algorithm
Braking DLog.
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
$\lambda$ is just a complex number with modulus one! (eigenvalue of our unitary quantum transformation).
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
To extimate the phase of the eigenvalues which corresponds to finding the $U_x$ transformation