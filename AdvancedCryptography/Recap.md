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

#### Proof of knowledge (Knowledge soundness)
If a malicious prover $P*$ can prove $x\in L$ with non negligible probability, then an extractor algorithm can obtain the witness in polinomial time.
In real life, usually, we say that if someone claims she knows something secret, then there must be a way to extract the secret from her!

#### Zero knowledge
A proof system $Pi=(P,V)$ is zero knowledge if exists an expected PPT alogrithm S s.t. $\forall V*$, any  $(x,w)\in R_L$  and  any $z\in\{0,1\}*$, the followin 2 distribution are computationally indistinguishable:$$\{<P(w),V*(z)>(x)\},\{S^{V*}(x,z)\}$$
This basically means that we can run an internal simulation without the knowledge of the witness, and produce a sequence of comunication identical to the one produced by a prover that actually ownes a valid witness. This means that a proof that holds the zero knowledge property can be runned succesfully also by a single player not knowing the witness.

> **Blum's protocol**
> Goal: proof the knowledge of an hamiltonian cycle in a graph.
> Prover sends a committed random permutation of a graph (sends commitment of a graph obtained by permuting the original graph into a new one);
> Verifier sends a bit b = 0 or 1
> Prover answare in the way:
> b = 0:
> 	Opens a cycle in the permuted graph;
> b = 1:
> 	Opens all the commitment and send the permutation;
> If b = 0, the prover shows his knowledge of the witness (the cycle in the graph), if b = 1 the prover shows that the permutation he sent is a valid permutation and that he is not cheating. 

### Commitment scheme

Send a committed message. We want to get the following properties:
- **Correctness**: honest Alice accept opening if Bob is honest;
- **Binding**: Unlucky that Bob can open 2 different messages;
- **Hiding**: Unlucky that Alice get info on m before the opening is sent;

#### Pederson commitment scheme

> $Comm(m) = g^mh^r$, where r is a random number;

- Computationally binding;
- Perfect hiding;

#### Random oracle
Random oracle model:
SHA256
Generate random string based on the input. Same input same string.
Use heuristic assumption but is post quantum secure.


### Schnorr protocol
Prooving Dlog of $y = g^x$
A -----$g^r$------>B
A<----$c$---------B
A----$z = r  + cx$-->B

B verify:
$g^z = ay^c = g^rg^{xc} = g^{r+cx}= g^z$

> **Completeness**: guaranteed by the proof that the B verification is correct;

> **Soundness**: There is nothing to cheat on, verifier is just sending challenge c;

> **PoK** (Proof of Knowledge): Malicious verifier V with his extractor algorithm can run 2 times the proover with the same randomness and get $z = r + cx$ and $z' = r + c'x$. Now the verifier can easily compute x as: $x = {(z'-z)\over (c'-c)}$

> **HVZK** (Honest Verifier Zero Knowledge): Is a particular instance of the Zero Knowledge property in which the verifier is honest.


#### Fiat shamir transform
The Schnorr protocol can be transformed by means of the Random Oracle model. We need SHA256 to perform a transformation so we can run the protocol in a single message and compute it by our own.
New paradigm:

A --------- $(a = g^r, c = H(y||a), z = r + cx)$ -------> B
In this way we commit c in a univoque way and we tie c to our random generated r and our instance y of the discrete log, and we are for sure taking a random $c$ since we are SHA256 compliant. We introduce heuristic assumptions.

Fiat Shamir obviously keeps the same property as the Schnorr version of the protocol.
The fiat-shamir transform of the Shnorr protocol satisfies the Zero Knowledge property also with dishonest verifier, since the verifier cannot interact in the protocol, and the only verification that he will do will be the check of the correctness of the statement sent by the prover. But how can the prover prove correctly the knowledge?
The prover will compute a random c and a random z, and compute a as: $a = {g^z\over y^c}$;
The verifier check will be correct except for the fact that $H(y||a)$ would be different from c. This does not matter at all because inside the simulation we can control the Random Oracle model even for the malicious verifier.


Schnor ID ?
Special Soundness ?

> [!NOTE]
> Special Soundness $\not \Rightarrow$ Soundness


#### Schnorr signature
We can use schnorr protocol to sign messages.
We can just do the normal fiat shamir transform of Schnorr protocol and then insert into the hashing function also the signed message, and c will be $c = H(y||a||m)$.
Schnorr signature is aggregatable: this means that if 2 person signed the same message m, we can aggregate those signature by means of an easy opeartion
Given the frist signature $a_1,c_1,s_1 = x_1 + c_1x_1$ and the second one $a_2,c_2,s_2 = x_2 + c_2x_2$
we can aggregate signature in the following way:$$s = s_1 + s_2 = r_1 + r_2 + (x_1 + x2)c,\ a = a_1 a_2, y = y_1y_2\ , g^s = ay^c = g^{r_1+r_2}g^{(x_1+x_2)c}$$
where s is our combined signature obtained as shown above.


# Blockchain

## Tornado Cash

## ZCoin

## ZCash

## ZK-SNARKs