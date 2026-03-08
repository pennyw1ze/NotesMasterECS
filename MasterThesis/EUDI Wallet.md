# Functionalities
The functionalities of a Wallet can be grouped into the following categories:

- **Secure identification and authentication**, ensuring that Users can present person identification data in a trusted environment.
- **Exchanging qualified and non-qualified User attributes** through secure and verifiable electronic attestations of attributes.
- **Electronic signing of documents or data**, allowing Users to create legally recognised qualified electronic signatures and seals.
- **Generate and use pseudonyms** for authentication, to enhance privacy and prevent tracking.

# Actors

![[Pasted image 20260224155454.png]]
*Note that a single entity may combine multiple of the roles depicted in the figure, as long as that entity complies with all requirements, both legal and technical, for each of the roles. In addition, potential conflicts of interest are to be avoided, but this issue is outside the scope of this ARF.

| Role                                                                | Primary Responsibility                                                        | Section                                                                                                                                                                                       |
| ------------------------------------------------------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **User of Wallet Unit**                                             | Manage, store, and present PIDs/attestations.                                 | [Section 3.2](https://eudi.dev/latest/architecture-and-reference-framework-main/#32-users-of-wallet-units)                                                                                    |
| **Wallet Provider**                                                 | Make the certified Wallet Solution available to Users.                        | [Section 3.3](https://eudi.dev/latest/architecture-and-reference-framework-main/#33-wallet-providers)                                                                                         |
| **PID Provider**                                                    | Issue Person Identification Data (PID) to Users.                              | [Section 3.4](https://eudi.dev/latest/architecture-and-reference-framework-main/#34-person-identification-data-pid-providers)                                                                 |
| **Trusted List or LoTE Provider**                                   | Maintain, manage, and publish Trusted Lists and/or Lists of Trusted Entities. | [Section 3.5](https://eudi.dev/latest/architecture-and-reference-framework-main/#35-trusted-list-or-lote-provider)                                                                            |
| **QEAA Provider**                                                   | Issue Qualified Electronic Attestations of Attributes (QEAAs).                | [Section 3.6](https://eudi.dev/latest/architecture-and-reference-framework-main/#36-qualified-electronic-attestation-of-attributes-qeaa-providers)                                            |
| **PuB-EAA Provider**                                                | Issue EAAs on behalf of a public sector body.                                 | [Section 3.7](https://eudi.dev/latest/architecture-and-reference-framework-main/#37-eaa-issued-by-or-on-behalf-of-a-public-sector-body-responsible-for-an-authentic-source-pub-eaa-providers) |
| **EAA Provider**                                                    | Issue Non-Qualified Electronic Attestations of Attributes (EAAs).             | [Section 3.8](https://eudi.dev/latest/architecture-and-reference-framework-main/#38-non-qualified-electronic-attestation-of-attributes-eaa-providers)                                         |
| **Qualified Electronic Signature Remote Creation (QESRC) Provider** | Provide Qualified Electronic Signature Remote Creation services.              | [Section 3.9](https://eudi.dev/latest/architecture-and-reference-framework-main/#39-qualified-electronic-signature-remote-creation-qesrc-providers)                                           |
| **Authentic Source**                                                | Act as the definitive repository for specific attributes.                     | [Section 3.10](https://eudi.dev/latest/architecture-and-reference-framework-main/#310-authentic-sources)                                                                                      |
| **Relying Party (RP) / Intermediary**                               | Request and receive attributes from a Wallet Unit.                            | [Section 3.11](https://eudi.dev/latest/architecture-and-reference-framework-main/#311-relying-parties-relying-party-instances-and-intermediaries)                                             |
| **Conformity Assessment Body (CAB)**                                | Certify Wallet Solutions and audit Trust Service Providers.                   | [Section 3.12](https://eudi.dev/latest/architecture-and-reference-framework-main/#312-conformity-assessment-bodies-cab)                                                                       |
| **Supervisory Body**                                                | Review the proper functioning of ecosystem actors.                            | [Section 3.13](https://eudi.dev/latest/architecture-and-reference-framework-main/#313-supervisory-bodies)                                                                                     |
| **Device Manufacturers / Subsystems**                               | Provide the underlying platform (hardware, OS, secure elements).              | [Section 3.14](https://eudi.dev/latest/architecture-and-reference-framework-main/#314-device-manufacturers-and-related-subsystems-providers)                                                  |
| **Attestation Scheme Provider**                                     | Define and publish the Attestation Rulebooks and schemes.                     | [Section 3.15](https://eudi.dev/latest/architecture-and-reference-framework-main/#315-attestation-scheme-providers-for-qeaas-pub-eaas-and-eaas)                                               |
| **National Accreditation Body (NAB)**                               | Accredit CABs according to EU regulations.                                    | [Section 3.16](https://eudi.dev/latest/architecture-and-reference-framework-main/#316-national-accreditation-bodies)                                                                          |
| **Registrar**                                                       | Manages the registration of Providers and Relying Parties.                    | [Section 3.17](https://eudi.dev/latest/architecture-and-reference-framework-main/#317-registrars)                                                                                             |
| **Access Certificate Authority (Access CA)**                        | Issue access certificates for authentication.                                 | [Section 3.18](https://eudi.dev/latest/architecture-and-reference-framework-main/#318-access-certificate-authorities)                                                                         |
| **Provider of Registration Certificates**                           | Issue certificates detailing registration status and scope.                   | [Section 3.19](https://eudi.dev/latest/architecture-and-reference-framework-main/#319-providers-of-registration-certificates)                                                                 |

---
# Reference architecture overview
The figure below gives an overview of the architecture of the EUDI Wallet ecosystem and its components.

![[Figure_2_High-Level_Architecture.png]]

---
# **EUDI analysis**
I will analyze the EUDI system in the following way:
FUNCTIONALITY:
- Description;
- Actors;
- Cryptographic primitives adopted;

## PKI and signature schemes
First a little clarification:
The entire ecosystem is based on **Public Key Infrastructure (PKI)**
- **Elliptic Curve Cryptography (ECC):** is the best choice for mobile device performance. ISO/IEC 18013-5 and SD-JWT-VS uses **COSE** (CBOR Object Signing and Encryption) and **JOSE** (JSON Object Signing and Encryption) frameworks;
- **RSA**: Used for local device binding due to the dimension of keys is supported by **X.509** (RFC5280) certificates and ETSI standards for QES signatures. 

Signature format for documents and transactions:
Sources cites explicitly ETSI profile that should be applied to files:
- **XAdES**;
- **PAdES**;
- **CAdES**;
- **JAdES**;

---
## 1) Identification and authentication
**Description**:
Users are able to **Identify and authenticate** to online and offline services, while using **selective disclosure** of attributes or attestations.

**Actors**:
- User;
- Wallet unit: smartphone application;
- Relying party: service provider that asks for data;
- PID provider: 

**Protocol(s)**:
ISO/IEC 18013-5 overview:
1. Set up a communication channel using QR code or NFC, and to subsequently communicate over BLE, NFC, or Wi-Fi Aware;
2. Security mechanisms ensuring
	- Confidentiality and authenticity of all data exchanged between a Wallet Unit and a Relying Party,
	- Relying Party authentication;
Paper about this protocol [here](https://cdn.standards.iteh.ai/samples/69084/197168b6d9e84880ae8cd3b344f8bf0e/ISO-IEC-18013-5-2021.pdf)

*Protocol scheme intuition:*
![[Figure_3_Proximity_Flow.png]]


OpenID4VP standard defines message structures, transaction flows, and an HTTP-based interface specification for attestation presentations by Wallet Units to Relying Parties. 
1. OpenID4VP also specifies security mechanisms ensuring:
	- Confidentiality and authenticity of all data exchanged between a Wallet Unit and a Relying Party,
	- Relying Party authentication;
Paper about this protocol [here](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html)

*Protocol scheme intuition:*
![[Figure_5_Remote_Cross-Device_Flow.png]]

In both protocol, **device binding** is achieved by including a cryptographic public key in the PID or attestation and signing it. The corresponding private key is protected by a WSCA/WSCD or keystore in the Wallet Unit.


The protocol specified in ISO/IEC 18013-5 is used for proximity attestation presentation flows, while the protocol specified in OpenID4VP is used for remote attestation presentation flows. Although these protocols differ in the details, on a high level, they both implement **Relying Party authentication** as shown in Figure below.
![[Figure_13_Relying_Party_Authentication.png]]

---
## 2) Exchanging user attributes
**Description**:
Allows user to require, store and share EAA, PubEAA, QEAA, Electronic Attestation of Attributes. Wallet support selective disclosure, allowing user to share only selected attributes among the whole attestation's attributes.

**Actors**:
- User;
- Wallet Unit;
- Relying party;
- Attestation provider (QEAA, PuBEAA, EAA), entity transmitting attestations;

**Protocol**:
SD-JWT VC stands for Selectively Disclosable JSON Web Token Verifiable Credentials and are a special form of JWTs [RFC 7519] that are selectively disclosable.

SD-JWT VC specifies the following aspects:
- The encoding to be used for attributes and metadata, namely JSON, as well as rules to prevent collisions of claim names;
- A proof mechanisms ensuring the authenticity and integrity of a PID or attestation, while allowing selective disclosure of attributes;
- A security mechanism enabling device binding of PIDs and attestations, see [Section 6.6.3.8](https://eudi.dev/latest/architecture-and-reference-framework-main/#6638-relying-party-instance-verifies-device-binding). This mechanism is optional in SD-JWT VC;

Zero knowledge proof are still on their way to be implemented (see on the bottom of this paper).
Actual github implementation [here](https://github.com/vcstuff/sd-jwt-vc-types).

*Visual representation of the protocol:*
![[SD-JWT-VC.png]]

---
## 3) Qualified Electronic Signature (QES
**Description**:
Allows users to create legally recognised qualified electronic signatures and seals for legally binding transactions and document signing.

**Actors**:
- User;
- Wallet unit;
- **local QSCD**, local tamper resistant hardware device that provides signatures \ **QESRC** (*Provide Qualified Electronic Signature Remote Creation services*) provider;

**Protocol**:
The comunication between the local wallet unit and the QSCD/WSCD occurs via **Secure Cryptographic Interface**
The EUDI Wallet ecosystem that use cryptographic keys, including Wallet Providers, PID Providers and Attestation Providers, Trusted List or LoTE Providers, Providers of registration certificates, and Access Certificate Authorities. Such parties will typically use a certified **Hardware Security Module** (HSM) for managing private and secret keys.
So basically all the security is based on **HARDWARE** components, which handles key generation and managing, performs user authentication, comunicate to the wallet via hardware secure interface and provide signatures and seals.
The use of any cryptographic protocol for key generation here is delegated to the wallet implementor.

---
## 4) Pseudonyms
**Description**:
Users are able to present a pseudonym to a Relying Party (RP) to authenticate in situations where it is not necessary for the Relying Party to learn the User's identity. This helps user evading tracing from service providers.

**Actors**:
- User;
- Wallet unit;
- Relying parties;

**Protocol**:
We have 3 different type of pseudonyms:
- Verifiable pseudo (allows user to prove ownership);
- Attested pseudo (subtype of verifiable ones, allowing Relying Parties to verify that a third party has attested that a pseudonym is owned by a User);
- Scope rate-limited pseudo (subtype of a verifiable pseudo guaranteeing that the User is limited to control only a certain number of pseudonyms (called the rate) for a given scope);

**W3C WebAuthn** defines the technical specification for a type of verifiable pseudonyms called Passkeys. Protocol is divided into 2 phase:

1. **Registration:**
	1. The User generates a public-private key pair and stores both the public and the private key at their secure device (referred to as an Authenticator);
	2. The User registers the public key at the desired Relying Party service;

2. **Authentication:**
	1. When the User wishes to authenticate towards a service, the service will send them a challenge consisting of a random value;
	2. The User uses the private key stored on their Authenticator to sign the challenge and sends this back to the service;
	3. The service verifies that the signature on the challenge can be verified using the registered public key. If the signature verifies and the origin matches the expected origin, the User is considered authenticated and thereby granted access to the service;

*Pseudonyms are stored encrypted in a local safe area on the device;*
Actual implementation [here](https://github.com/w3c/webauthn/wiki/Explainer:-WebAuthn-immediate-mediation).

*Visual representation of the authentication scheme*
![[W3CWebAuthn.png]]

---
# Trust model


![[Figure_12_Trust_Model.png]]
*Comprehensive schema of the trust model in the application*

---
# [7.4.3.5.3 Zero-knowledge proofs](https://eudi.dev/latest/architecture-and-reference-framework-main/#74353-zero-knowledge-proofs)
*NOTE: Discussions on Zero-Knowledge Proofs (ZKPs) are ongoing. No specific ZKP has been selected to be supported by components in the EUDI Wallet ecosystem.

Unlike Relying Party linkability, **Attestation Provider linkability** **cannot be fully eliminated** when using attestation formats based on **salted hashes**. The only viable mitigation is to adopt Zero-Knowledge Proofs (ZKPs) as a verification mechanism instead of relying on salted-attribute hashes. A Zero-Knowledge Proof (ZKP) is a cryptographic protocol that allows one party (the prover) to convince another party (the verifier) that a given statement is true without revealing any additional information beyond the validity of the statement itself. This ensures that the verifier gains no knowledge about how the prover knows the statement to be true, thus preserving privacy while enabling trust.

However, the integration of ZKPs in the EUDI Wallet ecosystem is still under discussion and development, due to the complexity of implementing ZKP solutions in secure hardware and the lack of support in currently available secure hardware (WSCDs). As with Relying Party linkability, organisational and enforcement measures can help deter Attestation Providers from colluding and tracking Users. Additionally, many Attestation Providers are subject to regular audits, making it easier to detect collusion and tracking compared to Relying Parties.

Zero-Knowledge Proof (ZKP) mechanisms for verifying personal information are highly promising and essential for ensuring privacy in various use cases. They enable Users to prove statements such as "I am over 18" without disclosing any personal data, offering a robust solution for privacy-preserving authentication and verification.

One area of development is age verification, where the European Commission is actively exploring and testing ZKP-based solutions. The outcomes of this initiative could pave the way for the adoption of ZKPs within the EUDI Wallet ecosystem, further strengthening privacy protections in future implementations.

The Discussion Paper for Topic G (Zero-Knowledge Proofs) presents the (desired) privacy properties of Zero-Knowledge Proof schemes. It introduces the main families of Zero-Knowledge Proof schemes and gives an overview of representative solutions. Finally, it discusses topics related to the integration of Zero-Knowledge Proof schemes into the EUDI Wallet ecosystem.

High-level requirements for Zero-Knowledge Proofs to be used in the EUDI Wallet ecosystem are included in Topic 53 of Annex 2.