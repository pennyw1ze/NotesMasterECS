# Preemptive Multi-Queue Fair Queuing

## Abstract

The paper identifies a fundamental deficiency in state-of-the-art multicore schedulers: although they enforce fair CPU allocation on a per-core basis, they fail to provide fairness across multiple cores when scheduling parallel programs that use blocking synchronization. Such programs frequently experience *deceptive idleness*, a condition in which threads appear idle due to synchronization but still require CPU resources to make collective progress. As a result, these programs are unfairly penalized, leading to performance degradation and high variability. To address this issue, the authors propose **Preemptive Multi-Queue Fair Queuing (P-MQFQ)**, a scheduling algorithm that extends fair queuing principles to multiple CPUs and compensates for deceptive idleness using controlled preemption. The algorithm is implemented in Linux CFS and Xen, demonstrating significant improvements in fairness, utilization, and performance.

---

## 1. Introduction

Modern operating systems and hypervisors rely on variants of fair queuing (FQ) algorithms to manage CPU resources. While these schedulers work well on single CPUs, they are **not** designed to **fairly allocate CPU** capacity across multiple cores at the program level. This limitation becomes critical for parallel programs that rely on blocking synchronization, where threads must progress collectively. When such programs are colocated with CPU-intensive workloads, they often receive less than their fair share of CPU, even though they would fully utilize the system if run alone.

This unfairness has practical consequences. Public cloud providers **avoid CPU multiplexing** among SMP virtual machines due to concerns about performance unpredictability, leading to poor resource utilization and higher costs. The problem also **threatens** the **scalability** of serverless computing, where many short-lived, interdependent tasks are multiplexed on multicore systems. The paper argues that addressing this issue requires rethinking fair scheduling beyond the per-core abstraction.

---

## 2. Background and Problem Analysis

### 2.1 Start-Time Fair Queuing

Fair queuing algorithms are traditionally modeled using **Generalized Processor Sharing (GPS)**, an idealized system in which all backlogged flows receive service simultaneously in proportion to their weights. For a backlogged flow $i$ in a set $F$, GPS guarantees a service rate

$$
r_i = \frac{\phi_i}{\sum_{j \in F} \phi_j} \cdot r
$$

where $r$ is the server capacity and $\phi_i$ is the flow weight.

Since GPS **cannot be implemented directly** (Il GPS assume che il server possa servire _tutti i flussi contemporaneamente_, dividendo la capacità in porzioni infinitamente piccole. Nella realtà, invece, i pacchetti sono **discreti**: un link può trasmettere **un pacchetto alla volta**, non frazioni di pacchetto), packet-based approximations such as WFQ and SFQ are used. 

PGPS transmits packet by packet in its entirety and only serves one packet at a time. Serves packet in the **increasing orded of their finishing time**.
PGPS approximate bandwith allocation in GPS by serving packets in the increasing order of theri finish time F under GPS. PGPS uses notion of **virtual time** to track packets transmission.

To each packet k of a flow i is assigned a start tag $S_i^k$ and a finish tag $F_i^k$ as:$$S_k^i = max\{F_i^{k-1},v(t)\},\ F_i^k=S_i^k + {L_i^k\over \phi_i}$$where $L_i^k$ is the size of the packet and $\phi_i$ is the weight of the program and v(t) is the local virtual time. Since computation of v(t) is **too expensive** other methods are implemented.
**SQF** serves packet in the **increasing order of start time** instead.


---

### 2.2 Fair-Share CPU Scheduling

Proportional-share scheduling applies fair queuing concepts to CPU scheduling by associating each task or user with a weight and allocating CPU time proportionally. Algorithms such as lottery scheduling, stride scheduling, and CFS rely on virtual time mechanisms derived from packet scheduling. While these approaches work effectively on uniprocessor systems, their extension to multiprocessor systems introduces fundamental challenges, particularly when threads cannot run concurrently on multiple CPUs.

---

### 2.3 Deceptive Idleness

The core problem analyzed in the paper is **deceptive idleness (DI)**. Parallel programs often use blocking synchronization primitives such as mutexes, barriers, and semaphores. When a critical thread (e.g., a lock holder or designated waiter) is preempted, other threads block and appear idle, even though the program as a whole still demands CPU to make progress.

Under SFQ-based schedulers, system virtual time advances independently on each CPU. When an idling thread wakes up, its start tag is aligned with the current virtual time, causing it to lose the CPU share it forfeited while blocked. Because there is no compensation mechanism, repeated blocking leads to systematic loss of CPU allocation for parallel programs.

Load balancing further exacerbates the problem. CPUs with deceptively idling threads appear lightly loaded and attract additional threads, causing sibling threads to stack on the same core. This increases intra-program contention, serialization, and idleness, creating a negative feedback loop.

---

### 2.4 Multi-Queue Fair Queuing

Il _multi-queue fair queuing_ studia come condividere equamente la capacità aggregata di più risorse (code/link o CPU) tra diversi flussi. Alcuni lavori estendono lo _Start-time Fair Queuing_ (SFQ) a più code, ma il problema centrale è come definire il **tempo virtuale di sistema** v(t)v(t)v(t).

- **Min-SFQ(D)** definisce v(t) come il minimo _start tag_ tra tutte le richieste pendenti. Questo favorisce i flussi lenti e in ritardo, ma può causare **starvation** dei flussi veloci in presenza di burst.
    
- **SFQ(D)** definisce v(t) come il massimo _start tag_ delle richieste già inviate ma non completate. Consente di sfruttare pienamente tutte le code, ma **non compensa** i flussi in ritardo.
    
- **FSFQ(D)** combina i due approcci usando più tag per ogni richiesta, cercando di bilanciare utilizzo e compensazione dei flussi lenti.
    

Tuttavia, **nessuno di questi algoritmi risolve il problema della deceptive idleness nei programmi paralleli**. Quando un programma parallelo ha un solo thread critico attivo (ad esempio bloccato in una sezione critica), altri programmi possono occupare le CPU libere, facendo perdere al programma parallelo una quota significativa di CPU. Anche con Min-SFQ(D), il programma parallelo viene trattato come “in ritardo” ma riceve comunque meno CPU rispetto al suo fair share ideale.

In conclusione, sebbene questi algoritmi garantiscano equità tra flussi in teoria, **falliscono nel garantire equità reale ai programmi paralleli** in presenza di sincronizzazione e deceptive idleness, portando a una perdita sistematica di prestazioni.

---

## 3. Preemptive Multi-Queue Fair Queuing

**Goals**:
- Deriving a global dispatch order of threads on multiple CPUs;
- Enforce fairness at program level;
- Allow threads from a lagging program to be timely scheduled on CPU;
- We want time assigned to be proportional to program weights;

A **request** represent the CPU demand from a thread. The request time is either max time a thread can run or actual runtime if thread blocks while running.

**Components**:
- **Centralized request dispatch queue** to schedule requests and track system virtual time;
- System virtual time;
- Program virtual time;

Algorithm:
- System virtual time $v(t)$ is defined as the max start tag of all request in service. Program virtual time $v_i(t)$ is defined as max start tag of request in service that belong to program $i$.
- **Start tag** $S_i^k = max\{F_i^{k-1},v_i(t)\}$ or $v(t)$ if is newly launched/idling;
- On arrival, if the start tag is smaller then $v(t)$ it means that other program/s receive more service than this one, and the request with the highest start time is preempted (guaranteed to be from a different program) and $v(t)$ is updated with the 2nd largest request's start tag;
- The finish tag of the preempted request becomes:
  $F_i^k = S_i^k + {l_r\over \phi_i}$ where lr is the time the request has been running on CPU.

## Idea chiave

P-MQFQ vuole garantire **equità a livello di programma**, non di singolo thread.

👉 Se un programma ha ricevuto **meno CPU del dovuto**, deve poter **recuperare**, anche **preemptando** (interrompendo) un programma che ha usato troppo.


## 1️⃣ Start tag e tempo virtuale (intuizione)

- **Start tag** = “turno” di una richiesta  
    → più è grande, più la richiesta è _avanti_ nel servizio ricevuto
    
- **Tempo virtuale di sistema v(t)** =  
    misura **quanto servizio è già stato dato globalmente**
    

Se una richiesta arriva con:

- start tag **piccolo** → programma _in ritardo_
    
- start tag **grande** → programma _avanti_
    


## 2️⃣ Cosa significa

> _“lo start tag è inferiore al tempo virtuale di sistema”_

Succede questo:

- Il programma **A** invia una nuova richiesta
    
- Il suo start tag < v(t)
    

👉 Interpretazione:

> “Altri programmi hanno già ricevuto più CPU di A”

Quindi **A è penalizzato ingiustamente** (tipico caso di deceptive idleness).


## 3️⃣ Perché avviene la preemption

In P-MQFQ:

🔴 **Non si aspetta** che il programma recuperi lentamente  
🟢 **Si corregge subito l’ingiustizia**

👉 La nuova richiesta:

- **preempta** (interrompe)
    
- la richiesta **in servizio con lo start tag più grande**
    

Perché proprio quella?

- È la richiesta del programma **più avanti**
    
- È quella che ha consumato **più della sua quota**
    


## 4️⃣ Perché la richiesta preemptata è di un altro programma

Questa frase è importante:

> _“appartiene sicuramente a un programma diverso”_

Perché?

- Il tempo virtuale di sistema avanza **solo** grazie a richieste  
    di **altri programmi**
    
- Il programma della nuova richiesta **non può aver causato** v(t) > suo tempo virtuale
    

👉 Quindi la richiesta “colpevole” è sempre di un altro programma.


## 5️⃣ Cosa succede dopo la preemption

Ora abbiamo:

- una richiesta interrotta
    
- una nuova richiesta che prende la CPU
    

Il sistema deve **ricalcolare v(t)**.

🔧 Regola:

> v(t) diventa il **secondo start tag più grande** tra le richieste in servizio

Perché?

- Il più grande è stato rimosso (preemptato)
    
- Il secondo rappresenta il nuovo “livello massimo” di servizio dato
    


## 6️⃣ Finish tag della richiesta preemptata (intuizione)

Quando una richiesta viene interrotta:

- non perde il lavoro fatto
    
- il tempo già eseguito viene **contabilizzato**
    

Il **finish tag**:

- aggiorna quanto servizio ha effettivamente ricevuto
    
- servirà per schedularla correttamente quando tornerà
    

👉 Nessun lavoro viene buttato via, solo rimandato.

---

## Esempio semplice (2 CPU, 2 programmi)

- Programma **P1** → ha bloccato spesso (in ritardo)
    
- Programma **P2** → usa tutta la CPU
    

Arriva una richiesta di **P1**:

- start tag di P1 < v(t)
    
- P-MQFQ dice:
    
    > “P2 ha usato troppo”
    

➡ richiesta di P2 viene preemptata  
➡ P1 prende la CPU **subito**

✔ Equità ristabilita  
✔ Deceptive idleness eliminata



---

### 3.1 Approximating P-MQFQ on Distributed Queues

Because centralized queues do not scale, P-MQFQ is approximated by augmenting existing schedulers with three operations: **MIGRATE**, **PPREEMPT**, and **SWITCH**. These operations allow schedulers with per-core run queues to approximate global fairness without maintaining a centralized structure.

- Con code distribuite: **non esiste un massimo globale**.
    
- Soluzione: si considera la CPU il cui **tempo virtuale locale avanza più velocemente** → il thread attualmente in esecuzione lì ha ricevuto più servizio.
    
- Quindi il thread del time slice scaduto può **migrarsi e preemptere** quel thread su un’altra CPU.

---

### 3.2 Implementation

P-MQFQ is implemented in the Linux Completely Fair Scheduler (CFS), KVM, and Xen’s credit scheduler. The implementation balances fairness improvements with overheads such as thread migration and cache disruption.

---

## 4. Evaluation

Experiments with PARSEC and NPB benchmarks show that P-MQFQ significantly improves CPU allocation fairness for parallel programs with fine-grained synchronization. These programs achieve CPU utilization close to their proportional fair share. Migration overhead and cache miss increases are shown to be limited and generally outweighed by performance gains.

---

## 5. Related Work

The paper reviews packet scheduling, proportional-share CPU scheduling, and multiprocessor scheduling techniques. Existing approaches fail to account for inter-thread dependencies and therefore cannot address deceptive idleness at the program level.

---

## 6. Discussion

The discussion highlights the trade-offs between fairness, scalability, and overhead, emphasizing that deceptive idleness is a fundamental limitation of current multicore schedulers that hinders efficient CPU multiplexing.

---

## 7. Conclusion

The paper concludes that deceptive idleness causes severe unfairness in multicore scheduling for parallel programs. By introducing preemption into multi-queue fair queuing, P-MQFQ restores fairness, improves utilization, and enhances performance, enabling more effective CPU multiplexing.
