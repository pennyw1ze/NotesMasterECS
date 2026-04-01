### The idea behind the zero-knowledge proof
$\exists (x_1, r_1, r_1^*, \hat{r_1^*})=w :\gamma = \delta_3(x_1, r_1, \beta) \wedge c_1 = com(x_1, r_1^*) \wedge c2 = com(r_1, \hat{r_1^*})$ 

Each time a player sends a message in a honest-but-curious protocol, he additionally a zero-knowledge proof of knowledge, with _all other players_, to prove that "I know an input x and a random tape r such that":
1. Commitment sent in the first flow are valid commitments to x and r
2. The message i just sent is equivalent to NextMessage(x, r, transcript)

By doing this we can transform any semi-honest protocol to make it resistant w.r.t. fully malicious players. This is because any deviations from the protocol can be caught.

There is still an issue: *we don't have a way to prove that the randomness that a player uses is actually random*, since there isn't even computation involved -idea, use pseudo-random number generator with time as seed, but it's insecure-. The issue is too much freedom for the user to choose randomness.
#### Coin-Tossing randomness
We use the **coin-tossing** functionality, without letting the other party know the bit. 
$P1 \gets comm(r_1) \gets P2$
$P1 \to r_2 \to P2$
$r = r_1 \oplus r_2$ 
Now we can use this scheme to have an NP algorithm that can be sure that r has been computed following the protocol. If an adversary would've changed $r_1$ a posteriori, then we would know that the commitment would fail. 
ElGamal commitment scheme $c = g^{r^*} h^{r_2}$ where $r_2$ is the commitment.