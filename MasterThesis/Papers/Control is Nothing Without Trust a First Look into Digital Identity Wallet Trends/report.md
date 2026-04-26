This paper from university of trento guys describes mainly functional and non funcitonal requirements for digital identity wallets:

## Functional requirements
1. The main functional requirements for which they are developed are to **store, manage** (review, remove) **, and share** (explicitly selecting what data to store/combine and share into/outside the wallet.) the identity or its related data;
2. Identity wallets also provide **secure storage** for cryptographic material associated with the data;
3. The wallet may additionally have functionalities for **recovery** and **back up**;

The EUDI-ARF covers the aforementioned requirements along with other
functionalities such as the creation of (qualified) electronic signatures/seals that
demand the provision of interfaces to the services providing the functionality,
requiring identification with an eIDAS high LoA.

## Non-functional requirements
Authors took this requirements from Principle of SSI V3 ([here](https://sovrin.org/principles-of-ssi/)):
- **Representation**: Identity subjects must be able to be repre- sented by any number of digital identities;
- **Delegation (of control)/Provability**: Identity subjects must have the ability to control how their identity data is used and extert this control on their choice;
- **Decentralization**: A digital identity data must not be represented, controlled or whatever by a centralized system;
- **Security**: Identity subjects must secure their identity data;
- **Verifiability and authenticity**: Identity subjects are required to provide verifiable proof of the authenticity of their digital identity information;
- **Privacy and minimal disclosure**: Identity subjects must protect the privacy of their digital identity data and share the minimum digital identity data required for any interaction;
- **Transparency**: Identity subjects and all other stakeholders must have easy access to and verify information necessary to understand the incentives, rules, policies, and  algorithms under which agents and other components of the ecosys- tems operate.

The paper proceeds with a comparison between different existing wallets 