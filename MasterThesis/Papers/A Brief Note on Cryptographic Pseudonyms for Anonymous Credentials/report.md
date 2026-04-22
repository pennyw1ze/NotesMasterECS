The paper aims to describe pseudonyms for the upcoming EUDI wallet.

### Motivation 
REASON WHY PSEUDONYMS ARE GOOD:
The difference between such an EUDI pseudonym and an arbitrary account creation with fabricated attributes at that RP is that the RP receives a guarantee that the pseudonym is backed by a credential that has been issued to an end user. **Anonymous** but **accountable**.

A secondary goal could be to prevent troll bots from creating a large number of accounts controlled by a small number of people.  **ANTI DDOS**

Pseudonyms only add privacy when coupled with selective disclosure. 
Pseudonyms are categorized as:
- **Verifiable** (verifiably derived from a user controlled key);
- **Certified** (verifiably derived from a user holding a certain credential);
- **Scope-exclusive** (verifiably derived for a cer- tain scope, and enforcing pseudonyms to be unique per scope). 

### Actors
- **Holder**;
- **Wallet**;
- **Holder key**;
- **PID provider**;
- **Relying party**;

#### Security and privacy requirements
The authors identified the following security and privacy requirements:
- **Soundness:** A holder shall be able to create pseudonyms only when they possess an underlying EUDI credentiall;
- **Unforgeability:** A malicious holder shall not be able to create a fake ID;
- **Unlinkability:** We know;
- **Unobservability:** : The PID provider should not be able to trace/observe the usage of pseudonym;
- **Selective disclosure:** We know;
- **Scope-exclusive pseudonyms:** RP can be guaranteed that a user can only derive a unique pseudonym using his credentials;
- **Transferability:** Pseudonyms derived from an EUDI credential shall be transferable from one wallet instance to another;

## Technical solution
1. When credentials are issued, they must contain a new attribute, pseudonym_seed (pns), high entropy unique value, never disclosed directly;
2. For a pseudonym presentation, wallet computes pseudonym as output of PRF applied to an RP identified (scp, ex: website domain) and an idx (integer to upper bound pseudonyms), using as seed the pns. In addition, the holder shares only non-identifying attributes and the RP-specific identifier nym with the RP and provides a zero knowledge presentation that proves that: 
	1. The holder has a valid credential and knows the device holder key (when device binding is required);
	2. That credential includes (a commitment to) pns as one attribute;
	3. The pseudonym ID nym has been created by computing a PRF keyed on pns with the RP identifier scp (and an optional, possibly hidden, idx).

For transferability, allowing a set of pseudonyms to be transferred from one EUDIW instance to another is transferring the pns after re-provisioning the holder’s core EUDI credentials on the new devices.

I THINK THIS PAPER IS NOT REALLY VALID FOR THE FOLLOWING SENTENCE:

```
That is, adding such pseudonyms reduces perfect privacy of BBS-based ZKPs to computational privacy, which can be retroactively broken when efficient quantum computers become available. 
```

