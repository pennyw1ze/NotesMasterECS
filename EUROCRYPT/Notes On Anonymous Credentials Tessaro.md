_Approaches_
- Generic ZK (slow);
- Tailor made digital signatures with efficient ZK (BBS);

**CONS**
- BBS not post quantum secure, but there protocols PQS made on top of it. (gotta check them out);
- Pairing based;
- DIfficult device binding.
80byte signature size.

CLAIM
I know a valid signature for Verification Key of the issuer on a list of attributes such that $a_i = a*$.
We can randomize the input set.

Papers for other BBS anonymous credentials implementations:
Keyed-verification anonymous credentials
issuer = verifier.
Server-aided anonymous credentials
Public-verifiability with help.