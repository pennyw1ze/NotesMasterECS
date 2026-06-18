[13/06/26 12:20] Zurg: Volentieri. Questa è una dimostrazione di sicurezza per un protocollo di **Oblivious Transfer (OT)** fatta secondo il *simulation paradigm*, nel modello *semi-honest*. Ti ricostruisco prima il contesto e poi entro nei dettagli delle tre distribuzioni che vedi nelle slide.  
  
## Cosa stiamo dimostrando  
  
In un 1-su-2 OT ci sono due parti: un **Sender** che possiede due messaggi $x_0, x_1$, e un **Receiver** che ha un bit di scelta $b$. Alla fine il Receiver deve imparare *solo* $x_b$ e **nulla** su $x_{1-b}$, mentre il Sender non deve imparare $b$.  
  
Le slide trattano la **security against the receiver**, cioè: dimostrare che il Receiver non impara niente sul messaggio che *non* ha scelto, $x_{1-b}$.  
  
Il protocollo sottinteso è la classica costruzione PKE-based con *oblivious key generation*:  
- il Receiver genera onestamente una coppia di chiavi $(pk_b, sk_b) \leftarrow \text{Gen}(1^n)$ per lo slot $b$;  
- genera la chiave dell'altro slot $pk_{1-b}$ in modo **oblivious**, cioè con $\text{Samp}$, *senza conoscere la chiave segreta corrispondente*;  
- manda $(pk_0, pk_1)$ al Sender, che restituisce $c_0 = \text{Enc}_{pk_0}(x_0)$ e $c_1 = \text{Enc}_{pk_1}(x_1)$;  
- il Receiver può decifrare solo $c_b$ (ha $sk_b$), ma non $c_{1-b}$ (non ha $sk_{1-b}$).  
  
## L'idea del simulation paradigm  
  
L'intuizione è questa: la *view* del Receiver (tutto ciò che vede: la sua randomness, i messaggi ricevuti, input e output) non deve contenere informazione su $x_{1-b}$. Come si formalizza "non contiene informazione"? Si chiede che esista un **simulatore** $\mathcal{S}$ che, ricevendo *soltanto* l'input $b$ e l'output legittimo $x_b$, riesca a produrre una distribuzione **indistinguibile** dalla view reale.  
  
Il punto cruciale è che $\mathcal{S}$ **non riceve $x_{1-b}$**. Se riesce comunque a riprodurre tutto ciò che il Receiver vede, allora la view reale non può contenere informazione (estraibile in tempo polinomiale) su $x_{1-b}$ — altrimenti la view simulata, che di $x_{1-b}$ non sa nulla, sarebbe distinguibile da quella reale. Quindi "simulabile senza $x_{1-b}$" è la formalizzazione pulita di "il Receiver non impara nulla su $x_{1-b}$".  
  
L'obiettivo finale è proprio il simbolo $\approx$ tra i due riquadri della prima slide:  
$$\mathcal{S}(1^n, b, x_b) \;\approx_c\; \text{View}^\pi_{\text{receiver}}(1^n, b)$$  
  
## Le tre distribuzioni e la catena della prova  
  
La prova non passa direttamente da View a $\mathcal S$, perché tra le due cambiano *due cose contemporaneamente*. Si usa quindi una distribuzione intermedia, **Hybrid**, e si cambia una cosa alla volta:  
  
$$\text{View} \;\equiv\; \text{Hybrid} \;\approx_c\; \mathcal{S}$$  
  
**View reale.** Il Receiver genera $pk_b$ con $\text{Gen}(1^n; r_{\text{Gen}})$ e $pk_{1-b}$ in modo oblivious con $\text{Samp}(1^n; r_{\text{Samp}})$; riceve i due cifrati e la sua view è $(r_{\text{Gen}}, r_{\text{Samp}}, c_0, c_1, b, x_b)$. Nota: $c_{1-b}$ cifra il vero $x_{1-b}$.  
  
**Hybrid.** Identica alla view tranne per *come* viene prodotta $pk_{1-b}$: invece di usare $\text{Samp}$, si usa $\text{Gen}(1^n)$ (quindi questa chiave ora *ha* una segreta associata), e poi si usa $\text{pkSim}$ per ricavare una randomness $r_{\text{Samp}}$ finta. Qui sta il trucco di $\text{pkSim}$: come dice la nota a margine, è l'algoritmo che, data una public key $pk$, produce la randomness $r$ tale che $\text{Samp}(r)$ avrebbe prodotto quella stessa $pk$. Serve a "spiegare" la chiave come se fosse stata campionata con $\text{Samp}$, in modo che la view abbia esattamente lo stesso aspetto. Per questo **View ≡ Hybrid sono identiche** (uguaglianza perfetta, non solo computazionale): è una proprietà dello schema (oblivious / explainable sampling) che le chiavi di $\text{Gen}$ e $\text{Samp}$ siano distribuite allo stesso modo e che $\text{pkSim}$ produca randomness con la distribuzione giusta. In Hybrid $c_{1-b}$ cifra ancora il vero $x_{1-b}$.  
  
**Simulatore $\mathcal{S}$.** Identico a Hybrid tranne per l'unica differenza rimasta: $c_{1-b} = \text{Enc}_{pk_{1-b}}(0^n)$, cioè cifra **zeri** invece di $x_{1-b}$. Ed è ovvio che debba fare così:  
[13/06/26 12:20] Zurg: il simulatore $x_{1-b}$ non ce l'ha.  
  
## Perché i due passaggi reggono  
  
Il primo passaggio (View → Hybrid) è *gratis*: sono identiche per definizione di $\text{pkSim}$. È proprio per poter fare il secondo passaggio che si introduce $\text{Gen}$ al posto di $\text{Samp}$: per invocare la **CPA security** serve una public key "vera" del gioco CPA, sotto la quale gli encryption siano indistinguibili.  
  
Il secondo passaggio (Hybrid → $\mathcal{S}$) cambia solo il plaintext sotto $pk_{1-b}$: da $x_{1-b}$ a $0^n$. Questo è indistinguibile per **riduzione alla CPA security**: nella view non compare mai $sk_{1-b}$, quindi un distinguitore tra Hybrid e $\mathcal{S}$ sarebbe, di fatto, un avversario che distingue $\text{Enc}_{pk_{1-b}}(x_{1-b})$ da $\text{Enc}_{pk_{1-b}}(0^n)$ — cioè romperebbe la CPA.  
  
Mettendo insieme $\text{View} \equiv \text{Hybrid} \approx_c \mathcal{S}$ si ottiene $\text{View} \approx_c \mathcal{S}$, che è esattamente la definizione di sicurezza contro il Receiver.  
  
## La morale  
  
Tutto il senso del simulation paradigm qui è: la view reale del Receiver è riproducibile da chi conosce solo $(b, x_b)$. Il messaggio non scelto $x_{1-b}$ è "nascosto" dietro un cifrato $c_{1-b}$ di cui il Receiver non possiede mai la chiave segreta, e questo è ciò che permette al simulatore di rimpiazzarlo con $0^n$ senza che nessuno se ne accorga. La doppia struttura ($\text{Gen}$ vs $\text{Samp}$ + $\text{pkSim}$) serve solo a "trasportare" la dimostrazione dentro il framework della CPA security, dove la sicurezza si sa già dimostrare.  
  
Se vuoi, posso farti vedere la cosa speculare — la *security against the sender*, dove invece si dimostra che il Sender non impara $b$ — oppure un'istanza concreta dello schema (tipo basato su DDH/ElGamal) per vedere cos'è davvero $\text{Samp}$ e $\text{pkSim}$.