# PQ
Directions for **post quantum cryptography** security:
- Lattice based;
- Code-based;
- Hash/symmertric key based signature; 

## Impagliazzo's world
•**Algorithmica**: P = NP or something "morally equivalent" like fast probabilistic
algorithms for NP.
•**Heuristica**: NP problems are hard in the worst case but easy on average.
•**Pessiland**: NP problems hard on average but no one-way functions exist. We
can easily create hard NP problems, but not hard NP problems where we
know the solution. This is the worst of all possible worlds, since not only can
we not solve hard problems on average but we apparently do not get any
cryptographic advantage from the hardness of these problems.

Impagliazzo teorized worlds where the NP completeness theory is resolved (P = NP) or similar. The 2 most interesting are:
- **Minicrypt**: One way functions exists, but public key cryptography does not exists ( this is a possible world when quantum computers will be used in practice since diffie hellman and RSA will be broken );
- **Cryptomania**: One way functions and public cryptography both exists (the actual world);

Post quantum advent brings in new challenges. The most important is:

> **Shor algorithm**
> Shor algorithm brakes both Diffie-Hellman encryption and RSA encryption. This implies the end of Public Key cryptography as we actually knows it.


new algorithms have been theorized to resist quantum attacks, but everything is in research field.

##### Grover's algorithm
Establish that for a quantum computer a cost of $O(n)$ becomes $O(\sqrt{n})$.
This must be taken into account, but can be fixed by just increasing the size of the keys.
(ex. AES256 instead of AES128 ecc...)

For this scope has been invented some technologies such as **quantum key distribution**, used to distribute keys, and when the key is opened, it's bits get phisically altered and is compromised.

---
# Zero knowledge

Quick recap on main properties:

#### Completeness
$$For\ all\ (x,w) \ \in R_L, Pr[<P(w),V>(x)\ =\ 1] = 1;$$
Basically, if the proover has a correct witness $w$ for a problem $x$ in the lenguage $L$ (lenguage realm $R_L$), then the probability that the proover $P$ convinces the ferifier $V$ is 1. This is done by running an execution of the protocol with the angular parenthesis.

#### Soundness
$$\exists \epsilon(x)\ negligible\ function\ s.t.\ \forall x\notin L\ and \forall P*, Pr[<P*,V>(x)\ =\ 1] < \epsilon(x);$$
The probability that during an execution of the alogrithm a malicious proover $P*$ that does not own any witness convinces a verifier $V$ is negligible (small probability w.r.t. x).

#### Proof of knowledge
If a malicious prover $P*$ can prove $x\in L$ with non negligible probability, then an extractor algorithm can obtain the witness in polinomial time.


#### Zero knowledge
A proof system $Pi=(P,V)$ is zero knowledge if exists an expected PPT alogrithm S s.t. $\forall V*$, any  $(x,w)\in R_L$  and  any $z\in\{0,1\}*$, the followin 2 distribution are computationally indistinguishable:$$\{<P(w),V*(z)>(x)\},\{S^{V*}(x,z)\}$$
This basically means that we can run an internal simulation without the knowledge of the witness, and produce a sequence of comunication identical to the one produced by a prover that actually ownes a valid witness. This means that a proof that holds the zero knowledge property can be runned succesfully also by a single player not knowing the witness.

> **Blam's protocol**
> Goal: proof the knowledge of an hamiltonian cycle in a graph.
> Prover sends a committed random permutation of a graph (sends commitment of a graph obtained by permuting the original graph into a new one);
> Verifier sends a bit b = 0 or 1
> Prover answare in the way:
> b = 0:
> 	Opens a cycle in the permuted graph;
> b = 1:
> 	Opens all the commitment and send the permutation;
> If b = 0, the prover shows his knowledge of the witness (the cycle in the graph), if b = 1 the prover shows that the permutation he sent is a valid permutation and that he is not cheating. 
