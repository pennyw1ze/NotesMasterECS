# IOT security

What is IOT?

> **IOT** **Definition**:
> Cyberphisical ecosystem of interconnected sensors and actuators which enable intelligent decision making;

## Sensors
Monitor the environment. Sensors can measure physical, chemical and other indicators, and collect informations.

## Actuators
Robotic arms, drone's rotator ecc. Actuators are the acting part in the IOT environment, and usually requires low computational power.

The structure of an IOT Device is usually the following:
- There is a processing unit:
- The processing unit is conencted to a:
  - Network interface;
  - Non-volatile memory;
  - Power supply;
- The system in his integrity is connected to:
  - Sensors;
  - Actuators;
  - Device management;

The **number** of IOT devices is continuously increasing and has currently become larger then the number of humans on the planet.
There are several problems in the security of IOT devices. Some of them are:
- Moving from private to **public networks**;
- Low security from **early design**;
- Difficulty in **updating** old systems;

## Stuxnet (yy 2011-12)
Stuxnet was one of the biggest cyberattack involving IOT devices.

- **Goal**: sabotage Iranian Nuclear Program:
	- It caused phisical damage to the infrastructure;
	- Nuclear program slowed down by four years;
- **How**:
	- Make centrifuge operate at wrong speeds;
	- Present acceptable values to monitoring software;
- **Impact**:
	- Attackers were in a position where they could have broken the plant;
	- They preferred slow down the program;
- **Spread**:
	- Fire and forget weapon;
	- The concentration of infections in **Iran** indicates this was the initial target;
	- Used propagation techniques let Stuxnet spread beyond the initial target;
	  The **propagation** followed this steps:
		- **Network** propagation;
		- Removable **Drive** propagation;
		- Step7 project **file** infection;

## MIRAI malware (yy 2016-17)
- **Goal**:
	Turns networked devices running Linux into remotely controlled bots that can be used as part of a **botnet** in large scale network;
- **Targets**:
	Primarily targets online consumer devices such as IP cameras and home routers and were Infected nearly 65k devices in 20 hours;
- **How**:
	Mirai operates in three stages:
	1. Infect the device:
	   Scan for IOT device in network and logs in bruteforcing username and passwd;
	2. Protect itself:
	   Kill other process running on the infected device;
	3. Launch attack:
	   Infected devices launches HTTP flood, SYN flood and other DDoS based attacks;


## CIA of IoT
• Confidentiality and privacy:
	– Data collected from a device should not reveal secure information to unauthorized device;
• Integrity:
	– Data is not tampered;
• Availability:
	– Services can always access the data;



## Source of securtiy threat in IOT devices

### Security issues at sensing layer

• Node capturing
• Malicious code injection
• False data injection
• Replay attacks
• Cryptanalysis and Side channel attacks
• Eavesdropping and interference
• Sleep deprivation attacks
• Booting vulnerabilities

### Security issues at network layer

• Phishing site attacks
• Access attacks
• (D)DOS attacks
• Data transit attacks
• Routing attacks
• Sinkhole attacks
• Wormhole attacks
• Spoofing attacks


### Security issues at middleware layer

• Man-in-the-middle attack
• SQL injection
• Signature wrapping attack
• Cloud malware injection
• Flooding attack in cloud


### Security issues at gateways

• Secure onboarding
• Extra interfaces
• End-to-end encryption
• Firmware updates


### Security issues at application layer

• Data thefts
• Access control attacks
• Service interruption attacks
• Malicious code injection
• Sniffing attacks
• Reprogram attacks


## Remote attestation
There is a challenger that asks for an attestation and a proover that must prove his identity.
There are 2 main approach to solve this problem which are hardware based and software based.

### SWATT: Software Attestation.
Basically, challenger verify the memory content of the prover device, which is known. If an adversary is here, he must've changed the content.
If the adversary redirect the challenge, the overhead will be noticed.
We add a nonce to avoid reply attack, and we omitt part of the memory that will change.
The response time is limited, and the scan of the memory is not linear. The memory of the device is scanned following a PRG that use the nonce as a seed and touch every memory location with probability $O(nlogn)$.

### SCUBA: Secure Code Update By Attestation.
The goal is to securely recover original code in presence of malicious code.
Having nodes that can comunicate with each other and with a safe base station, can dispose of public cryptography and where adversary can compromise sensors but not the base, we can reach the goal.

TOCT TOU: Attacker inject code between the time the code is verified and the code is invoked for the execution.
For this reason we need ICE, which is an integrity check that prove the integrity of the code execution. ICE is run into an untampered environment.

### Hardware based
We need tamper resistance, and to obtain it we need:
- read-only memory;
- Secure-key storage;
- MCU access control;
- A secure boot;
We basically have a safe code zone in our memory in which we can run the attestation procedure and that memory zone cannot be tampered. So the question here is about hardware compromision of that zone since via software is a read-only section.

**Hybrid attestation**
We also have hybrid attestation which involves both hardware and software based approach in part.

### TyTAN
GOAL: designing cheap and secure architecture for low-end embedded systems that provides.
We have secure **Inter Process** **Comunication** and **secure task** loading at runtime.
**Attestation**: Root of Trust computes a cryptographic hash function over the binary code of each created task.
We will also use a secure storage to store data received from task, that are encrypted with a public key related to the task.
We can also achieve a non-interactive attestation with the use of a real time clock and an attestation trigger.
A possible attack to this protocol is the Denial of Service.

### SEDA: Secure Embedded Device Attestation
We want to guarantee integrity for a network of devices. We use a spanning tree, where each node “attests” its children and reports to its parent.
All the device are equipped with a trusted component and talks to their neighbors, and all the devices share the same key.
**Cons**:
Lack of flexibility and fails against physical attacks.

### SANA: Secure Aggregate Network Attestation
There are aggregators in the networks which combines proofs of provers.
The provers require a Trusted Execution Environment.
Physically compromised aggregators cannot forge attestation result, but physically compromised provers can evade detection.
Sana guarantees public verifiability and has a better resiliance to DoS and physical attacks.

### DARPA: Device Attestation Resilient to Physical Attacks
We have a device that monitors other devices. Every device broadcast heartbeat to others.
Then heartbeats are collected and stored.
We can address physical attacks but is not suitable for dynamic networks.

### PADS: Practical Attestation for Highly Dynamic Swarm Topologies
Each node produce his self attestation here. Each device talks to his neighbors, device are mobile and a consensus primitive is used among device to corroborate the attestation result.
They spread news by gossip diffusion algorithm.
The device must have:
-  A Read-Only Memory (ROM);
- A Memory Protection Unit (MPU);
- A secure Real-Time Clock (RTC);

We have only provers and verifier in this protocol, provers requires TEE and the attestation proof contains hash value of the software, shared with other nodes in range.

The dynamic part of the network is implemented when the device share their knowledge about their neighbors: If they have a common neighbors which is considered bad by just one of them, then it will be considered bad by both.

**PROS**:
	○ Suitable for dynamic networks;
	○ Consider device movement during attestation;
	○ Verifier can have the snapshot of the network at run-time;

**CONS**:
	○ Nodes cannot easily join the network;
	○ Adding a new node => reconfiguration of all the devices;

##### Bloom filter
A space efficient probabilistic data structure in which we hash the name of the device and we use the hashed string as index to access an array in which we store bits(1 or 0) depending on known infos.
Of course we can have collisions.
We can compute the approximate numbe of elements in the network by counting the number of bits in the array and dividing by the assigned number of beats for each element.

With this protocol we have security against adversarial comunication since the self attestation is generated in TEE, the only thing that adversary can do is not partecipating in the protocol because he cannot produce a valid attestation.

---
# XR Security


---
# Security and Machine Learning


---
# Future networking


---
