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

---
## Securing 2PC against malicious player final part
how to prevent players from using their inputs to leak informations on other player's input ?

Evaluator:
We do not perform an OT for every wire and every circuit. Instead, we will perform an OT using the evalutaro input just once for each wire in a single circuit, and then recycle that bit to cover all other wires with the same label for other circuits.

Garbler:
Instead of garbling circuit for funciton f(x,y) he will compute output for function f(x,y) || H(y).
- Evaluator checks H(y) is same for majority;
- H should be collision resistant;
- H should hide y;
Replacing H with random oracle model will add some kind of syntactycal problem because we should garble the circuit of the random oracle, but it has no circuit ! SHA256 comes after defining the security of the protocol.
We cannot claim security of random oracle and then demonstrate with the circuit of SHA256, it is not secure!

Damage:
- I am not satisfied with the evaluator that knows the garbler is cheating but cannot reveal the info and just take majority;

---
## Implementing SHA256 inside MP-SPDZ
What is an oblivious PRF ?
A PRF that can be computed in 2 party computation and can hide the inputs to the respective parties.
PRF is a tool that with a short key simulates an array of random strings
PRF(k,x). SHA256 is a PRF in the random oracle model.
I downloaded sha256 circuit. It takes 2 inputs and output 256 bits.
Inputs are 512 bits and 256 bits. 512 is the key.
SHA256 process inputs by block, keeping his internal state of 256 bits.
To implement sha, the second 256 bits must be provided by us and are fixed. Are written on wikipedia.

```
_Initialize hash values:_
(first 32 bits of the _fractional parts_ of the square roots of the first 8 primes 2..19):
h0 := 0x6a09e667
h1 := 0xbb67ae85
h2 := 0x3c6ef372
h3 := 0xa54ff53a
h4 := 0x510e527f
h5 := 0x9b05688c
h6 := 0x1f83d9ab
h7 := 0x5be0cd19
```

Also we have to add padding in order to round our input in the first 512 bits. The padding bits are:

```
_Pre-processing (Padding):_
begin with the original message of length L bits
append a single '1' bit
append K '0' bits, where K is the minimum number >= 0 such that (L + 1 + K + 64) is a multiple of 512
append L as a 64-bit big-endian integer, making the total post-processed length a multiple of 512 bits
such that the bits in the message are: <original message of length L> 1 <K zeros> <L as 64 bit integer> , (the number of bits will be a multiple of 512)
```

For the padding, we must have a 1 followed by 0s reaching 448 and the last block are the 64 bits which are supposed to encode the length of my original message.

---
Why we use Fieldman VSS ? Because we are not involving the Oblivious Transfer primitive.
To protect ao as exponent of g ($A_0 = g^{a_0}$), let's say our secret is s:
We compute a random $a_0$, $c = H(a_0)\oplus s$, and send c to everyone.

---
## Fully Homomorphic encryption
El gamal:
Cipher text: ($u = g^r$,$v = mPK^r$)
Decryption: (plain = ${v \over u^x}$);
I can re-randomize:
u times $g^{r'}$ we add randomness r' to r.
It is homomorphic with respect to multiplication property
we can compute encryptiion of multiplication of $m_1$ and $m_2$ by just multiplying the cypertext.
Additive elgamal allows for m1 + m2 but you will end up to bruteforce the space of the message.
You do v = $g^{m_1}g^r$
and the when you multiply and decrypt you obtain 
$g^{m_1 + m_2}$ and then you will have to bruteforce.

