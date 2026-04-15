Dopo una breve introduzione, dove gli autori spiegano come l'autenticazione nel mondo digitale si stia spostando da soluzioni come più password per più siti a soluzioni central-control come OAuth2.0 (accedi con google), ecc., gli autori accennano problemi relativi alla privacy ed alla mancanza di controllo dell'utente sui propri dati, e come con l'architettura eIDAS 2.0 si voglia passare ad un sistema basato sulla sovranità dei propri dati (SSI).

Il threat modelling dell'architettura di EUDIW, anche in questo paper, è stato effettuato seguendo due framework, LINDDUN e STRIDE (Microsoft).

Ok, è figo perchè questi framework sono stati usati per fare un analisi sulla generica architettura, ma io potrei riusarli per fare una security analisi sull'implementazione italiana del wallet e trovare varie vulnerabilità.

Hanno trovato 49 vulnerabilità, 13 delle quali sono approposito di OpendID4VC, sia il protocollo per ottenere che quello per mostrare le proprie credenziali. Sul paper vengono riportati solo 7 delle 36 vulnerabilità inerenti EUDIW.

### Presentare una risposta senza autorizzazione
Un attaccante potrebbe rubare o presentare una risposta impersonificando un altro utente e generando una impropria verifica delle credenziali.

Attacchi ad OpenID4VC:
- Utilizzare l'issuer redirect URI registration system per indirizzare lo user in una zona sotto il controllo dell'attaccante;
- Un attaccante potrebbere intercettare e rubare un request uri in una sessione con un issuer per poi utilizzarlo per ottenere un access token e quindi delle credenziali;
- L'insufficiente entropia dei parametri request uri consente all'attaccante ti attaccare il protocollo provando ad indovinare questi valori;
- Utilizzo di one-time proof of wallet control rubate o già usate consentirebbe all'attaccante di ottenere accesso ad un portafoglio;

**ATTACCHI AL NOSTRO WALLET**:
1. Repudiation of requested/obtained/revoked verifiable credentials / Wallet Trust Evidence. 
	- **Actors**: Involves an entity denying the initiation or receipt of a transaction or an action;
	- **Attack description**: a malicious Holder can deny the request/gain of cre- dentials by trying to **remove the logs**
2. Credential theft: refers to the malicious acquisition of the Holder’s credentials to present it to the Verifier and obtain unauthorized access to the service.
	- **Actors**: Malicious holder and verifier;
	- **Attack description**: exploits the flaw in the presentation request to inject a  stolen/leaked credential in the communication with the Verifier to access the service;
3. Exploitation of faulty credential status check mechanism: occurs when the credential validity check mechanism is not designed in a privacy-preserving way (e.g., it demands direct communication between the Verifier and Issuer). Thus, it would leak sensitive information about the usage of credentials and enable Holder’s tracking.
	- **Actors**: User and RP;
	- **Attack description**:  curious Issuer can use the flaw in the Verifier’s credential validity check by the Verifier to obtain information about the Wallet’s actions and perform the Holder’s activity **linking**;
4. Unauthorized presentation **response** (VP token) **forwarding**: aims to impersonate the Holder at the Verifier by forwarding the VP token extracted from the interaction of Wallet and Verifier into the attacker session with the Verifier.
	- **Actors**: Entity stoling data, RP;
	- **Attackd description**: an attacker can send the stolen presentation to the Verifier instead of a presentation created by his Wallet;
5. Registration of non compliant entity: aims to register a non-compliant entity to operate in the ecosystem for a malicious purpose.
	- **Attack description**:  an attacker can misuse the flaw within a certification system to operate a non-compliant entity;
6. Exploitation of faulty data exchange protocol implementation: advantages the im- proper implementations of the exchange protocols without following the best practices that can lead to increasing the attack surface.
	- **Attack description**: obtains the Holder vp_token by exploiting a flaw on presenting the access token;
7. Data minimization violation: refers to the practice of requesting, obtaining, and storing more personal information from individuals than is strictly necessary for the intended service or transaction
