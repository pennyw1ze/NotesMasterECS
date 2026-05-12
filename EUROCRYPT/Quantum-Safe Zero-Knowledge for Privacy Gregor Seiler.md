More then 65% internet traffic on cloudflare is encrypted in ML-KEM (Kyber).
Web quantum safeness with ML-DSA should happen in 2029.

Electronic identities.
Certificates are signatures of credentials.
No replacement for nice proof-friendly standard model signatures such as BBS.
We can modify the schemes that we have now to make them easier to prove.

pk = (A,t) sign = (c,z)

ML-DSA
Verification
w = Az- ct
H(w,pk,msg) = c
$||z||<= \beta$
Not proof friendly: SHAKE hash function is severe with respect of zero knowledge proofs.

FALCON:
pk = A, sig = s
Verification:
As = H(msg) ? is the verification check
$||s||<= \beta$

Of course we could replace the Hash function with more zk proof friendly hash functions like Poseidon.

Even better under MSIS assumption, replace H(msg) with $B\mu$ where $\mu = bin(msg)$.

Merkle tree credentials:
zk-creds:
- Issuers create merkle trees with  user certificates as leaf;
- Issuer publish roots and send membership paths;
- Users prove membership in a recent tree;

>[!WARNING]
>I don't think this satisfies the decentralization that EUDI wallet wanted to achieve

Simple to set up proof when hash function is proof friendly.
Short-lived validity is preferable in practice.

Certificates must be bounded to user devices (for example the secure element) by adding pk of secure element.
In presentations need to prove device possession.
Nide to hide PoP public key with proof that public key is among some other public keys.

Friendly dillithium PoP:
Verification: H(Az - ct.pk,msg) = c ?
- PoP message is a public random challenge;
- User can output w = Az - ct;

Simplify: 
- Don't hash pk in random oracle;
- Same A for all public key;

Easy proof:
- Publicly evaluate c = H(w,msg);
- User proves knowledge of z: Az = w + ct

Fixed A means it doesn't need to be part of the witness and no proof of correct expansion.

THis I think they are called: Lattice proof systems.

## Lattice proof construction
Lattice:
$R_q = Z_q[x]/(x^d + 1)$
Allows for high performance NTT based multiplier implementations.

Anatomy of lattice proof:
Main operations:
- Decompose;
- Commit;
- Project;
- ...

Overview of current concrete lattice pro  
- LNP: Linear proof size, small proof of small statement, zero knowledge;
- LaBRADOR: Polylog proof size, smallest quantum safe proof for big statements;
- RoKoko: Polylog verifier complexity.

The difference between poly scheme and a snark is that the snark prove a big statement, the poly scheme brakes down the proof into smaller benches.

 