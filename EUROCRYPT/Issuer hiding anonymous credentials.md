User asks Verifier for policy (trusted issuer list)
The user computes an or proof over the policy members.

Existing constructions:
Type 0,1 and 2. We will see I hope.

Anonimity set should keep always the same in order not to make the user leak informations.

Randomizing BBS public key ipk and adapt a signature signa over $a$.
### Type 0 construction
Without policy public key.
Let's say the verifier trust 1,4,8, and the user has sign from 4.
We use the BBS key rerandomization process.

### Type 1
With universal policy keys.
Public key randomization facilitate Bobolz et al. framework.
Manager sing issuer keys 1,4,8 and prover compute  NIZKP that {cred,ipk,sigma_i|Vf(a,cred,ipk) = 1}*