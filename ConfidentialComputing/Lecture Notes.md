Ragioni per cui crediamo che un calcolo eseguito su una macchina corrisponda effettivamente a quello che ci aspettiamo che la macchina stia eseguendo:
Trust assumption of correct behaviour of component without direct verification.

**TCB** (Trusted Computing Base): unità più piccola sulla base del quale un sistema viene costruito.
Più piccolo è il TCB più grande è il controllo che abbiamo sul codice, perchè l'unità di cui ci fidiamo è piccola quindi ingrandendo l'ordine di unità non ci fidiamo più, è provato che quello funziona bene senza fiducia.

In questo settore andiamo a levare la fiducia ai sistemi cloud e non basati su hardware, non si dà per scontato che un cloud provider rispetti le sue policy, senza accedere ad esempio ai nostri dati o dando effettivamente a disposizione la macchina che l'utente si aspetta di ricevere.

Rischi in un infrastruttura cloud:
**Insider threat**
- Admi con accessi privilegiati;
**External attack**:
- Side channel;
- Hpervision escape;
- Physical threats;

![[Pasted image 20260928114022.png]]

Questo corso si interessa della protezione di Data-in-use, dati carciati in memoria RAM, in CPU, e le loro esposizioni in chiaro durante le computazioni in CPU. Vulnerabili ad attacchi su OS compromessi.

**Confidential Computing**: pratica per proteggere dati mentre vengono utilizzati, facendo  in modo che i calcoli vengano eseguiti in TEE (hardware security);
Questi sistemi garantiscono che quando i dati escono dall'ambiente protetto sono cifrati, mentre se rimangono dentro sono decifrati ma protetti da hardware security.

TEE: area hardware protetta isolata e confidenziale:
- Esecuzione isolata rispetto all'OS;
- Memoria cifrata e integrità verificata;
- Remote attestation per provare fiducia a third parties;

Attributi garantiti:
- Confidenzialità;
- Integrità;
- Attestazione;

Come funzionano le attestazione:
è un processo che consente ad un remote party di verificare che un codice sta  runnando in un genuino, inviolato TEE.

Quote: hash of enclave code and data;
Contiene l'identità del TEE, ed altre informazioni relative ad esso;

Applicazioni pratiche:
- Finance;
- Salute;
- Confidential Machine learning;

Quindi tu:
- Verifichi che l'hardware sia apposto con Quote/Attestation inviando a server Intel/AMD/Google;
- Inietti le tue chiavi;
- Invii dati cifrati con quelle chiavi;

Threat model:
- Asset da proteggere;
- Chi sono gli attaccanti;
- Cosa possono fare;
- Assunzioni;
- Vettori d'attacco;

![[Pasted image 20260928125323.png]]

#### Memory Dump Attacks
Attaccanti con accesso fisico alla RAM possono ottenere dati con tools speciali.
#### Cold Boot Attack
Attacco fisico, vengono staccate le DIM e raffreddate velocemente, poi vengono estratti i dati anche senza CPU.
#### System Call
Attacchi di rete che intereccettano le system call per esempio attraverso un DNS malevolo.
Ci sono sistem call non supportate dentro un enclave, ma esistono comunque modi per comprometterli tipo fork bomb (generazione processi per saturare le risorse dell'enclave).
#### Moduli Kernel Compromessi
Intercettano System logs, modificare il sistema operativo e leggerne il contenuto.

Attacchi dai quali TEE ci possono proteggere contro Admin Cloud:
- VM Snapshots;
- Accessi al disco;

Side channel:
- Manipolazione dei meccanismi che gestiscano le pagine, page table ecc. che possono essere compromessi a meno che non vengono inclusi nel Secure Enclave;
- Branch prediction;
- ecc...
Gli enclavi funzionano ma non sono invincibili, sono misure in più da aggiugnere ad altri sistemi di scurezza;

è possibile osservare pattern di accesso su TEE virtual machine.
Mitigato da ORAM.

Thread-off nel threat model:
- Page swapping penalizzato da dimensione pagine limitata;
- Encryption aggiunge parecchia latenza e CPU overhead;
- Restrizioni di programmazione (multithreading limitato, fork non disponibile);
- Debugging più difficile, visibilità failure limitata, ecc.;

# Hardware root of trust
**Root of Trust** (RoT): componente hardware a basso livello (boradcom) che viene usato per costriure firme digitali e strutture ad albero;
Su questo componente si mette su un Secure Boot. Possono essere Root of Trust hardware o software.
Software non è abbastanza sicuro:
- La chiave può sempre essere modificata;
- Gli amministratori non sono sempre fidati;
- Sono fin troppo facili da compromettere;

**Secure boot** serve a proteggere da attacchi di natura bootkit e rootkit. Usa chain of trust:
1. Si legge il codice macchina;
2. Si verifica che venga usato un bootloader firmato;
3. Il bootloader avvia un OS firmato e sicuro;

TPM (Trusted Platform Module) si usa per salvare più versioni di una firma digitale di diverse componenti (firmware, kernel, ecc.). 
Hanno delle controindicazioni:
- Hanno vita breve e numero di operazioni limitato;
- Ne esistono anche software;
- Si usa per verificare che il boot è in uno stato atteso;
- Puoi salvare dentro chiavi usate per storage ed effettuare operazioni di attestazione;
- Throughput basso rispetto a normali CPU;
- Debugging opaco come enclavi;
- Algoritmi Vendor-specific;

Disponibili su macchine virtuali, vengono spesso virtualizzate o offerte dai cloud provider.
Consente di avere un secure storage ad enclavi come trustzone che non ne dispongono.

# Key provisioning
Include operazione di generazione, mantenimento e utilizzo di chiavi crittografiche.
Lifecycle:
1. Generazione;
2. Associazione al dispositivo che le ha generate. Si fonde la chiave unica del dispositivo con le nuove chiavi generate e si crea un attestazione con una firma;
3. Secure Storage: Non può essere utilizzata in altri dispositivi perchè viene legata subito ad una chiave hardware che vive solo nel TEE;
4. Utilizzo: le chiavi possono essere utilizzate solo se il device è untampered;

Sealing: cifrare chiavi con chiave hardware che risiede in un solo TEE.


---
# SGX Enclave
Cosa garantiscono:
- OS non può vedere cosa avviene all'interno dell'enclave;
- MEE (Memory Encryption Engine), cifra le pagine in DRAM;
- Possibile verificare identità quando è inizializzato con Attestation;
- Possibilità di sigillare dati hardware con chiavi visibili solo all'interno dell'enclave;

SGX Overview:
![[Pasted image 20260929114839.png]]


Usecase:
- Finance;
- Healthcare;
- Cloud & Conf. Comp.;
- Machine Learning;
- DRM (Digital Right Management);

Enclave Lifecycle Overview:
1. Enclave creation;
2. Page addition & initialization (con MR ENCLAVE);
3. VIene creata una prima attestazione;
4. L'applicazione usa l'enclave chiamando ECALL ed OCALL per entrare ed uscire;
5. Spegnimento, enclave distrutto e memoria rilasciata;

Ci sono una serie di istruzioni che vengono prodotte in fase di compilazione del codice, tradotte per SGX. Le istruzioni con parametri della chiamata vengono passate nel buffer Call gate. Bisogna cercare di ridurre al massimo le transizioni tra gli ambienti perchè sono costose.
Struttura della memoria classica con Stack, Heap, ecc.

AEX (Async Exit) protegge la confidenzialità dell'enclave. Viene chiamata quando l'enclave viene messo a rischio. Sono comunque possibili timing attack.

SSA (State Save Area) Area nella quale vengono salvati stati dell'enclave. Una sorta di PCB per i processi fatto per l'enclave.

EPC è dove risiede la memoria degli enclavi.
I page swap causano AEX e dunque perdita di risorse.

### SGX attestation
Serve a verificare che l'hardware non è stato modficato e l'esecuzione sta avvenendo in maniera genuina. Esistono tanti tipi di attestazione.
Si usano attestazioni locali o remote, verificate da entità diverse (IAS, Intel Attestation Service, DCAP per le locali). Si può convertire l'attestation da local a remote.