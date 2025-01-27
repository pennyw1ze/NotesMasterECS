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
Stronger are the timing assumptions that the system can provide, higher is the accuracy of the information that a failure detector can state.
We can classify failure detectors with a table like this one:

![[Pasted image 20250121131058.png]]

###### **Accuracy**:

> **Weak accuracy**: some correct processes are never suspected;
> **Strong accuracy**:  correct processes are never suspected.

###### **Completeness**:

> **Weak completeness**: Eventually every process that crashes is permanently suspected by some correct process;
> **Strong completeness**: Eventually every process that crashes is permanently suspected by every correct process.

### Perfect Failure detectors
In this case we must have a **Synchronous System**, which can handle crash failures. We also need **pp2p links**.
Using its own clock and the bounds of the synchrony model, a process can infer if another process has crashed.

##### Properties
- Strong completeness;
- Strong accuracy.

##### Code
![[Pasted image 20250121205225.png]]

### Eventual perfect failure detector
For the eventual PFD, the requirement for the system is just **partial synchrony**.
We also need **pp2p links**.
There is a **time $T$** after which a process failure can be actually detected.
During the time before $T$ the failure detector can make mistake, so we call the processes that before where called _detected_ as **_suspicious_**.
##### Properties
- Strong completeness;
- Eventual strong accuracy;

![[Pasted image 20250122101741.png]]

A proccess, as said before, can make mistake by labelling suspect a process which is not, but the state can be easily restored, granting eventual strong accuracy.

---
# Leader Election

Algorithm to establish a common leader/coordinator/master between a set of processes under a certain types of assumptions.
### Perfect leader election
We use a perfect failure detector $\implies$ (pp2p links, synchronous system)
##### Properties:
- Eventual election: either there is a leader, or all process are faulty;
- Accuracy: if a process is leader, then all previous leaders have crashed;

##### Code:
![[Pasted image 20250122102728.png]]

### Eventual perfect leader election
If we does not have a perfect failure detector, we can still use an eventual pfd.
##### Properties
- Eventual accuracy: there is a time after which every correct process trusts some correct process;
- Eventual agreement: there is a time after which every 2 correct processes trusts the same correct processes.

##### Code:
![[Pasted image 20250122103724.png]]

---
# Broadcast comunication

In this section we will see how to allow broadcast comunication between processes under different system assumptions.
### Best Effort Broadcast (BEB)
Best effort broadcast can ensure message delivery only if the sender don't crash, otherwise the other processes may disagree on weather or not to deliver a message.
Can be adopted by an asynchronous system, uses pp2p links.
##### Properties
All the properties basically relies on the properties of the pp2p links.
- Validity: if a correct process broadcast $m$, then every correct process delivers $m$;
- No duplication: no message is delivered more than once;
- No creation: if a process delivers $m$ with sender $p$, then $m$ was previously broadcasted by $p$;
##### Code

![[Pasted image 20250122105426.png]]

### Reliable Broadcast (RB)
Basically a BEB with the agreement property. In synchronous systems uses pfd
There are 2 implementations, in synchronous and asynchronos systems.
##### Properties
- Validity;
- No duplication;
- No creation;
- Agreement: if a message $m$ is delivered by some correct process, then $m$ is eventually delivered by every correct processes.

##### Code
![[Pasted image 20250122112831.png]]
In the deliver event, we need to check if the message $m$ has already been delivered, if it was delivered then it is listed in $from[s]$. It may happen that we receive a message already delivered because of the retransmission. If a process crash, all the message previously received by those process are re-broadcasted by every process in order to ensure agreement, so in this case may happen to receive again a message already delivered.
We will have in the best case 1 RB mesage per each BEB message, and in the worst case (N-1 systems crash) we will have N-1 RB message for each BEB message.

#### RB asynchronous systems
Under the assumption of asynchronous systems, we can't rely on PFD, and we will have to retransmit every message that we receive.
The RB asynchronous just keeps the same properties of synchronous RB, but, for each BEB message, it requires N RB messages.
##### Code

![[Pasted image 20250122113506.png]]

### Uniform Reliable Broadcast (URB)
The agreement property introduced by RB becames **_uniform_**.
There are 2 implementations, in synchronous and asynchronos systems.
##### Properties:
- Validity;
- No creation;
- No duplication;
- Uniform agreement: if a message $m$ is delivered by some process (whether **faulty or correct**), then $m$ is eventually delivered by every correct process.

##### Synchronous systems
Uses PFD and BEB.
##### Code

![[Pasted image 20250122114232.png]]
When we broadcast a message, we insert it in pending, when we receives other processes deliveries we add ack to the message delivered and when a message in pending has been acked by all the correct processes, if we did not already delivered it, we deliver it.
##### Asynchronous systems
In asynchronous systems, the code is more or less the same, but we do not have a PFD so we can't check if $correct \subseteq ack[m]$. Assuming that the majority of the process is correct, we can change the function with the following:$$|ack[m]| > N/2$$We just check if the majority of the process have delivered the message.

### Probabilistic broadcast
A probabilistic broadcast has the following system specification:
- Messages are delivered the $99\%$ of the times;
- Not fully **reliable**;
- Implements a tree structure, adopting a **hierarchical logic**;
##### Properties
Ensures the following properties:
- Probabilistic validity: We can say that $\exists\ \epsilon > 0\ s.t.$ when a correct process broadcast a message $m$, $Pr[deliver_{p_i}(m)] \ge 1 - \epsilon$.
- No creation;
- No duplication;
##### Code

![[Pasted image 20250122121427.png]]
The algorithm choose $k$ random other processes and sends them the message $m$ to broadcast it. The gossip procedure spreads the message $m$ even on delivery, but for a number of times equals to $r$ (wich is added when the broadcast starts, and is decreased at each gossip deliver call).

---
# Consensus
The problem: a group of process must agree on a value that has been proposed by one of them.
Basic consensus **specifications**:
- **Termination**: Every correct process eventually decides some value;
- **Validity**: If a process decides a value $v$, then $v$ was proposed by some process;
- **Integrity**: No process decides twice;
- **Agreement**: No 2 correct processes decides different values;
There is an important result here to remark:
###### FLP Impossibility result
No algorithm can guarantee to reach consensus in an **asynchronous system** if we are in presence of crash.
### Floading Consensus
Requires synchronous systems. All processes shares a proposal between each other, then a value is chosen with a **deterministic algorithm**.
##### Code
![[Pasted image 20250122123352.png]]
Basically stores the values received from the other processes, broadcast his proposal via BEB, when he receives a proposal via BEB delivery he stores it in the proposal array and save the sender in the received from array, and when all correct processes sent their proposal and the decision has not been taken, he takes the deterministic decision and broadcast the choosen value. He updates the number of rounds. If he receives a decision from another process, and the process is correct and he has not taken a decision yet, he stores the other process decision and rebroadcast it via BEB.
##### Properties
- Validity and integrity thanks to pp2p links;
- Terminates in at most N iterations;
- Agreement reached thanks to the deterministic function;
##### Performance
- Best case: 1 round of comunications = $2\times N^2$ , $N$ messages for each process (1 broadcast for each process ) to spread the proposal and another one to spread the decision.
- Worst case: N rounds: $N^3$.
### Uniform Consensus (UC)
We have the same properties of the Floading Consensus, except for the agreement which becomes uniform.
##### Additiona property:
- **Uniform agreement**: No 2 processes decides differently (even if they are faulty).

In UC the process decides only if he received a proposal from every correct process, he does not store partial information at every round. We have a proposal set instead of a list, and the $receivedfrom$ array is cleaned at the beginning of every round.
##### Code
![[Pasted image 20250122133639.png]]

##### Performance
Since we choose the value only in the last round, we will have $N$ rounds for sure, which makes the cost equal to $N^3$.

##### Properties
- **Validity** and **Integrity** follow from the properties of the best-effort broadcast;
- **Termination** is ensured because all correct processes reach round N and decide in that round (the strong completeness property of the failure detector implies that no correct process waits indefinitely for a message from a process that has crashed, as the crashed process is eventually removed from correct);
- **Uniform Agreement** holds cause all processes that reach round N have the same set of values in their variable $proposalset$;
---
# Paxos
Basically, consensus over asynchronous systems. We need network to work for enough time. We have 2 basic assumptions:
- **Agents** can fail by stopping, the also operate at arbitrary speed and they may restart;
- Messages can take **arbitrarily long time** to be delivered, can be also be **duplicated** or lost, but they **aren’t corrupted**;
Roles in Paxos protocol:
- **Proposers**: who propose a value;
- **Acceptors**: processes that decides the final value;
- **Learners**: observe passively the process and obtain the final value;
The model with one acceptor is obviously the simpliest one, but we have a SPF, so we must have different processes with this role. To choose, we will use the majority criteria.
Remember that proposer associate to the proposed value a unique ID $n$.
The paxos protocol has 2 main phases:
##### Phase 1:
- A **proposer** choose a new proposal version number $n$ (the id of the proposed value) and sends to the acceptors $(PREPARE,n)$;
- If an **acceptor** receives a prepare request, he **promise** not to accept any more proposal numbered less then $n$, and suggest the value $v'$ of the highest numbered proposal that he has accepted. He sends: $(ACK,n,n',v') or (ACK,n,\bot,\bot)$;
- If the acceptor has already promised for an higher value of $n$, he sens back $(NACK,n')$;
##### Phase 2:
- If a proposer received a majority of $ACK$ for a specific value, he can issue an **accept request** to the majority of all the acceptors: $(ACCEPT,n,v)$, where $n$ is the sequence number (ID) and $v$ is the associated value;
- If the acceptor receive an $ACCEPT$ request, it accept the proposal unless he has already responded to a prepare request with an higher $n$ (ID);
- When acceptor accepts a proposal, sends $(ACCEP T, n, v)$ to all the **learners**;
- Learners that receives $(ACCEP T, n, v)$, sends a (DECIDE, v) to all the other learners;
- All the learners that receive $(DECIDE, v)$, decide $v$.

---
# Ordered comunication

Defines guarantees about the **order of deliveries** inside a group of processes.
The ordering of comunication is important for the **reliability** of the system.
We have 3 types of ordering: **FIFO, Causal and Total.**
### FIFO broadcast
FIFO broadcast is based on the RB.
We just add the FIFO delivery property:
- **FIFO delivery**: If $p_i$ broadcast $m_1$ before $m_2$, then no correct process deliver $m2$ before $m1$.
If the RB is Uniform, it means that all the correct processes delivers the same set of messages.
##### Code

![[Pasted image 20250122183825.png]]
It differs from RB just because we are using a log sequence number ($lsn$) and an array ($next$) in which we keep track of all the sequence number of the messages delivered by a process.

### Causal order broadcast
Is an extension of the happened before relation. It keep track of messages _caused by_ other messages. For example, if a process broadcast $m1$ before it broadcast $m2$, or a process delivers $m1$ and then suddenly broadcast $m2$.
- **Causal delivery**: for any message $m1$ that may have potentially caused $m2$, no process delivers $m2$ unless it has already delivered $m1$.
It uses again RB.
Notice: Causal Order $\implies$ FIFO Order.

##### Vector clock Code

![[Pasted image 20250122185716.png]]

We send the message with the a copy of the current vector clock, and then we send the message and attach the vector clock. If the vector clock attached to the message is smaller than the receiver entry of the vector clock, the receiver will deliver it and update its clock, otherwise he will wait for the previous message to arrive in order to satisfy Causal delivery.

##### Non waiting Code

![[Pasted image 20250122191452.png]]

We have a list of past messages, and each time we broadcast a message, we append to it the list of messages that we previously sent. So, if we have to deliver a message, we check the list of previously sent message and before sending the actual one we check if there is some message that we didn't delivered between the passed ones. Then we can safely deliver the actual message, and happend it to the list of delivered and past.

### Total Order
Does not implies any other kind of ordering, in fact it is orthogonal to the other ordering, meaning that a sequence can be total ordered but not FIFO ordered.
![[Pasted image 20250122192056.png]]
##### Properties
- **Validity:** if a correct process broadcast $m$, then every correct process delivers $m$;
- **Integrity:** no duplication;
- **Agreement:** if a message $m$ is delivered by some correct process, then $m$ is eventually delivered by every correct processes;
- **Order:** imposes constraints on the sequence of messages to be delivered.

**RECAP UA vs NUA**
**UA** establish that if any process delivers $m$ before $m'$ then every process delivers $m'$ only after he delivered $m$.
**NUA** establish that if any **correct** process delivers $m$ before $m'$ then every process delivers $m'$ only after he delivered $m$.
Correct processes always delivers the same set $M$.
Each faulty delivers a set $M_{p_i}$
UA: $M_{p_i} \subseteq M$
NUA: can be $s.t.\ M_{p_i} - M \neq 0$.
#### Categories of T.O.
##### SUTO (Strong Uniform Total Order)
If some process delivers $m$ before $m'$, then a process delivers $m'$ only after $m$.
##### WUTO (Weak Uniform Total Order)
If 2 process $p$ and $q$ delivers the same messages $m$ and $m'$, then $p$ delivers $m$ before $m′$
$\iff$ q delivers $m$ before $m′$.
##### SNUTO (Strong Non Uniform TO)
If some **correct** process delivers $m$ before $m'$, then every **correct** process delivers $m'$ only after $m$.
##### WNUTO (Weak Non Uniform TO)
If 2 **correct** process $p$ and $q$ delivers the same messages $m$ and $m'$, then $p$ delivers $m$ before $m′$ $\iff$ q delivers $m$ before $m′$.

##### Code
![[Pasted image 20250123114612.png]]

---
# Registers
A register is a variable shared between processes and accessible through read and write operations.
The **read** operation reads the value $v$ stored in the memory of the register.
The **write** operation updates the value of the register with the provided new value.
We will assume that:
- $v \in \mathbb{R}^+$ and the initial value of a register is $0$;
- Values are **unique**;
- Process can't invoke multiple operations **at the same time**;
Register **notation**: $(n1,n2)$ where $n1$ is the number of processes that can write and $n2$ is the number of processes that can read the register.
Operations are characterized by an **invocation** and a **return** event.
If an operation is invoked but never returned, it has **failed**.
We say that an operation $o_1$ **precedes** $o_2$ if the return event of $o_1$ happens before the invocation event of $o_2$. Otherwise, the operations are **concurrent**.
Sequential specification:
- **Liveness**: each operation eventually terminates;
- **Safety**: each read operations returns the last value written;
## Regular register
RR is a register (1,N) where the following properties holds:
- **Termination**: if a correct process invokes an operation, then the operation eventually receives confirmation;
- **Validity**: a read operation returns the last value written, or a value written in concurrency with the read operation.
#### Read-one write-all RR
The implementation requires a PFD, pp2p links and BEB.
The idea is that each process store a local copy of the register, so reading the value has cost $O(1)$. Instead to write a value each process must update the other processes register, so it cost $O(n)$.
##### Code
![[Pasted image 20250123150516.png]]
After the write operation has happened, we update the value and send an ack message to the process that updated the value. Since to write we must send a broadcast and wait the ack, we will have a total amount of $2N$ messages.

#### Majority voting
If we can't rely on a PFD, because we may have silent failures, we can rely on a set of processes which could be asked to keep values with timestamp, and when reading the value perform a majority voting.
##### Code
![[Pasted image 20250123153019.png]]
When a process needs to write it will begin to broadcast by sending its value and its timestamp (increased). When we receive a message in the deliver event of the write, I will check if the timestamp received is bigger then the current timestamp of the value, and in this case I update the value and will send the ACK back. When the writer receives at least $N/2$ ACK’s (since we have the assumption of the majority of correct process) will trigger the WriteReturn. When instead we have a read operation, since we don’t have a perfect failure detector I cannot be sure that my value is still correct, I need to consult all the other process in order to obtain a quorum (so to obtain a variable). In the deliver event of the read we do the quorum, in fact the first control is that the r (timestamp of the read ) received is the same of my actual rid e will be inserted in the readlist and when the number of processes in the list is at least the half of the total number of process we will trigger the ReadReturn. In order to perform a Write operation or a Read operation we need at most $2N$ messages.
### Atomic register
Is a RR with an ordering property:
- **Ordering**: if a read return $v_2$ after a read that has returned $v_1$, then $v_1$ cannot be red again by any process.
To implement an atomic register we will:
- Use a $(1,N)$ RR to build a $(1,1)$ atomic register;
- Use $N$ $(1,1)$ atomic register to build a $(1,N)$ atomic register.
##### Code
![[Pasted image 20250123155803.png]]
And for the $(1,N)$ part:
![[Pasted image 20250123160104.png]]
bah what to say, lettese go dancing.

There is also a **Read-Impose Write-All** algorithm that basically, when a read is performed, impose to all the correct process actually running to update their local value.

---
# Software Replication
Is used for fault tolerance purposes, to grant the availability of a service.
If we consider $p$ the **failure probability** of an object $O$, then $1-p$ is the **availability** of $O$.
If we **replicate** an object $O$ for $n$ times, now his availability become $(1-p)^n$.
So the situation is the following:
- A generic process $p_i$ interacts with a set of objects $X$;
- An operation is a pair of invocatoin/response generated by the process/the object;
- After issuing an operation the process is **blocked**;

Let us consider the precedence relation ( < ) and the concurrency relation ( || ).
There are 3 consistency criteria:
- **Linearizability**: If $\exists$ a sequence $S\ s.t.$ for any 2 operations $O_1,O_2\ s.t.\ O_1 < O_2$, we have that $O_1$ appears before $O_2$ in $S$, and the sequence $S$ must be legal i.e. for every object in the sub-sequence of $S$ they have to respect the sequential specification of the object;
- **Sequential consistency**: A history $H$ is sequentially consistent if it admits a **linear extension** in which all the read are legal. The sequential consistency does not respect real time, but logical time (local time of the processes);$$Linearizability\implies Sequential\ Consistency$$
- **Causal consistency**: Let $H$ be an execution history and let $H_i$ be the sub-history of $H$ from which all the read operations not issued by $p_i$ are removed. A history is causally consistent if, for each process $p_i,$ all the read operations of $H_i$ are legal.$$Sequential\ Consistency\implies Causal\ Consistency$$

![[Pasted image 20250124122101.png]]
![[Pasted image 20250124122114.png]]
![[Pasted image 20250124123337.png]]
![[Pasted image 20250124123402.png]]

Linearizability and Sequential consistency compose the **strong consistency**, while causal consistency compose the **weak consistency.**

**Sufficient condition** for linearizability:
- **Atomicity**: Given an invocation of a process $p_i$, if one replicate handles this invocation, then every correct replica also handles the same invocation;
- **Ordering**: Given 2 invocations, if 2 replicas handles both invocations, they handle them in the same order;
2 Types of replication: active and passive.
## Passive replication
The passive replication technique is based on the **primary backup** logic.
- **Primary**: Receives invocations from clients and sends back answares;
- **Backup**: Interacts with the primary. Replaces the primary in case of crash.
In case of an invocation, the primary sends an update to all the backup replicas. After receiving the acks from them, primary sends back the answare.
Linearizability is guaranteed since the order in which the primary receives the invocations defines the unique order of the sequence.
When primary fails, is started a leader election to elect a new primary replica between the backup remaining.
## Active replication
There is no coordinator, and all the replicas have the same role. Each replica acts the same way because is deterministic.
To ensure linearizability, we must use a **Total Order Broadcast** to preserve the atomicity and the ordering of the sequence.
Upon the failure of a replica, in active replication there is no operation needed.

----
# CAP theorem
The CAP theorem states that in a shared data system, in case of failures, we can guarantee always **at most 2** of the 3 following properties:
- **Consistency**: Every request receives the right response;
- **Availability**: Every request eventually receives a response;
- **Partition resiliance**: Servers can be partitioned into multiple groups that cannot comunicate with each other.
Partition resiliance means that we split the replicas and we break the links between them, we make them uncapable of comunicating between each others.
---
# Byzantine
Byzantine are that process which can act **arbitrarily** and **cannot be detected** as crashed or failed, because they are active and can send response. In most of the cases, are systems compromised or being controlled by an attacker.
There is the need of adapting our standards to this kind of failures in order to provide guarantees also under this kind of threats.
## Byzantine tolerant Broadcast
To fight byzantine process we can introduce cryptographic tools. This will allow process to authenticate, as well as to verify the identity of the sender process. We start by Introducing authenticity in the pp2p links, that gains the **Authenticity property**, which establishes that if some correct process q delivers a message m with sender p and p is correct, then m was previously sent to q by p.
### Byzantine Consistent Broadcast
Byzantine consistent broadcast has the following properties:
- **Validity**: if a correct p broadcast $m$, then every correct process eventually delivers $m$;
- **No duplication**;
- **Integrity**: if correct p delivers $m$ with sender q and q is correct, then $m$ was previously sent by q;
- **Consistency**: if some correct p delivers a message $m$ and another correct process delivers a message $m'$, then $m = m'$.
Basically for the consistency property we say that every correct process delivers the same message. Byzantine consistent broadcast implements authenticate pp2p links.
##### Code

![[Pasted image 20250124161338.png]]
Basically we deliver a message only when we received a number of echo grater then $(N+f)/2$.
In a normal quorum we have to wait until we get the majority ($N/2$), but in this case, since we have $f$ byzantine process, we need at least $(N+f)/2$. In this case the number of correct process is at least $(N+f)/2 - f = (N-f)/2$, wich is our majority excluding the byzantine ones ($N-f$ is the number of correct processes).
### Byzantine Reliable Broadcast
Same properties as before, plus:
- **Totality**: if some message $m$ is delivered by a correct process, then every correct process eventually $m$.
When the quorum of a receiving process is reached the process will send a ready message to all the other processes with its id and the message received from the echo. When a deliver event of an echo message arrives the ready array is filled with the message in the position of the sender process. The ready message of the broadcast can be sent even when the number of ready messages delivered is higher than f , this cause in this case at least one process is correct. At the end in order to deliver the broadcast message we need to check if the number of processes from which I received the ready message is at least > 2f and this is made in order to avoid byzantine processes that sends twice a ready message.
##### Code

![[Pasted image 20250124173649.png]]
## Byzantine tolerant Consensus
We would like to obtain the same properties that we had in the previous consensus system model, but this time we will not be able for sure to guarantee that a process either do not choose, or choose the same value as the correct one because byzantine processes may invent values or claim to have proposed different values.
We must restrict the specification to only correct processes.
Thus, we define:
- **Weak validity**: if all process are correct and propose the same value $v$, then $v$ is choosen by all correct processes. If all process are correct and some process decides $v$, then $v$ was proposed by some process;
- **Strong validity**: if all process are correct and propose the same value $v$, then $v$ is choosen by all correct processes. Otherwise, a correct process may only decide a value that was proposed by some correct process or the special value $\square$.
The problem is since this a recursive algorithm we have a large number of messages and it’s very complex so a solution can be to use messages authentication codes:
-  The commander signs and sends his value $(v : 0)$ to every lieutenant.
-  For each i:
	-  If lieutenant i receives a messages $(v : 0)$ from the commander and has not received yet any order then: $Vi = {v}$ and sends $v : 0 : i$ to every lieutenant;
	- If lieutenant i receives a messages $v : 0 : j1 : ... : jk$ and $v$ is not in $Vi$ then: adds $v$ to $Vi$ and if $k < f$ sends $v : 0 : j1 : ... : jk$ to every lieutenant other than $j1 : ... : jk$
- For each i : when lieutenant i receives no more messages, he obeys the order choice($Vi$);
---
