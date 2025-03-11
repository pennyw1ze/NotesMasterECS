# PQ
Directions for **post quantum cryptography** security:
- Lattice based;
- Code-based;
- Hash/symmertric key based signature;

## Impagliazzo's world
Impagliazzo teorized world's where the NP completeness theory is resolved (P = NP) or similar. The 2 most interesting are:
- **Minicrypt**: One way functions exists, but public key cryptography does not exists ( this is a possible world when quantum computers will be used in practice since diffie hellman and RSA will be broken );
- **Cryptomania**: One way functions and public cryptography both exists (the actual world);
Problems with RSA and DIFFIE-HELLMAN: Shor algorithm.
new algorithms have been theorized to resist quantum attacks, but everything is in research field.

##### Grover's algorithm
Establish that for a quantum computer a cost of $O(n)$ becomes $O(\sqrt{n})$.
This must be taken into account, but can be fixed by just increasing the size of the keys.
(ex. AES256 instead of AES128 ecc...)

For this scope has been invented some technologies such as **quantum key distribution**, used to distribute keys, and when the key is opened, it's bits get phisically altered and is compromised.

# Zero-knowledge
Used for zcash or cryptocurrencies.
