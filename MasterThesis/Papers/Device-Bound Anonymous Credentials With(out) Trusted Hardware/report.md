Il paper **Device-Bound Anonymous Credentials With(out) Trusted Hardware**  affronta il problema di come legare le credenziali anonime (AC) a un dispositivo fisico per impedirne la duplicazione (non-transferability), proteggendo al contempo la privacy dell'utente anche se l'ambiente sicuro del dispositivo è compromesso o remoto.

### Perchè il binding
Il paper **"Device-Bound Anonymous Credentials With(out) Trusted Hardware"** di Friedrichs et al. affronta il problema di come legare le credenziali anonime (AC) a un dispositivo fisico per impedirne la duplicazione (non-transferability), proteggendo al contempo la privacy dell'utente anche se l'ambiente sicuro del dispositivo è compromesso o remoto

### Sfide principali
- **Limiti Hardware:** Molti smartphone attuali non hanno SE certificati "LoA High". Per questo, il regolamento prevede l'uso di **Remote HSM** (server remoti), che però aggravano i rischi;
- **Fiducia nell'Hardware:** Gli SE attuali sono spesso "black-box". Se un produttore o un attaccante ne sovverte il codice, potrebbe inserire canali subliminali per identificare gli utenti.
- Non zero knowledge attualmente + implementazioni fragili;

**2. Nuovi Modelli di Sicurezza**

Il paper introduce due varianti del sistema:

- **DBAC (Context-Aware):** L'SE locale conosce il contesto della presentazione (es. a chi sto mostrando i dati). Adatto per hardware locale fidato.
- **BDBAC (Context-Blind):** L'SE contribuisce alla firma in modo "cieco", senza conoscere il contesto o i dati presentati. È il modello fondamentale per i **Remote HSM** per garantire la sovranità digitale e la privacy totale.

Propone inoltre una **gerarchia di 4 livelli di privacy (Unlinkability)** basata sul grado di corruzione dell'SE:

- **Unlink-0/1:** SE onesto (con o senza accesso temporaneo dell'attaccante alle interfacce).
- **Unlink-2:** SE sovvertito (il codice interno è malevolo ma stateless).
- **Unlink-3:** SE completamente sotto il controllo in tempo reale dell'avversario (necessario per il modello Cloud HSM).

**3. Le Tre Costruzioni Proposte**

Tutti i protocolli utilizzano le **firme BBS** (standard IETF) per la credenziale principale e firme standard (Schnorr o BLS) per il contributo del dispositivo.

1. **BBS-Schnorr:** Utilizza firme Schnorr per il binding. È efficiente ma raggiunge solo il livello **Unlink-1**; un SE malevolo può usare la casualità della firma Schnorr per tracciare l'utente.
2. **BBS-BLS:** Sostituisce Schnorr con firme deterministiche **BLS**. Raggiunge il livello **Unlink-2** (resistenza alla sovversione) perché l'output dell'SE è unico e non può contenere canali subliminali.
3. **BBS-BlindBLS:** La soluzione più avanzata per il **Cloud HSM**. Utilizza firme BLS "cieche". Raggiunge il livello **Unlink-3** e la **SE-Obliviousness**, garantendo che nemmeno un server remoto malevolo possa sapere cosa l'utente stia firmando o chi sia.


### Pregi
Hanno risolto problema alla base di longfellow che era il secure element come single point of failure, secure element che lavorava ancora con ECDSA quindi Ligero aveva bisogno di ottimizzazioni ad hoc per essere veloce (anche se alla fine Google ci è riuscita) e hanno risolto il problema della scarsa compatibilità dei secure element.

### Difetti
Ogni versione da loro trovata presenta delle vulnerabilità in caso di hardware compromesso:
- **BBS-Schnorr (Vulnerabilità alla sovversione):** Questa variante è esplicitamente indicata come **non resiliente alla sovversione dell'hardware**;
- **BBS-BLS (Mancanza di Obliviousness):** Sebbene risolva il problema della sovversione essendo deterministica, questa variante **rivela all'SE il messaggio che sta firmando**;

La loro idea **NON È POST QUANTUM SICURA**.

Presenta **fughe di metadati (timing)** insormontabili nel modello Cloud HSM.

HSM essendo on cloud richiede connessione (**NO OFFLINE USAGE**) e chiaramente introduce latenza e ritardi nel funzionamento.