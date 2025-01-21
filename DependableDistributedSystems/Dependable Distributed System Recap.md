# Intro

#definition 
> Definition of distributed system

Trying to define a distributed system:
- Set of entities;
- Comunication, coordination, resource sharing;
- Common goal;
- Appear as a single computing system;

#definition 
> Definition of dependability

**Dependability** is the ability of a system to deliver a service that can 
justifiably be trusted.

### Failures
There exists different kind of failures:
- **Crash**: system stop working;
- **Omission**: not sending a message that the system is supposed to send;
- **Eavesdropping**: loose informations;
- **Arbitrary**: may behave correctly but send fake informations;

---
# Synchrony

### Synchronous systems

#definition 
> Definition of **Synchronous systems**

There is always a **know upper bound** on a time taken by a process to execute a basic step;

The following definitions follows the previous one:

- **Synchronous comunications**: there is a known upper bound on the time taken by a message to reach a destination;

- **Synchronous physical clock**: there is a known upper bound on drift of a local clock wrt real time; (wrt = with respect to)

What does having a synchronous system implies?
If we have a synchronous system, we can always provide a perfect failure detector, we can measure transit delay, we can coordinate processes basing on time. A major problem is the coverage of synchrony assumption.

### Asynchronous Systems

#definition 
> **Asynchronous systems**

Assuming an asynchronous distributed system means to not make any timing assumption about processes and links.

- Even without access to physical clocks, it is still possible to measure the passage of time based on the transmission and delivery of messages;

- Time measured in this way is called logical time;

### Partial (eventual) synchrony

#definition 
> Partial synchrony
 
There is an unknown time t after which the system becomes synchronous; 

**Properties**:
- Is not true that the system starts asynchronous and then after some (may be long) time it becomes synchronous;
- We expect that the period of synchrony is long enough to grant that our distributed algorithm will terminate;
---
# Link abstractions

### Stubborn Link
>The sender must take care of the retransmissions if it wants to be sure that a message m is delivered at its destination.

Properties:
- **Stubborn delivery**: if a correct process p sends a message m once to a correct process q, then q delivers m an infinite number of times;

- **No creation**: if some process q delivers a message m with sender p, then m was previously sent to q by process p.

### Fairloss Link
>The specification does not guarantee that the sender can stop the retransmission of each message.

Properties:
- **Fairloss**: if a correct process p infinitely often sends a message m to a correct process q, then q delivers m an infinite number of times;

- **Finite duplication**: if a correct process p delivers a message m to the process q, then m cannot be delivered an infinite number of times from q;

- **No creation**: same as before ^.

### Perfect Point to Point Link
>Each message may be delivered more than once.

Properties:
- **Reliable delivery**: if a correct process p sends a message m to a correct process q, then q eventually delivers m;

- **No duplication**: same as before ^;

- **No creation**: same as before ^.

---
# Time

> In a distributed system in presence of network delay, processing delay, etc... it is impossible to realize a common clock shared among every process.

Instead of using a unique synchronized clock, each process uses it's own local machine's clock, and approximate the time with an approximation function:
$$C_i(t) = \alpha H_i(t) + \beta$$
where:
- $H_i(t)$ is the time of the local machine at time t;
- $C_i(t)$ is the approximated time;
- $\alpha, \beta$ are parameters based on previous time output;
Then we can use $C_i$ as a timestamp for events produced by $p_i$.
We want to bind the granularity of time $T_{resolution}$ with a delta. So we say:
$$T_{resolution} < \Delta T$$
where $\Delta T$ is the time between 2 events.

#definition 
> **UTC (Universal Time Coordinated)** is a standard that provides accurate time coordinates for each country; 

##### External synchronization
> All the process in the distributed system chooses an external reference and synchronize themselves on it

Let $D$ be the synchronization bound and $S$ the UTC authority. Then we have:$$| S(t) - C_i(t)| < D,\ \ \forall i = 1,2,...,N;$$
##### Internal synchronization
 >All the processes synchronize their clocks Ci between them
 
 Let $D$ be the synchronization bound and $C_i,\ C_j$ respective clocks for processes $p_i,\ p_j$. Then we have: $$|C_i(t) - C_j(t) < D,\ \ \forall j = 1,2,...,N;$$
 >**NOTE:**
 >A set of process P externally synchronized with bound $D$ is also internally synchronized with bound $2D$.
 
 #definition 
 > An hardware clock $H$ is correct if is drift rate is within a limit of $\rho > 0$;

 $$1-\rho \le \ dC/dt \ \le 1 + \rho$$
![[Pasted image 20250120154434.png]]

Software clocks have to be **monotone**: $t' > t \implies C(t') > C(t)$.
This property can be guaranteed by choosing $\alpha$ and $\beta$ carefully.

### Christian's algorithm
Algorithm for external synchronization, based on using a time server $S$, receiving a signal from an $UTS$ and then computing the time in function of the $RTT$, the time that the message take to arrive at the destination and then go back to the sender.
Algorithm:
A process p sends a request to a server, server answer with the actual time, and the process computes the time by taking the time sent back by the server and subtracting $RTT/2$.

The accuracy of the Christian's algorithm is $\pm (RTT/2 - min)$.

### Berkeley's algorithm
Internal synchronization algorithm based on master-slave structure.
Steps:
1) The master process $p_m$ sends a message with a timestamp $t1$ (local clock value) to each process of the system ($p_m$ included);
2) When a process $p_i$ receives a message from the master, it sends back a reply with its timestamp $t2$ (local clock value);
3) When the master receives the reply message it reads the local clock ($t3$) and compute the difference between the clocks $\Delta=(t1 + t3 )/2 – t2$ for each $p_i$, computes the average of all the differences and applies the correction to all the processes. If the correction is positive the process will increase the local value of the clock, if the correction is negative the process will **slow down** the local clock (it means hiding interrupts that updates the clock).

The accuracy of the protocol depends on the $RTT$ which is not taken into account by the master.

### NTP (Network Time Protocol)
Is a de facto standard for external clock synchronization of ds on internet.
Is a scalable protocol that uses a UTC source connected to primary servers, and then establish a hierarchy of connection between servers that relies on the time provided by the server in the upper level, since arriving to the UTC source.

![[Pasted image 20250121105419.png]]

Based on remote procedure like Christian's algorithm.
The NTP hierarchy is reconfigurable in presence of faults.

### Happened before relation
Two events e and e’ are related by happened-before relation (e → e’) if:
- $pi\ |\ e\ →_i\ e’$;
- $m\ |\ e=send(m)$ and $e’=deliver(m)$;
- $e, e’, e’’ |\ (e → e’’)\ \cap\ (e’’ → e’)$ (happened-before relation is transitive).

Notice that the happened before relation imposes only partial order over events of the execution history.

---
# Logical clock

Is a software counter that monotonically increase its value.
Can be used to timestamp event, and establish a partial order.
There are different types of logical clock, for example:

### Scalar clock
Each process has a scalar clock that is incremented when a message is sent and attached to the message, and updated when a message is received with the timestamp of the received message.

Scalar logical clock can guarantee the following property
- If $e → e’$ then $ts_e < ts_{e’}$;
But it is not possible to guarantee
- If $ts_e < ts_{e’}$ then $e → e’$;

### Vector clock
Each process $p_i$ initialize a vector $V_i$ of N entries.
When $p_i$ sends a message, the entry $V_i[i]$ is incremented, and $V_i$ is attached to the message as a timestamp.
When $p_i$ receives a message his logical clock is updated like this:$$V_i[j] = max(ts[j], V_i[j]), \forall\ j = [1,N]$$
Then $p_i$ generates a receive event and his entry is increased.

##### General properties:
- Scalar clocks: $e → e’\ \implies\ L(e) < L(e’)$;
- Vector clocks: $e → e’\ \iff\ L(e) < L(e’)$
--- 
# Distributed mutual exclusion

Mutual exclusion abstraction:
- At most one process is  in the **critical section**;
- **No deadlock**;
- **No starvation**.

There are different types of approach to this problem.
### Logical clock based approach
#### Lamport's algotithm
How it works:
- Request to access the CS:
	- $p_i$ sends a request message, attaching $ck$, to all the other processes;
	- $p_i$ adds its request to $Q$.
- Request reception from a process $p_j$:
	- $p_i$ puts $p_j$ request (including the timestamp) in its queue;
	- $p_i$ sends back an ack to $p_j$.
Before entering the CS, a process must check if:
1. $p_i$ has, in its queue, a request with timestamp t;
2. t is the small timestamp in the queue;
3. $p_i$ has already received an ack with timestamp t’ from any other process and $t’>t$;
On the release, the process sends a release message.
When a process receive a release message, the sender is removed from Q.
The safety is respected since the process never access CS in the same time,
Fairness satisfied, since order of requests is respected due to Q (concurrent requests are exception)
Needs $3(N-1)$ messages for the CS access.

### Coordinator based approach
There exist a special process (i.e., a coordinator) that collects requests and grant
permission to enter into the critical section
Performance:
- entering the CS always requires 2 messages (i.e., REQ and GRANT) taking one RTT;
- releasing the CS only requires 1 message;
	- such message represent the delay between two different accesses to the CS;
Pros:
- Lightweight protocol;
Cons:
- Scalability issues;
- Coordinator is a SPoF( Single Point of Failure).

### Token based approach
A process interested to the CS can access it only when it receives a **token**.
The token is unique and it is exchanged between processes.
To guarantee fairness, we can exploit a structured **logical topology** (i.e., a ring) for exchanging messages related to the mutual exclusion protocol.
Performance:
- The algorithm continuously consume communication resources (even if no one is interested to the CS);
- The delay experienced by every process between the request and the grant varies between 0 (it just receives the token) and N messages (it just forwarded the token);

### Quorum based approach
To enter the CS every process waits to get the acknowledgement only by a subset of processes large enough to guarantee conflicts.
Each process has associated a voting set that satisfies the following properties:
- $p_i\ \in\ V_i$;
- $\forall\ i, j, V_i \cap V_j \neq ∅$ (i.e., there is at least one common member for each pair of voting sets);
- $|Vi| = K$ (voting sets have all the same size for fairness – same load principle);
- each $p_j$ is contained in M voting sets (same responsibility principle).
For **Maekawa**, the right parameters are:
- $K = \sqrt N$;
- $M = K$;
- $|V_i| = 2 \sqrt N$.
**Maekawa** algorithm is not deadlock free!
---
# Failure detector
A failure detector is an oracle providing information about the **failure state** of a process.
Stronger are the timing assumptions that the system can providing, higher is the accuracy of the information that a failure detector can state.
We can classify failure detectors with a table like this one:

![[Pasted image 20250121131058.png]]

###### **Accuracy**:

> **Weak accuracy**: some correct processes are never suspected;
> **Strong accuracy**:  correct processes are never suspected.

###### **Completeness**:

> **Weak completeness**: Eventually every process that crashes is permanently suspected by some correct process;
> **Strong completeness**: Eventually every process that crashes is permanently suspected by every correct process.

### Perfect Failure detectors
In this case we must have a **Synchronous System**, which can handle crash failures.
Using its own clock and the bounds of the synchrony model, a process can infer if another process has crashed.

##### Properties
- Strong completeness;
- Strong accuracy.

##### Code
![[Pasted image 20250121205225.png]]
