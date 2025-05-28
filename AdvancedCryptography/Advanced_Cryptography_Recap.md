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
