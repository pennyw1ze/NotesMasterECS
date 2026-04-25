Abstract about difficulty to introduce anonymous credentials with ECDSA with secure element and device binding.
The paper propose a solution that is using the widely adopted ECDSA signature scheme, adding efficient ZK arguments about SHA256 without requiring changes to mobile devices and without requiring non-standard cryptographic assumptions.
Our proofs for ECDSA can be generated in 60ms. When incorporated into a fully standardized identity protocol such as the ISO MDOC standard, we can generate a zero knowledge proof for the MDOC presentation flow in 1.2 seconds on mobile devices depending on the credential size.

## Contenuto
Ho dato un occhiata a quello che hanno fatto:
sostanzialmente hanno implementato una transizione perfetta dall'attuale implementazione di ARF piena di difetti a quella con zero knowledge, post quantum, efficiente, mantenendo il secure element attuale e costruendo prove arbitrarie (anche da ECDSA) in maniera efficiente (le prove su SHA256 ed altre sono ottimizzate in modo da runnare in secondi). Il loro lavoro si è concentrato sulle credenziali americane ma è stato talmente convincente che sta venendo implementato anche per quelle europee.
Riassunto Gemini affidabile (e non troppo interessante) sulle tecnologie:
Ecco un breve elenco delle tecnologie chiave utilizzate e come funzionano nel sistema:

- **Ligero (MPC-in-the-head):** È il sistema di prova ZK core. Funziona senza un "trusted setup" e permette di provare la validità di circuiti aritmetici. Viene usato per "avvolgere" l'intera prova, garantendo che sia succinta e trasparente.
- **Protocollo Sum-check:** Una tecnica di computazione verificabile che permette al verifificatore di controllare somme di polinomi su ipercubi booleani. Viene integrato con Ligero per evitare il collo di bottiglia computazionale delle trasformate NTT pesanti, rendendo la generazione della prova molto più veloce.
- **Codifica Reed-Solomon:** Utilizzata per i "polynomial commitments" all'interno di Ligero. Frigo et al. hanno introdotto metodi innovativi per eseguire questa codifica tramite **convoluzioni lineari** su campi non ottimizzati (come NIST P-256) e tramite **FFT additive** su campi binari.
- **Circuiti ECDSA e SHA-256 specializzati:** Longfellow usa circuiti ottimizzati per verificare firme ECDSA (standard NIST) e hash SHA-256. Per massimizzare l'efficienza, la verifica della firma avviene su **campi primi**, mentre quella dell'hash su **campi binari** (GF(2128)).
- **Coerenza Cross-Field (MAC):** Poiché il sistema usa campi diversi per compiti diversi, impiega un codice di autenticazione del messaggio (**MAC**) per garantire che il testimone (_witness_) sia coerente tra i vari circuiti (es. che l'output dello SHA-256 binario sia lo stesso input dell'ECDSA primo).
- **Trasformata di Fiat-Shamir:** Utilizzata per rendere il protocollo **non-interattivo**, permettendo al wallet di inviare la prova completa in un unico messaggio al verificatore.
- **Alberi di Merkle:** Impiegati per istanziare l'oracolo della prova nel sistema Ligero, basandosi esclusivamente sulla sicurezza delle funzioni di hash.
- **Primitive SIMD128 (Longfellow-zk):** Il fork europeo Dyne.org ha aggiunto ottimizzazioni a basso livello in **assembler** per velocizzare ulteriormente i calcoli crittografici sui dispositivi mobili.

## Difetti:
- La loro implementazione è post quantum, ma il loro punto forte (il fatto che si basino sugli attuali secure elements) è anche un punto debole perchè gli attuali secure elements si basano su ECDSA, rendendoli dunque **post-quantum insecure**. Risultato: le zk proof con Ligero sono post quantum sicure, il sistema di firma su cui si basano no. La loro proposta potrebbe essere adottata in una fase di transizione tra un SE con ECDSA ad un SE post quantum sicuro;
  
- 20'000 righe di codice in cui hanno già trovato degli errori e posto rimedio (il file con gli errori corretti si trova in questa cartella);

- Il SE rimane un single point of failure, in nessuna parte del paper viene proposto l'uso di multi party computation per eliminare il single point of failure del SE;

Il sistema di zero-knowledge proof introdotto da **Frigo et Al.** nasce come un protocollo **interattivo**, ma viene reso **non-interattivo** per l'applicazione pratica nel mondo reale.
Ecco i dettagli su come funziona e il confronto tra le due modalità:
- **Fondamenta interattive:** Il sistema si basa su una combinazione di due tecniche: un protocollo di **verifiable computation (VC)** basato sul _sum-check_ e il sistema di prova **Ligero**. Entrambi questi componenti sono intrinsecamente interattivi, richiedendo più round di "sfida e risposta" tra prover e verifier.
- **Trasformazione in non-interattivo:** Per rendere il sistema utilizzabile nelle credenziali digitali (dove il wallet deve inviare una prova in un unico messaggio), i ricercatori applicano la **trasformazione di Fiat-Shamir**. Questa tecnica sostituisce le sfide casuali del verifificatore con l'output di una funzione di hash crittografica, permettendo al prover di generare l'intera prova autonomamente.
**Protocollo Interattivo**

- **Vantaggi:**
    - **Meno assunzioni crittografiche**: (non necessita del "Random Oracle Model" richiesto da Fiat-Shamir);
    - **Semplicità logica:** È più facile dimostrare la validità (_soundness_) del protocollo;
- **Svantaggi:**
    - **Inefficienza di rete:** Richiede che sia il prover (il wallet) che il verifier rimangano online e connessi per tutta la durata dello scambio di messaggi.
    - **Incompatibilità con gli standard:** Protocolli come **OpenID4VP** si aspettano che la presentazione della credenziale avvenga in un singolo invio di dati;
**Protocollo Non-Interattivo**

- **Vantaggi:**
    - **Scalabilità e Usabilità:** Il prover invia un unico messaggio . Questo è fondamentale per scenari mobile o per la verifica asincrona.
    - **Portabilità:** La prova può essere memorizzata o inoltrata a terzi e verificata in qualsiasi momento senza coinvolgere nuovamente il wallet;
- **Svantaggi:**
    - **Complessità e vulnerabilità:** Rendere non-interattivo un sistema complesso come quello di Frigo et al. richiede implementazioni impeccabili (facile fare errori).
    - **Dimensioni della prova:** Spesso le prove non-interattive risultano più grandi rispetto a quelle generate round-by-round.
In sintesi, **Longfellow zk è non-interattivo**. Questa scelta è obbligata per poter integrare la tecnologia ZK nell'architettura EUDI, garantendo che un cittadino possa mostrare la propria identità con un click, senza dover gestire una complessa "conversazione" crittografica con il terminale di verifica.

