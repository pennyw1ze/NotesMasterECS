They restrict ourselves to the analysis of three key processes that involve Level of Assurance (LoA) high, namely wallet activation and both issuance and presentation of Person Identification Data (PID) and mDL.

They formally scope our privacy evaluation to the upper application (EUDIW functional architecture), data representation (e.g., SD-JWT, mDOC, JSON, CBOR), and session layers (e.g., OpenID4VCI,VP over HTTPS). We do not formally evaluate the lower transport (e.g., TCP or UDP), network (e.g., IP), data link (e.g., Near-Field Communication (NFC), Bluetooth Low Energy (BLE)), or physical layers (e.g., Wi-Fi, Ethernet, or RFID).

```formalization
EUDIW ∶= [Asset] [Action] [Flow] [Scenario] [Entity]
Asset ∶= PID ∣ QEAA ∣ EAA ∣ Pub-EAA
Action ∶= issuance ∣ presentation ∣ verification ∣ revocation
Flow ∶= proximity (supervised ∣ unsupervised)
∣ remote(cross-device ∣ same-device)
Scenario ∶= eGOV ∣ eKYC ∣ mDL ∣ eSIM ∣ ePrescription
∣ QES(wallet-centric ∣ QTSP-centric)
Entity ∶= Wallet ∣ AP ∣ RP ∣ WP ∣ QTSP
```

# Threat
Threats that our researchers find out:

## 1) **Linkability of quasi-identifiers**
When users are required to disclose their attributes, the presentation is signed with, and therefore reveals, the associated PK𝑢 for verification. Furthermore, the PK𝑢, and $EAA_{revoc\_id}$ persist regardless of the session. These quasi-identifiers, along with the PoA (Proof of Association), are used to identify the session and verify the presentation and can be leveraged to correlate repeat interactions. For salted hash technique, salted hashes remain unchanged every time the user present his attributes, therefore resulting in an easily linkable element. It should also be emphasized that not all quasi-identifiers have the same degree of linkability, e.g., PK𝑢 is involved in all of a user’s interactions with LoA high, whereas EAArevoc_id is revealed only when using a specific EAA, and a disclosure is revealed only if this specific attribute is requested. 

```model
Misactors: RPs, APs, WPs, supervisory bodies, and ext. actors
Attack Flow: (Mis)actors (un)intentionally accessing the information
	within the communication channels or databases containing
	stored data can correlate the quasi-identifiers with attributable
	information within the same database, publicly available, or
	auxiliary information from other sources
Trigger: The RP with any other data processor with whom data has
	been shared, including supervisory bodies. Externals that gain
	access (un)lawfully. The user in the event of sharing data with
	supervisory entities
Preconditions: Access to attributable data and quasi-identifiers that
	are linked to each other w.r.t attributable data (e.g., EAA) or
	linkable data (e.g., selective disclosure, persistent keys)
```

## 2) Identifiability risk between service providers
In this case, we have the relying party which is able to reconstruct full identity thanks to cooperation with the attestation provider.

```model
Misactors: RPs, APs, supervisory bodies, and external actors
Attack Flow: APs collect linked pseudonyms, while RPs or external
	entities combine these with auxiliary data. This data aggrega-
	tion enables the re-identification by correlating pseudonymous
	identifiers with attributable information
Trigger: RPs can (un)intentionally acquire attributable data when per-
	sistent identifiers, such as EAArevoc_id, or public keys for verifi-
	cation are communicated
Preconditions: Access to quasi-identifiers that are linked to each other
	and correlated with attributable data
```
## 3) Re-identification of pseudonymous identities
Since implementation of pseudonyms is leaved to Member State, they could come up with legislative measures that forces RP/AP to reveal informations about the users under certain circumstances, violating their privacy.

```table
Misactors: MSs, supervisory bodies
Attack Flow: Legislative measures force users, APs, or RPs to force-
	fully collect or disclose sensitive data or process such data
	without explicit user consent or notice
Trigger: Legislative measures by MSs in relation to national or pub-
	lic security, prevention or investigation of criminal offenses of
	economic or financial interest
Preconditions: EUDIW’s core functionalities and communication pro-
	tocols alongside EU legislation. Quasi-identifiers that are linked
	to each other based on attributable data, transaction records,
	and retention periods
```
## 4) Non-repudiation of wallet-related actions
The user will not be able to deny his involvment in any wallet related action due to digital signatures allegation which binds everything to a single user, removing any chance of plausible deniability.

```table
Misactors: RPs, MSs, supervisory bodies, or external third parties
Attack Flow: Upon (un)-intentional revelation of the public key that
	verifies a signature, and it is correlated to an identifiable at-
	tribute, the user is uniquely identified (rules out the possibility
	of plausible deniability).
Trigger: User wallet signs electronic transactions, including presenta-
	tions, with attributable data intended for data sharing
Preconditions: PoP linked to a PoA, and EAA with signed data
```
## 5) Excessive data disclosure to prevent service exclusion
Who establishes what attributes are really necessary to be disclosed for a given Relying Party ? A RP could ask for more attributes thant necessary, fooling user and trying to establish identification.

```table
Misactors: RPs or SPs
Attack Flow: The RP demands excessive data disclosure in their re-
	quests before the provisioning of the service
Trigger: The user authorizes an RP’s presentation request without
	reviewing or understanding what attributes are requested
Preconditions: Ambiguous collection and disclosure policy
```

## 6) Data formats undermine data protection principle
The adoptions of some standards which is forced by the ARF architecture definition is actually making difficult (if not impossible) to realize full data protection envisioned by eIDAS. Even with temporary attestations, some attestations like mDLs and ID are permamente and their data can be stored.

```table
Misactors: RPs, or external actors.
Attack Flow: (Un)-intentional data access by misactors with access to
	communication channels or datastores.
Trigger: The EUDIW use of communication protocols that do not
	accommodate unlinkability along signed data
Preconditions: EUDIW compliance with EU’s regulation on core func-
	tionalities and communication protocols (European Commis-
	sion, 2024a)
```
## 7) Detectability of wallet-related actions via passive network monitoring
EUDIW’s communication relies on traditional Internet networks and, therefore, inherits its threat model (Christl, 2017). In this context, passive network monitoring denotes any inspection of traffic without modifying it, performed by any Machine-in-the-Middle (MitM). Therefore it is possible to monitor wallet usage for external actors.

```table
Misactors: Passive entities to the communication.
Attack Flow: Communication monitoring with correlatable and/or at-
	tributable data. Un/Intended access to data at rest.
Trigger: The active monitoring of user wallet communications or the
	(un)intended access to data at rest.
Preconditions: EUDIW compliance with EU’s regulation on core func-
	tionalities and communication protocols (European Commis-
	sion, 2024a).
```

---
Nel capitolo successivo vengono menzionate soluzioni al problema, tutte implementabili attraverso Zero-Knowledge proofs.
Viene anche menzionato DAA (Direct Anonymous Attestation), legata all'hardware tamper-resistance, che permette divulgazione selettiva ed unlinkability ma manca ancora una specifica per le attestazioni digitali in questo ambito.