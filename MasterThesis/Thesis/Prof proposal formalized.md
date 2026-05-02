In base alle fonti analizzate, la proprietà di **Deniability (o Designated Verifier Proofs)** descritta nel "SoK" e nel "Vision Framework" **non è garantita né implementata** nel sistema specifico di Frigo et al. (Longfellow).

Ecco i dettagli tecnici che spiegano questa distinzione basandosi sui documenti:

1. Verificabilità Pubblica vs. Ripudiabilità

L'implementazione di Frigo et al. dichiara esplicitamente di voler produrre **"publicly verifiable arguments"** (argomenti pubblicamente verificabili).

- **Fiat-Shamir:** Per rendere il protocollo non interattivo (da sumcheck + Ligero), Longfellow utilizza la trasformata di **Fiat-Shamir**. Questa trasformata converte un protocollo a monete pubbliche in una prova che **chiunque** può verificare in modo indipendente una volta pubblicata, proprio perché le "sfide" del verificatore sono simulate tramite hash crittografici deterministici basati sulla trascrizione della prova stessa.
- **Conseguenza:** Poiché la prova è pubblicamente verificabile, essa costituisce una prova crittografica oggettiva della validità degli attributi che un terzo potrebbe verificare con la stessa certezza della Relying Party originale. Questo è l'opposto della "Deniability".

2. Deniability come "Feature" opzionale dei sistemi AC

Il documento **SoK (Source 14)** cita le _Deniable Presentations_ come una delle "ulteriori funzionalità" (Further Features) che le Anonymous Credentials (AC) possono offrire.

- Il SoK spiega che queste prove sono "vincolate a una specifica relying party" (Designated Verifier Proof), in modo che solo tale RP sia convinta della validità, poiché essa stessa avrebbe potuto generare una prova falsa.
- Tuttavia, mentre il SoK osserva che tali proprietà sono state proposte e sono compatibili con schemi come **BBS** attraverso prove di Schnorr, quando analizza il lavoro di **Frigo e shelat** (Sezione 3.2), si concentra sulla loro capacità di effettuare prove ZK sopra firme **ECDSA legacy**.

3. Focus di Frigo et al.: Efficienza su ECDSA, non DVP

Il contributo principale di Frigo et al. è superare il colloqui di bottiglia computazionale delle prove ZK su ECDSA/P-256 usando **sumcheck e Ligero** per rendere le prove pratiche su smartphone (circa 1.2 secondi per un flusso mdoc). Le fonti non menzionano l'integrazione di chiavi della Relying Party all'interno del circuito di prova per garantire la ripudiabilità. Al contrario, il sistema è progettato per essere un sostituto "drop-in" delle presentazioni mdoc standard, fornendo però l'unlinkability rispetto all'Issuer e alla Relying Party.

Sintesi della risposta

No, la proprietà di **Deniability** non è garantita dall'attuale implementazione di Frigo et al.

- **Frigo et al.** producono prove **pubblicamente verificabili** (grazie a Fiat-Shamir).
- La **Deniability** richiede che il protocollo ZK sia costruito in modo che il Verificatore possa simulare la prova (usando la propria chiave privata), una caratteristica che i documenti attribuiscono concettualmente al framework delle AC o a schemi come BBS, ma che non è parte del design di Longfellow focalizzato su ECDSA.

In breve: Longfellow ti dà **Anonymity** e **Unlinkability**, ma la prova che generi è "pesante" dal punto di vista legale/crittografico perché chiunque la veda può essere convinto della sua validità.