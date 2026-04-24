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
SD-JWT VC stands for Selectively Disclosable JSON Web Token Verifiable Credentials and are a special form of JWTs that are selectively disclosable.

SD-JWT VC specifies the following aspects:
- The encoding to be used for attributes and metadata, namely JSON, as well as rules to prevent collisions of claim names;
- A proof mechanisms ensuring the authenticity and integrity of a PID or attestation, while allowing selective disclosure of attributes;
- A security mechanism enabling device binding of PIDs and attestations, see [Section 6.6.3.8](https://eudi.dev/latest/architecture-and-reference-framework-main/#6638-relying-party-instance-verifies-device-binding). This mechanism is optional in SD-JWT VC;

Zero knowledge proof are still on their way to be implemented (see on the bottom of this paper).
Actual github implementation [here](https://github.com/vcstuff/sd-jwt-vc-types).

*Visual representation of the protocol:*
![[SD-JWT-VC.png]]

---
## 3) Qualified Electronic Signature QES
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
## 5) Payments
The eudi wallet is acting as an authenticator to allow user to pay.
Unlike Google Wallet, which typically stores a tokenized version of a physical credit card, the **EUDI Wallet** functions primarily as a **secure authenticator**. Instead of storing a card number, the Wallet is issued a **Strong User Authentication (SUA) attestation**. This attestation is a verified digital document that links your Wallet Unit to your specific bank account or payment service. This SUA attestation is **device-bound**, meaning it is cryptographically tied to the secure hardware of your specific phone (the WSCD/WSCA). 

PROCEDURE:
When you make a payment, the merchant (Relying Party) sends a request to your Wallet containing **transactional data**
You review this on your screen, and once you give consent, the Wallet **signs that specific data** using its secure internal keys to authorize the payment.
This process is designed to meet **Strong Customer Authentication (SCA)** requirements under EU regulations (PSD2/PSD3), ensuring the payment is as secure as possible

**Summary of the Payment Lifecycle**

- **Phase 1 (Registration):** Link the Wallet to your bank/service via an SUA attestation.
- **Phase 2 (Authentication):** Approve specific transactions by signing the amount and payee data.
- **Phase 3 (Termination):** Unlink the Wallet from the service by deleting or revoking the SUA attestation.

---
# Trust model

![[Figure_12_Trust_Model.png]]
*Comprehensive schema of the trust model in the application*

<<<<<<< HEAD
=======
**Summarize of chapter 6: trust model:**

**1. Trust in Ecosystem Entities**

- **Wallet Providers:** They are notified to the Commission by a Member State and are responsible for all components of the Wallet Unit. Their trust anchors are published in a **Wallet Provider List of Trusted Entities (LoTE)**, allowing PID and Attestation Providers to verify that a Wallet Unit is genuine and secure.
- **PID and Attestation Providers:** These entities must register with a national Registrar. Upon registration, they receive **access certificates** to authenticate themselves to Wallet Units and are included in **Trusted Lists** (for qualified providers like QEAA) or **LoTEs**.
- **Relying Parties (RP):** Service providers must register to request attributes. They receive an access certificate for each of their technical setups, known as **Relying Party Instances**. Optional **registration certificates** may be issued to list the specific attributes they are authorized to request for a given service.

**2. Wallet Unit Lifecycle and Security**

- **Activation:** During activation, the Wallet Provider issues **Wallet Unit Attestations (WUA)** and **Wallet Instance Attestations (WIA)**. The WUA describes the security properties of the hardware (WSCD) and binds it to the user's cryptographic keys.
- **User Authentication:** The system requires two distinct authentication mechanisms. An **OS-level mechanism** (e.g., device PIN/biometrics) is used for general access, while an additional **WSCA/WSCD-level authentication** is mandatory for sensitive cryptographic operations like issuing or presenting a PID.
- **Maintenance and Revocation:** Wallet Providers monitor the security posture of Wallet Units (e.g., detecting jailbreaking). If a unit is compromised, or at the user's request, the provider revokes the unit by revoking its WUAs.

**3. Issuance and Presentation Trust**

- **Issuance:** This process requires mutual authentication. The Wallet verifies the provider's access certificate, while the provider validates the Wallet’s WUA and WIA to ensure it meets the required **Level of Assurance (LoA) High** for PIDs.
- **Presentation and Consent:** Before any data exchange, the Wallet must authenticate the Relying Party Instance. Crucially, the Wallet must obtain **explicit User approval** after informing the user of the RP's identity, the attributes requested, and their intended use.
- **Binding Mechanisms:**
    - **Device Binding:** Mandatory for PIDs, it cryptographically links the attestation to the secure hardware of the device to prevent cloning.
    - **User Binding:** Ensures the person presenting the data is the rightful holder, typically by trusting the Wallet's local authentication or through additional measures like visual/biometric comparison of a portrait.

**4. Specialized Trust Scenarios**

- **Wallet-to-Wallet (W2W):** Supports proximity interactions between natural persons (e.g., a private car rental). Because the Verifier in this scenario is a natural person and not a registered Relying Party, the system relies on physical proximity and specific "Wallet-to-Wallet modes" to manage trust.
- **Intermediaries:** These are entities acting on behalf of Relying Parties. They must be registered with the Registrar of the intermediated party and are legally required to **delete all obtained data immediately** after transmission to the final Relying Party.
>>>>>>> origin/main


---
# [7.4.3.5.3 Zero-knowledge proofs](https://eudi.dev/latest/architecture-and-reference-framework-main/#74353-zero-knowledge-proofs)
*NOTE: Discussions on Zero-Knowledge Proofs (ZKPs) are ongoing. No specific ZKP has been selected to be supported by components in the EUDI Wallet ecosystem.

Unlike Relying Party linkability, **Attestation Provider linkability** **cannot be fully eliminated** when using attestation formats based on **salted hashes**. The only viable mitigation is to adopt Zero-Knowledge Proofs (ZKPs) as a verification mechanism instead of relying on salted-attribute hashes. A Zero-Knowledge Proof (ZKP) is a cryptographic protocol that allows one party (the prover) to convince another party (the verifier) that a given statement is true without revealing any additional information beyond the validity of the statement itself. This ensures that the verifier gains no knowledge about how the prover knows the statement to be true, thus preserving privacy while enabling trust.

However, the integration of ZKPs in the EUDI Wallet ecosystem is still under discussion and development, due to the complexity of implementing ZKP solutions in secure hardware and the lack of support in currently available secure hardware (WSCDs). As with Relying Party linkability, organisational and enforcement measures can help deter Attestation Providers from colluding and tracking Users. Additionally, many Attestation Providers are subject to regular audits, making it easier to detect collusion and tracking compared to Relying Parties.

Zero-Knowledge Proof (ZKP) mechanisms for verifying personal information are highly promising and essential for ensuring privacy in various use cases. They enable Users to prove statements such as "I am over 18" without disclosing any personal data, offering a robust solution for privacy-preserving authentication and verification.

One area of development is age verification, where the European Commission is actively exploring and testing ZKP-based solutions. The outcomes of this initiative could pave the way for the adoption of ZKPs within the EUDI Wallet ecosystem, further strengthening privacy protections in future implementations.

The Discussion Paper for Topic G (Zero-Knowledge Proofs) presents the (desired) privacy properties of Zero-Knowledge Proof schemes. It introduces the main families of Zero-Knowledge Proof schemes and gives an overview of representative solutions. Finally, it discusses topics related to the integration of Zero-Knowledge Proof schemes into the EUDI Wallet ecosystem.

High-level requirements for Zero-Knowledge Proofs to be used in the EUDI Wallet ecosystem are included in Topic 53 of Annex 2.


---
# APPROFONDIRE
### Trovare livelli di sicurezza desiderati nell'attestation exchange

~The concept of Level of Assurance is defined for electronic identification means (and the corresponding eID scheme). For other attestations held or managed in the Wallet Unit, assurance and trust are expressed through other mechanisms (e.g. the security controls required for issuance and lifecycle management) and the regulation does not prescribe any specific level to be reached.

To reflect this, the ARF uses the term level of security for non-PID attestations, to indicate that different attestations may be subject to different security requirements. Each Attestation Provider specifies the required level of security for its attestations (including, where applicable, requirements on issuance, storage, presentation, and lifecycle management).~*from eudi.dev documentation [here](https://eudi.dev/latest/architecture-and-reference-framework-main/#22-identification-and-authentication)*

---
### Capire come sono legate le attestation ed il PID;

Attestation e PID non sono legate tra loro ma sono legate al wallet unit attraverso device binding.

**Device binding** is the cryptographic link that ensures a PID or attestation belongs exclusively to the **Wallet Secure Cryptographic Device (WSCD)** in the User's Wallet Unit. This key property **prevents copying or cloning**, significantly boosting the attestation's security. 
Device binding is also a prerequisite for **User binding**, allowing the Relying Party to trust the Wallet Unit's internal User authentication mechanisms to verify the presenter is the rightful User.

Within the EUDI Wallet ecosystem, implementing device binding is mandatory for PIDs, since PIDs must be managed at Level of Assurance High, which is **impossible** without device binding to a WSCA/WSCD. It is also mandatory for attestations complying with ISO/IEC 18013-5, due to the fact that this is required in that standard. For SD-JWT VC-compliant attestations, implementing device binding is recommended but not mandatory. However, note that OpenID4VP enables the Relying Party to indicate if it wants to receive a proof of device binding. ([Section 6.6.3.8](https://eudi.dev/latest/architecture-and-reference-framework-main/#6638-relying-party-instance-verifies-device-binding))

| Feature          | **Device Binding**                              | **User Binding**                                           |
| ---------------- | ----------------------------------------------- | ---------------------------------------------------------- |
| **Binds to**     | The secure hardware/app instance.               | The physical person (natural person).                      |
| **Primary Goal** | Prevent cloning and theft of the data itself.   | Prevent unauthorized use of a legitimate device.           |
| **Core Method**  | Public/Private key pairs & Proof of Possession. | Local authentication (PIN/Bio) or visual/biometric checks. |
| **LoA High**     | Mandatory for LoA High PIDs.                    | Supported by certified Wallet authentication.              |
The device binding of PIDs is performed by the ISO/IEC 18013-5 and ISO/IEC 23220-2 algorithm (see [here](https://eudi.dev/latest/architecture-and-reference-framework-main/#532-isoiec-18013-5-and-isoiec-23220-2)).

**Lo user viene autenticato dall'autority**:
It is up to each QEAA Provider to implement the necessary User authentication processes. Note that, when User identity verification is necessary, it is likely that the User requesting a QEAA already possesses a PID. This would enable the QEAA Provider to carry out User identification and authentication at LoA high, by requesting and verifying User attributes from the PID in the Wallet Unit ([here](https://eudi.dev/latest/architecture-and-reference-framework-main/#36-qualified-electronic-attestation-of-attributes-qeaa-providers)).

---
### Capire perchè linkability può essere elimata su identification e non su attestation


OSSERVAZIONI:
- Perchè non diamo direttamente attributi separati e firmati invece di fare disclosure ?
- Cosa non va bene nel semplice inviare una credenziale firmata ?

---
Updates 24/03/2026
Capire in dettaglio:
1. Come si aggiungono le attestazioni, come si mostrano con selective disclosure;
2. A cosa servono gli pseudonyms, come si generano e come si usano;
3. Perchè non si limitano a firme ? Probabilmente il motivo è legato a propietà che implicitamente o esplicitamente devono essere soddisfatte. Identificarle.

### Risposte

1. Come si aggiungono le attestations:
	- Allora, per quanto riguarda la patente, mDLs (mobile driving license), viene esplicitamente dichiarato nella documentazione ([qui](https://github.com/eu-digital-identity-wallet/eudi-doc-attestation-rulebooks-catalog/blob/main/rulebooks/mdl/mdl-rulebook.md)) che si fa riferimento al modello ISO 18013-5, stesso usato per l'identificazione, pertanto dando per scontato la sua efficienza (l'avevamo detto insieme l'altra volta) non indagherò. Eventuale approfondimento [qui](https://www.dock.io/post/iso-18013-5#data-structure).
	- Le altre **attestazioni** vengono **ottenute** col seguente protocollo:  User si assicura che il provider sia reliable (in Official trusted list o Lists of Trusted Entities (LoTE) ). Provider autentica lo user ( seguendo il protocollo di autenticazione OpenID4VC mostrato nel capitolo legato all'identificazione ) ([Attestation Issuance Interface](https://eudi.dev/latest/architecture-and-reference-framework-main/#51-attestation-elements)), e valida la wallet instance ( Usa wallet instance attestation, tutto spiegato nel capitolo del trust model ). Dopo che lo user vede e valida l'attestation, il wallet verifica la firma del provider sull'attestazione e questa viene memorizzata nel wallet.
	**Come vengono salvate**? 
		EUDI specifica formati nei quali bisogna obbligatoriamente salvare le proprie attestations standardizzati:
	- **CBOR**: Concise Binary Object Representation, usato nello standard **ISO/IEC 18013-5**. Questo formato è obbligatorio per le patenti (mDLs), può essere usato per qualsiasi altra attestazione se l'attestation provider rimane compliant allo standard ([pointer alla documentazione](https://eudi.dev/latest/architecture-and-reference-framework-main/#51-attestation-elements));
	- **JWT**: JSON token usati per **SD-JWT VC**. Non è obbligatorio esprimere nessuna credenziale in questo formato.
	- **W3C Verifiable Credentials Data Model v2.0 (W3C VCDM 2.0):** Formato opzionale valido solo per non qualified EAA, ancora in fase di sviluppo quindi non ne parlerò;
	**Come si mostrano le attestazioni**:
		In base al formato in cui sono state salvate, sono disponibili i diversi protocolli sopra citati, ISO 18013-5, SD-JWT VC ( e W3C ecc. di cui non parlerò). Per quanto riguarda ISO 18013-5 e SD-JWT VC, entrambi i protocolli consentono di mostrare attestations con selective disclosure attraverso la tecnica del salted hashes, device binding e user binding.
	-  **Salted hashes**:  Both in the case of CBOR or JSON token, the issuer of that token provides the user a signed token. In that token are contained informations for example: faminily_name: Rossi. Instead of being written in plaintext, in the token is contained the hashed value of this informations. Only the user can reveal that informations by sending them in clear and salt which is used to hash to the verifier, which will compute the hash and check it is the same as the one in the tokens;
	- **Device binding**: Le EAA sono legate ad un device attraverso una public key ed una secret key, device firma random challenge dal provider che verifica se il device (che contiene sk) è lo stesso associato all'attestation tramite la public key presente nell'attestation;
	- **User binding**: Durante il processo di rivelazione allo user sono mostrate le credenziali che stanno per essere inviate ed è richiesta autenticazione (biometrics, pin, ecc.);

Nella sezione ([5.3 Attestation formats and proof mechanisms](https://eudi.dev/latest/architecture-and-reference-framework-main/#51-attestation-elements)) è presente una tabella riassuntiva.

|Feature|ISO/IEC 18013-5 / 23220-2|SD-JWT VC|W3C VCDM v2.0|
|---|---|---|---|
|**Data Format**|CBOR (Binary)|JSON Web Token (JSON)|JSON-LD (Linked Data)|
|**Proof Type**|Embedded (Salted Hashes)|Embedded (Salted Hashes)|Detached or Embedded|
|**Key Use Case**|Proximity (e.g., mDL)|Remote (e.g., remote identification)|General (requires profiling)|
|**Wallet Support**|Mandatory|Mandatory|Optional|
2. A cosa servono gli pseudonimi?
	   Questi sono i possibili usecase:
	- Gli pseudonimi possono essere usati per autenticare un utente quando non è necessario per la relying party conoscere l'identità dell'utente;
	  - Utente che desidera registrarsi senza rivelare la propria identità come sopra, ma associando al proprio pseudonimo un informazione del tipo "vivo a roma", "ho più di 18 anni", che può essere corredata mostrando con selective disclosure attributi di una o più attestations;
	  - Registrarsi come sopra, ma aggiungendo una garanzia per la relying party che l'utente potrà registrare sul proprio sito solo un certo numero limitato di pseudonimi;
	- Uno pseudonimo che è utilizzabile e linkabile da diversi attori;
	  
	Come si generano e come si usano?
		Nel momento di registrazione, lo user genera chiave pubblica e chiave privata, memorizza privata nel wallet secure crypto ecc. e invia la chiave pubblica al server (Relying Party), eventualmente al momento della registrazione possono essere presentati attributes come spiegato precedentemente.
		In fase di accesso, viene generata ed inviata random challenge, che viene firmata da chiave privata dello user e inviata e verificata da relying party. Molto semplice.
3. Perchè non si limitano a firme ? Quali propietà devono essere soddisfatte ?
	   Effettivamente guardando bene quello che viene fatto, sono solo firme. Infatti authentication e presentare attestation si basa solo protocolli per presentare in maniera sicura credenziali firmate, facendo opportune verifiche su attendibilità di relying party, attestation authority, user e device security, revocation lists ecc. Niente di più.
	   Per quanto riguarda selective disclosure su identità e attestations, la cosa si fa più complicata. Infatti in questo caso si usano salted hashes insieme a firme, inserendo però diverse problematiche che non garantiscono che la privacy venga preservata. Tutto nel paper suggerisce che l'unico modo per colmare questi security breach è implementare zero knowledge proofs.
	   Gli pseudonimi invece non sono ne firme ne hanno a che fare con salted hashes, ma sono solo key pairs utilizzati per l'autenticazione evitando di rivelare la propria identità.


---
## Problemi che trovo io

#### **Information leaking on salted hash technique**
Rivelare che sono maggiorenne, con salted hash e senza zero knowledge, significa rivelare il campo completo sulla mia carta di identità e quindi la mia data di nascia, rivelando informazioni. Stessa cosa per esempio per dimostrare che sono italiano rivelo che vivo a Roma o addirittura il mio indirizzo, in poche parole leaking di informazioni legato alla scarsità della granularità di cui dispone il protocollo di selective disclosure con salted hashes.

Soluzione proposta da loro: chi rilascia l'attestation può inserire in essa un campo per esempio in cui dice: maggiorenne ? si/no. In questo modo puoi mostrare di essere maggiorenne senza rivelare la data di nascita.

Chiaramente pessima soluzione perchè se devo dimostrare di avere più di 19 anni ? più di 20 anni ? Non ha senso questo tipo di soluzione.

---
#### **User leaking**
**Questa era già stata affrontata e non l'ho trovata io.**
So I have a doubt. If the relying party is asking for a  specific information to the user, for example if the user is over 18, and the user does not want to reveal any information other then that he is over 18 years old, the provider will ask the wallet a salted hash proof. Let's say for example of the PIDs. The PIDs will be signed by an entity, I will hash for example the name field, and all others field except for the birth date field.  So the provider will recieve a PID cancelled in all field except birth date (we are still leaking birth date year), and see that the document is signed by for example italian governament so is valid. Still, how are we proving that the birthdate is associated to our identity ? Would we need another proof ?
You could argue that we may just show that we are 18, but I would say that in this implementation we are allowded for example to use another person's document in our wallet to authenticate ourself and I think this is a problem and should not be possible.
-
Quando viene presentata attestation viene fatta challenge e verification device binding tra user device e attestation.
-
In this way is easy to understand user identity tho, because selective disclosed attribtues tied to a device, device tied to pid, pid reveals identity, once identity is revealed I can link it to the device, and then to the selective disclosed attributes, vanishing privacy. Once only is overloading provider, and also a lot of load of work on users, and time limit does not change that the action I proposed can be done in a short amount of time.
-
**3. The Planned Solution: Zero-Knowledge Proofs (ZKP)**

The sources conclude that the **"only viable mitigation"** to completely break the chain you described is the adoption of **Zero-Knowledge Proofs (ZKPs)**.

Quindi questo è un buco completo nel portafogli, ed è completamente riconosciuto anche da loro, quindi qualcosa su cui si può lavorare (linkability of identity of user between relying parties, and between relying party and authentication provider (which is harder to obtain) ).

---
Come fa lo pseudonym ad essere legato all'identità dello user se poi consiste solo in un keypair generato dal wallet ?
Perchè va presentato insieme ad un attestation almeno la prima volta, o viene fornito da thrusted third party e quindi garantisce certi privilegi. Quindi comunque possibile vittima di linking attack.

---
## Concluisioni al 26/03/2026
Le mie conclusioni in questa fase sono le seguenti:
EUDI wallet con l'attuale architettura è una piattaforma utile per collezionare documenti (attestaion) firmati che saranno riconoscibili in tutta europa, per salvare e presentare la propria identità, e come strumento di firma elettronica con valenza legale. Queste sono funzionalità che incontrano e soddisfano tutti i irequirement sfruttando semplicemente firme digitali, secure cryptographic environment e protocolli ultra-collaudati per la presentazione di credenziali come ISO 18013-5 e OpendID4VC.

Per quanto riguarda gli altri obiettivi che l'architettura ARF si propone come la presentazione di credenziali parziali attraverso salted hashes, queste non incontrano criteri di sicurezza sufficientemente robusti, e causano privacy leeaking (linking attack e l'altra vulnerabilità che avevo trovato io). Inoltre gli pseudonimi con l'implementazione attuale sembrano abbastanza inutili. Nel senso che comunque gli pseudonimi sono semplicemente credenziali salvate nel portafogli, quando non si vuole accedere con il proprio PID. Non hanno nessuna particolare funzione nè sfruttano nessuna particolare tecnologia.

--- 
Si può aggiungere la carta di identità scannerizzandola ?
Visionare implementazione italiana del wallet.
Il PID è permanente ? è prevista flessibilità ? Può cambiare ?
W3C Verifiable Credentials Data Model v2.0 (W3C VCDM 2.0) approfondire.
Rischio di sputtanarsi andando a chiedere credenziali ad-hoc agli issuer.
La questione delle entità certificate (che solo loro possano dare attestazioni) potrebbe portare dei problemi.

Controllare se è possibile utilizzare chiavi al di fuori dell EUDI Wallet
3.9 Qualified Electronic Signature Remote Creation (QESRC)
The Wallet Unit will allow the User to create qualified electronic signatures or seals over any data.

---
ALTRE POSSIBILI DIREZIONI: 
- Multi factor authentication: usare due smartphone o 1 smartphone ed 1 pc e fare multiparty computation o 2 factor authentication per evitare single point of failure. Si ricollega a [Navigating secure storage] paper. Trovare comunque altri modi per bypassare il secure storage requirement. Esplorare blackbox e ipotetiche backdoor del secure storage https://news.dyne.org/privacy-in-eudi/ e https://zenroom.org/?ref=news.dyne.org analizzare queste pagine.
- Rimpiazzare salted hash con pedersen commitment e usare zk per anonimicità, inoltre usare interactive zero knowledge per evitare di essere schedati (deniability);
- (eventualmente esplorare 2PC tra User e Relying Party);

next step
vedere nei lavori che posso controllare se le problemi del point of failure del secure environment e  minimizzare le informazioni condivise e deniability sono state affrontate.


---
## **FINDINGS 04/2026**:
Problema
Se fare posst quantum è così semplice perchè non è stato già fatto ? Dove sta la difficoltà ?
Un replacement di ECDSA con firma post quantum che problemi porta ?
Risposta
Non si può perchè, come dicono Anja Lehmann , Andrey Sidorenko , and Alexandros Zacharakis nel paper "Vision: A Modular Framework for Anonymous Credential Systems", current SEs merely support ECDSA signatures over P-256 curves. [[MasterThesis/Papers/Vision, A Modular Framework for Anonymous Credential Systems/paper.pdf#page=4&selection=43,15,44,43|paper, page 4]] . Inoltre occorre notare che gli algoritmi ZKP post quantistici sono ancora in fase di ottimizzazione e presentano una complessità circuitale più elevata (non sono entrato nello specifico, opinione dell'AI che ho trovato attendibile).
Il **device binding** richiede il coinvolgimento diretto dell'hardware sicuro (**Secure Element**), il quale è attualmente limitato a crittografia legacy come **ECDSA**.
Finding interessante
In sintesi:
- **Limite Hardware:** I chip degli smartphone attuali non supportano la matematica necessaria per firme avanzate (come **BBS**) o per calcolare **ZKP** nativi.
- **Vincolo di Sicurezza:** Per garantire l'anti-clonazione (LoA High), la chiave di binding deve risiedere nell'SE e non può essere estratta.
- **Il "Ponte" Complesso:** Per usare firme più anonime e flessibili, bisogna "tradurre" la firma ECDSA dell'hardware nel linguaggio del protocollo avanzato tramite **ZKP esterni** (come ZKAttest o zkSNARK), rendendo l'intero sistema molto più pesante e lento rispetto a una soluzione puramente hardware.
Senza questo vincolo hardware, il rimpiazzo con sistemi più complessi e performanti sarebbe immediato e quasi istantaneo.




Prendi ZK system proof esistente STARK o LIGERO o LIGETRON
Quanto ci mette LIGERO a creare proof su ML DSA/FALCON (firme post quantum sicure) ?
Attualmente è LIGERO il software utilizzato da ZK Felllow. Questi sono i tempi:

|Tipo di ZKP|Protocollo Esempio|Tempo Prover|Dimensione Prova|Note|
|---|---|---|---|---|
|**Sigma Protocols**|BBS + Schnorr|**Millisecondi**|**~Bypte/KB**|Massima efficienza, richiede supporto curve pairings.|
|**SNARKs Ottimizzati**|Woo et al.|**1.6ms**|**400B**|Estremamente veloce ma privacy limitata (DL assumption).|
|**Ibridi (Bridging)**|ZKAttest/CDLS|**~800ms**|**~150KB**|Compatibile con chip attuali (ECDSA), privacy statistica.|
|**SNARKs Generici**|Groth16/Ligero|**Secondi**|**Variabile**|Molto complessi, ideali per statement arbitrari.|



è possibile usare due smartphone con due identità ? Si è possibile, ma saranno considerate due diverse wallet unit. Questo cosa implica ? Nulla di fatto si può comunque fare digital signature, mostrare i propri attributes ecc.