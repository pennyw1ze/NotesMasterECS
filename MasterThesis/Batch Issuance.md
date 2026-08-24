Batch issuance is a mechanism suggested by ARF to allow SD-JWT Verifiable Credential system, as well as other similar credential systems, to achieve verifier-unlinkability (different verifiers cannot understand weather a presentation has been made by the same user or by different users).
The system is really simple: instead of issuing a single credential which a user would later on have to show to different verifiers (at least the device public key must be shown in order to grand device binding), the issuer sends to the user a batch of one-time credentials, all stored in the same Secure Element on the smartphone.
Each time a user needs to authenticate, he sends one of the credential to a verifier and then mark the credential as used.
The user grant to the issuer that all the device public key sent to obtain the batch are in the same secure element by crafting an attestation inside the secure element. In this way a relying party (verifier) doesn't need to check if the device public key of each credential belong to the same user's Secure Element because the issuer has already done the job.
In order to show to the verifier 2 different attestations, a user needs to provide a proof that those 2 attestations belong to the same Secure Element: otherwise malicious users could perform a mix-and-match attack. This proof is called Cryptographic Binding.

Notes on cryptographic binding
- To link 2 different attestations, ARF suggest to insert in attestation common fields which are unique for each user such as PID id: in this way users can show to the verifier that the 2 credentials are bounded by just showing the 2 common field;
- To achieve device binding with respect to different credentials that does not share a common field, ARF suggest to generate an attestation in the Secure Element that binds the 2 credentials togheter.

Now in our opinion this mechanism brakes verifier-unlinkability.