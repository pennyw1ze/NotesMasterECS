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

Since GPS **cannot be implemented directly** (Il GPS assume che il server possa servire _tutti i flussi contemporaneamente_, dividendo la capacità in porzioni infinitamente piccole. Nella realtà, invece, i pacchetti sono **discreti**: un link può trasmettere **un pacchetto alla volta**, non frazioni di pacchetto), packet-based approximations such as WFQ and SFQ are used. SFQ schedules requests based on *start tags* and defines system virtual time as the start tag of the request currently in service. These algorithms are work-conserving and perform well when flows are continuously backlogged.

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

Multi-queue fair queuing (MQFQ) aims to share the aggregated capacity of multiple servers among competing flows. Algorithms such as Min-SFQ(D), SFQ(D), and FSFQ(D) differ primarily in how they define system virtual time $v(t)$ when there are $D$ queues.

Although these algorithms can balance load across multiple queues, they do not address deceptive idleness. In the worst case, when a parallel program has only one active critical thread, up to $D - 1$ threads from another backlogged program may run ahead of it. The resulting unfairness is quantified using the absolute relative lag

$$
\text{lag} = \left| \frac{S_{\text{fair}} - S_{\text{parallel}}}{S_{\text{fair}}} \right|
$$

which leads to

$$
\text{lag} =
\left|
\frac{l_{\max} - l_{nc}}{l_{\max} + l_{nc}}
-
\frac{2 l_{\max}}{D (l_{\max} + l_{nc})}
\right|
$$

As $D \to \infty$ and $l_{nc} \ll l_{\max}$, the lag approaches 1, indicating near-starvation of parallel programs.

---

## 3. Preemptive Multi-Queue Fair Queuing

To address deceptive idleness, the paper proposes **Preemptive Multi-Queue Fair Queuing (P-MQFQ)**. The key idea is to enforce fairness at the program level by considering the aggregated CPU service received by all threads of a program across multiple CPUs.

P-MQFQ assumes a centralized dispatch queue and tracks system virtual time as the maximum start tag of all requests currently in service. Each program also maintains its own virtual time. When a request arrives with a start tag smaller than the system virtual time, it indicates that the program is lagging. In this case, the new request is allowed to **preempt** a currently running request with the largest start tag, ensuring timely compensation.

---

### 3.1 Approximating P-MQFQ on Distributed Queues

Because centralized queues do not scale, P-MQFQ is approximated by augmenting existing schedulers with three operations: **MIGRATE**, **PPREEMPT**, and **SWITCH**. These operations allow schedulers with per-core run queues to approximate global fairness without maintaining a centralized structure.

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
