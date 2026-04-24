Abstract about difficulty to introduce anonymous credentials with ECDSA with secure element and device binding.
The paper propose a solution that is using the widely adopted ECDSA signature scheme, adding efficient ZK arguments about SHA256 without requiring changes to mobile devices and without requiring non-standard cryptographic assumptions.
Our proofs for ECDSA can be generated in 60ms. When incorporated into a fully standardized identity protocol such as the ISO MDOC standard, we can generate a zero knowledge proof for the MDOC presentation flow in 1.2 seconds on mobile devices depending on the credential size.

## Difetti:
- La loro implementazione è post quantum, ma il loro punto forte (il fatto che si basino sugli attuali secure elements) è anche un punto debole perchè gli attuali secure elements si basano su ECDSA, rendendoli dunque **post-quantum insecure**. Risultato: le zk proof con Ligero sono post quantum sicure, il sistema di firma su cui si basano no. La loro proposta potrebbe essere adottata in una fase di transizione tra un SE con ECDSA ad un SE post quantum sicuro;

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