# Functionalities
The functionalities of a Wallet can be grouped into the following categories:

- **Secure identification and authentication**, ensuring that Users can present person identification data in a trusted environment.
- **Exchanging qualified and non-qualified User attributes** through secure and verifiable electronic attestations of attributes.
- **Electronic signing of documents or data**, allowing Users to create legally recognised qualified electronic signatures and seals.
- **Generate and use pseudonyms** for authentication, to enhance privacy and prevent tracking.

# Actors
The audience of the ARF consists of:

- Entities acting as PID Providers, QEAA Providers, PuB-EAA Providers, or EAA Providers;
- Wallet Providers;
- Relying Parties;
- Conformity Assessment Bodies (CABs);
- Supervisory Bodies;

![[Pasted image 20260224155454.png]]
*Note that a single entity may combine multiple of the roles depicted in the figure, as long as that entity complies with all requirements, both legal and technical, for each of the roles. In addition, potential conflicts of interest are to be avoided, but this issue is outside the scope of this ARF.

|Role|Primary Responsibility|Section|
|---|---|---|
|**User of Wallet Unit**|Manage, store, and present PIDs/attestations.|[Section 3.2](https://eudi.dev/latest/architecture-and-reference-framework-main/#32-users-of-wallet-units)|
|**Wallet Provider**|Make the certified Wallet Solution available to Users.|[Section 3.3](https://eudi.dev/latest/architecture-and-reference-framework-main/#33-wallet-providers)|
|**PID Provider**|Issue Person Identification Data (PID) to Users.|[Section 3.4](https://eudi.dev/latest/architecture-and-reference-framework-main/#34-person-identification-data-pid-providers)|
|**Trusted List or LoTE Provider**|Maintain, manage, and publish Trusted Lists and/or Lists of Trusted Entities.|[Section 3.5](https://eudi.dev/latest/architecture-and-reference-framework-main/#35-trusted-list-or-lote-provider)|
|**QEAA Provider**|Issue Qualified Electronic Attestations of Attributes (QEAAs).|[Section 3.6](https://eudi.dev/latest/architecture-and-reference-framework-main/#36-qualified-electronic-attestation-of-attributes-qeaa-providers)|
|**PuB-EAA Provider**|Issue EAAs on behalf of a public sector body.|[Section 3.7](https://eudi.dev/latest/architecture-and-reference-framework-main/#37-eaa-issued-by-or-on-behalf-of-a-public-sector-body-responsible-for-an-authentic-source-pub-eaa-providers)|
|**EAA Provider**|Issue Non-Qualified Electronic Attestations of Attributes (EAAs).|[Section 3.8](https://eudi.dev/latest/architecture-and-reference-framework-main/#38-non-qualified-electronic-attestation-of-attributes-eaa-providers)|
|**Qualified Electronic Signature Remote Creation (QESRC) Provider**|Provide Qualified Electronic Signature Remote Creation services.|[Section 3.9](https://eudi.dev/latest/architecture-and-reference-framework-main/#39-qualified-electronic-signature-remote-creation-qesrc-providers)|
|**Authentic Source**|Act as the definitive repository for specific attributes.|[Section 3.10](https://eudi.dev/latest/architecture-and-reference-framework-main/#310-authentic-sources)|
|**Relying Party (RP) / Intermediary**|Request and receive attributes from a Wallet Unit.|[Section 3.11](https://eudi.dev/latest/architecture-and-reference-framework-main/#311-relying-parties-relying-party-instances-and-intermediaries)|
|**Conformity Assessment Body (CAB)**|Certify Wallet Solutions and audit Trust Service Providers.|[Section 3.12](https://eudi.dev/latest/architecture-and-reference-framework-main/#312-conformity-assessment-bodies-cab)|
|**Supervisory Body**|Review the proper functioning of ecosystem actors.|[Section 3.13](https://eudi.dev/latest/architecture-and-reference-framework-main/#313-supervisory-bodies)|
|**Device Manufacturers / Subsystems**|Provide the underlying platform (hardware, OS, secure elements).|[Section 3.14](https://eudi.dev/latest/architecture-and-reference-framework-main/#314-device-manufacturers-and-related-subsystems-providers)|
|**Attestation Scheme Provider**|Define and publish the Attestation Rulebooks and schemes.|[Section 3.15](https://eudi.dev/latest/architecture-and-reference-framework-main/#315-attestation-scheme-providers-for-qeaas-pub-eaas-and-eaas)|
|**National Accreditation Body (NAB)**|Accredit CABs according to EU regulations.|[Section 3.16](https://eudi.dev/latest/architecture-and-reference-framework-main/#316-national-accreditation-bodies)|
|**Registrar**|Manages the registration of Providers and Relying Parties.|[Section 3.17](https://eudi.dev/latest/architecture-and-reference-framework-main/#317-registrars)|
|**Access Certificate Authority (Access CA)**|Issue access certificates for authentication.|[Section 3.18](https://eudi.dev/latest/architecture-and-reference-framework-main/#318-access-certificate-authorities)|
|**Provider of Registration Certificates**|Issue certificates detailing registration status and scope.|[Section 3.19](https://eudi.dev/latest/architecture-and-reference-framework-main/#319-providers-of-registration-certificates)|

---
# Reference architecture overview
The figure below gives an overview of the architecture of the EUDI Wallet ecosystem and its components.

![[Figure_2_High-Level_Architecture.png]]




---
# [7.4.3.5.3 Zero-knowledge proofs](https://eudi.dev/latest/architecture-and-reference-framework-main/#74353-zero-knowledge-proofs)
*NOTE: Discussions on Zero-Knowledge Proofs (ZKPs) are ongoing. No specific ZKP has been selected to be supported by components in the EUDI Wallet ecosystem.

Unlike Relying Party linkability, Attestation Provider linkability cannot be fully eliminated when using attestation formats based on salted hashes. The only viable mitigation is to adopt Zero-Knowledge Proofs (ZKPs) as a verification mechanism instead of relying on salted-attribute hashes. A Zero-Knowledge Proof (ZKP) is a cryptographic protocol that allows one party (the prover) to convince another party (the verifier) that a given statement is true without revealing any additional information beyond the validity of the statement itself. This ensures that the verifier gains no knowledge about how the prover knows the statement to be true, thus preserving privacy while enabling trust.

However, the integration of ZKPs in the EUDI Wallet ecosystem is still under discussion and development, due to the complexity of implementing ZKP solutions in secure hardware and the lack of support in currently available secure hardware (WSCDs). As with Relying Party linkability, organisational and enforcement measures can help deter Attestation Providers from colluding and tracking Users. Additionally, many Attestation Providers are subject to regular audits, making it easier to detect collusion and tracking compared to Relying Parties.

Zero-Knowledge Proof (ZKP) mechanisms for verifying personal information are highly promising and essential for ensuring privacy in various use cases. They enable Users to prove statements such as "I am over 18" without disclosing any personal data, offering a robust solution for privacy-preserving authentication and verification.

One area of development is age verification, where the European Commission is actively exploring and testing ZKP-based solutions. The outcomes of this initiative could pave the way for the adoption of ZKPs within the EUDI Wallet ecosystem, further strengthening privacy protections in future implementations.

The Discussion Paper for Topic G (Zero-Knowledge Proofs) presents the (desired) privacy properties of Zero-Knowledge Proof schemes. It introduces the main families of Zero-Knowledge Proof schemes and gives an overview of representative solutions. Finally, it discusses topics related to the integration of Zero-Knowledge Proof schemes into the EUDI Wallet ecosystem.

High-level requirements for Zero-Knowledge Proofs to be used in the EUDI Wallet ecosystem are included in Topic 53 of Annex 2.