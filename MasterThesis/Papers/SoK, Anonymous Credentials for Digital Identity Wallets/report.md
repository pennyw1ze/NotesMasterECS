PAPER PURPOSE:
Their work aims to provide this informational foundation and gives an overview of the two most prominent directions to build identity system from anonymous credentials: dedicated multi-message signatures with efficient proofs (such as BBS signatures with Schnorr proofs), and general-purpose zero-knowledge (ZKP) systems build on top of ECDSA.

What features should anonymous credentials have for the authors ? here they are:
#### Features of anonymous credentials (AC):
1. **Selective disclosure**;
2. **Multi-show unlinkability**: Relying party to relying party unlinkability ensures that different presentations of the same user, that don’t reveal any identifying information, cannot be linked;
3. **Untraceability**: prevents that a malicious issuer can link the credentials it issued;
4. **Single Key Binding**: Many or even all credentials that belong to the same user can be bound to the same user private key to reduce overall complexity;
5. **Presentation freshness**: Presentation of a credential never reveals the original credential, but only a proof thereof.  From such a presentation, no further presentation for a different context can be derived;
6. **Certified Pseudonyms**: allow to derive verifiable yet unlinkable pseudonyms from a user’s secret key;

MORE ADVANCED FEATURES:
- **Predicate proofs**:  Instead of revealing an exact attribute, proves that a property holds true (ex. age over 18 without revealing birth date);
- **Composite proofs**: Proof built upon several credentials and more advanced logics;
- **Deniable presentations**:  If the RP forwards the proof to a third party, this does not provide convincing cryptographic evidence of the validity of the user’s attributes;
- **(Partially-)Blind issuance**: Allows for the issuance of data that stays hidden from the issuer (ex. local university makes central authority signs final degree without revealing student's name or grades);
- **Issuer hiding**: Creates a presentation that only reveals membership of a certain trust ecosystem instead of revealing a specific issuer;
- **Conditional disclosure**: The presentation contains verified but initially protected attributes, which can be discovered by a dedicated party only when needed;

### Anonymous credentials options
Generally anonymous credentials are implemented in the following way: 
- The user obtains a credential as an issuer's signature on a set of attributes;
- User proves identity or discolse attributes via ZKP;
Concrete protocols for such constructions can be grouped in 2 categories:
- **Multi-message signature schemes**: digital signature schemes designed to support selective disclosure and ZKPs;
- **General purpose ZKP**: allows proving statement over arbitrary computations. They can be built upon legacy systems and standard signatures. More complex and less efficient.

The following section of the paper describes the adoptions of 2 technologies for the implementation of AC: **BBS** for multi-message signatures and **zkSNARKs** for general purpose zero-knowledge proofs.

> [!WARNING]
> Both zkSNARKs and BBS signature schemes are **POST QUANTUM INSECURE!**

> [!ATTIENTION]
> It seems like they are actually presenting How to Prove Post-Quantum Security for Succinct Non-Interactive Reductions at EUROCRYPT Alessandro Chiesa.

