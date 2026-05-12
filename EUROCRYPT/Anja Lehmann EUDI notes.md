Linked by the signature of the issuer which is unique and user public key.
Issuer can send bench (a lot of) salted hash one-use credentials. This does not remove unlinkability between issuer and relying party.

ZKP run entirely on public values. ZKP not need in secure hardware.

How to instatiate both signatures and ZKP ?
First of all, they will have to implement a standardized Zero Knowledge proof.

| Issuer | Device                                     | ZKP                                          | Efficiency   | Notes                                                    |
| ------ | ------------------------------------------ | -------------------------------------------- | ------------ | -------------------------------------------------------- |
| ECDSA  | ECDSA                                      | Cricuit Based (Very complex standardization) | 400ms, 300kb | Legacy compliant but complex (Longfellow implementation) |
| BBS    | Schnorr                                    | Schnorr type                                 | 10ms, 1kB    | Dedicated SE API                                         |
| BBS    | BLS or Schnorr (Not available on hardware) | Schnorr type                                 | 10ms, 1kB    | Simple SE API, Better privacy (Cloud HSM)                |

ZKP of proove of possession over device public key:
 MEssage (nonce is public) so we do not have to prove anything on hashes in zero knowledge;
 We can reveal part of the proof of possession (is fresh and used once);
 Must hide dpk
 Statement is fix (?)

ZKP of credentials:
Must hide message (avoid hashing of attributes)
Must hide credentials (multi-show unlinkability)
Typically reveals ipk issuer pub key
Should support many statements and be easy to extend

| Issuer | Device | ZKP         | Efficiency   | Notes                  |
| ------ | ------ | ----------- | ------------ | ---------------------- |
| BBS    | ECDSA  | Shnorr type | 400ms, 175kb | a lot of simple proofs |

Actually device binding proof of possesion can be computed and presented separately from the proof on credentials.

For post quantum transition, modular framework vision is going to replace singular component with post-quantum one.

POST-QUANTUM vs POST PRIVACY ?
Main priority: solutions should have a  post-quantum privay.
for post-quantum soundness, we can wait.

![[Pasted image 20260510145623.png]]

She is very concerned on deployed a wallet based on Legacy technologies.

Google and Apple dependencies A LOT Inside the wallet implementation!!!

