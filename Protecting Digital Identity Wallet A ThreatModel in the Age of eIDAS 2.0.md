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
ecc.


