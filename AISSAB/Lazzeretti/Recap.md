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

## IOT Authentication

### PUF
Something we own, used for authentication which is uniquely generated.
A PUF is a little “silicon fingerprint.” Tiny manufacturing variations make each chip respond uniquely to a given challenge.
1. **Enrollment**
    - The device (chip) is **physically connected** to a trusted server.
        
    - The server issues a series of random **challenges** and records the corresponding **responses**.
        
    - These CRP (Challenge–Response Pairs) are stored in the server’s database.
        
    - The chip is then embedded in the field device.
        
2. **Authentication**
    - When the device wants to prove its identity, the server sends it one of the previously‐seen challenges.
        
    - The chip computes and sends back its PUF response.
        
    - The server checks that the response matches its stored value.
    
~ChatGPT
Beyond the authentication application, PUFs is a physical device and can be used also as a PRNG and a **random horacle model**, and to store biometric informations.

##### Fuzzy extractor
The fuzzy extractor is an algorithm that tries to clean data from noises by generation of random numbers performed with PUFs.
A fuzzy extractor has the following properties:
- **Robustness**: detection of helper data tampering;
- **Weak reusability**: security of key if adversary knows all helper data;

##### X-lock algorithm
The X-lock algorithm is used to encrypt data on device like sensor nodes or whatever.
The vault can be unlocked with majority voting algorithm. If the majority is reached, the vault is decrypted by means of a pseudo-secret sharing.
We xor vault entries with bits taken from PUF.

### UAVS: Unmanned Aerial Vehicles
For example, drones. In UAVs, the comunications happens wireless.
We have the UAV network which could be:
- Centralized;
- Decentralized:
  - Single backbone;
  - Multiple backbone;
In this devices we can have different types of issues:
1. **Sensor**:
	   - GPS jamming;
	   - False data injection;
    Solutions:
    - Physical isolation;
    - Autonomous navigation;
2. **Hardware**:
	   - Supply chain;
	   - Battery attack;
	Solutions:
	- fine-grained circuit analysis;
	- pre-flight battery check;
3. **Software**:
	   - Operating system;
	   - Tampering captured signal;
	Solutions:
	- Os updates;
	- Data encryption;
4. **Comunication**:
	   - Usual attacks on network (DoS, Reply, ecc...);
	   - Attacks on routing protocols;
	Solutions:
	- Authentication;
	- Encryption;


---
# XR Security

**Augmented Reality** (AR) is an enhanced version of the real physical world that is
achieved through the use of digital visual elements, sound, or other sensory stimuli and
delivered via technology.

**Virtual Reality** (VR) is the use of computer technology to create simulated environment
(virtual reality places) where the user is inside of a three-dimensional experience.

**Mixed Reality** (MR) is a medium consisting of immersive computer-generated
environments in which elements of a physical and virtual environment are combined.

General elements of XR devices:
- Field of view;
- Lens and display;
- Positional tracking;
- Audio and speaker;
- Haptic feedback;

We can track out data like:
1. User **motion tracking**: movement of the user (by means of accelerometer, gyroscope, ecc...);
2. User **interaction tracking**: for example speech and eye-movement recognition;
3. **Environment tracking**: outward facing cameras;

But what are the risks?

### Data privacy
One of the biggest issue is the data privacy.
A lot of data are collected by XR devices, taken by user, sensor or third parties (like other applications)l.
This data collection must be compliant to the GPDR which is the actual collection of rules about data privacy in Europe.
This data are used to render the virtual reality, enhance realism and improve user experience, but are also sold to advertisment network provider, services provider and others.
The devices have many sensors that are “always-on”.
So, it is crucial to address the security and privacy vulnerabilities that could cause attacks
on XR devices.

### Attacks
Now let's talk about common attacks to XR devices and users.

1. **Password stealing**: With XR devices like gogles users might type password on virtual keyboard while being observed by hackers, that can understand the passwd by just recording the hand movements.
   Attackers can understand what letter the user is typing also by obtaining the gyroscope informations about the head rotation of the writer, and in general users head location;
2. **Behavioural authentication**: Such as pointing, grabbing, walking, typing, but these authentication methods are easily manipulated by attackers (for example, the task of ball-throwing for authentication);
3. **Social Engineering Attacks**: Extensive data collection in XR devices creates an opportunity for impersonation and social engineering attacks;
4. **De-anonymization Attacks**: Biometric data can also be stored in XR devices;
and others...

#### Defence mechanism 
- Permission-based model;
- Local differential privacy;
- Actuator’s Noise;
- Machine Learning;
- Multi-Party Computation;
- Trusted Execution Environments;

#### Negative impacts
- Identity-based harms;
- Persuasion, Coercion, and Manipulation;
- Reality Censorship and Information Disorder;

So basically there is need to define new human rights to govern this technology.

---
# Machine Learning
Section divided in 2 categories:
- Securing with ML;
- Adversiarial ML;
## **Securing IOT**
We can adopt Reinforced based authentication and learning based access control.
#### Reinforcement learning based authentication

• Model: Q-learning based authentication;
• Features: received signal strength indicators (RSSIs);
• The state observed by the receiver at time t consists of the false alarm rate and the missed detection rate of the spoofing detection at time t−1;
• The receiver chooses its action at based on the state $S_t$ to maximize its expected sum utility;
• Update utility and acceptance threshold;

#### Learning-based access control
Intrusion Detection Systems.
ML techniques help build lightweight access control protocols to save energy and extend the lifetime of IoT systems.
Access control will monitor:
- Collision rate;
- Packet request rate;
- Waiting time;

## **Adversarial Machine Learning**

For example, is easy to confuse a machine that tries to distinguish image by adding noise to that image. **Fooling image recognition** in real world is considered an AI attack.

Also neural networks are vulnearble to attacks.
The most **delicate phase** of neural network training is the one between the data acquisition and the model training. In this phase, an adversary could inject whatever data and try to create **backdoors** in the model.

### Attacks
Let's see some attacks that could be performed to AI models.
Attacks are classified as **targeted** if their aim is to turn a specific elem (ex. dog) into something else, or just **untargeted** if their aim is the general compromization of the neural network.

1. **Random noise**
	Add random noise to the data, but it must be proportioned to the weight.
	This attack requires the knowledge of the classification function, so a white box approach is easier.
2. **Exploitation of empty regions**
	- Regions of the feature space for which no examples are provided are classified randomly and can be exploited by the attacker (again by adding a tailored noise);
3. **Label poisoning**
	The introduction of corrupted labels aims at modifying the detection region so to ease attacks carried out at test time.
	**Solution**
	- To avoid contamination attack, we must perform and adversary-aware re-training;
4. **Backdoor attack**
	The adversary **poison** the training set. This could be done also without any knowledge about the system.
	**Solution**:
	1. Prevent:
		- Stealthiness at training time is also required;
		- Only limited forms of corruption are possible;
	2. Care:
		 -  Partially retraining the network;
		 - Backdoors often rely on dormant nodes, pruning inactive nodes on benign samples may help removing the backdoor;
	**Challenges**:
	- Trigger invisibility (test time);
	- Trigger robustness;
	**Honeypots**:
	Backdoors can be used also as honeypots to detect adversarial attacks.

---
# Future networking

#### **Part 1: Software Defined Networking (SDN)**

**Software Defined Networking** (SDN) is a revolutionary approach to managing, configuring, and optimizing network operations.
With the advent of cloud computing, IoT, and mobile devices, modern networking requires performance, reliability, and most importantly, flexibility.

SDN addresses these needs by **decoupling** the **control plane** (decision-making logic) from the **data plane** (packet forwarding mechanisms). 
This architecture allows for **centralized**, software-based controllers that program network devices . This centralization enables a global network view, allowing for real-time responsiveness, improved **programmability**, and streamlined network management.

**Challenges**
- The centralized controller can become a single **point of failure**; 
- **Lack** of mature **security standards** exposes the network to various threats, including DoS attacks, unauthorized rule injection, and man-in-the-middle interceptions;
- OpenFlow's limited encryption support in early versions exacerbates privacy concerns;
**Benefit**
- Improved scalability;
- Automation;
- Reduced cost through commodity hardware;

#### **Part 2: SDN Towards Programmability**

While traditional SDN architectures improve control-plane programmability, they often rely on **stateless data planes**. This statelessness limits the ability to perform context-aware processing essential for applications like firewalls, load balancers, and intrusion detection systems. The need for frequent interaction between the controller and data plane introduces latency and scalability issues.

To address this, the field has shifted toward integrating **programmable data planes**. These programmable switches, built with flexible hardware like Intel's Tofino or NVIDIA's Spectrum series, are designed to run custom packet processing logic at line rate. The **P4 programming language** has emerged as a powerful tool to define how packets should be parsed, processed, and forwarded, independent of the underlying hardware.

Programmable data planes mark a significant evolution in network design, enabling hardware-level customization, rapid deployment of new protocols, and high-speed enforcement of policies like QoS and security rules. However, they also introduce new challenges, including resource limitations, debugging complexity, and the need for more skilled network operators.

**Part 3: Programmable Data Planes Applications**

The convergence of programmable data planes and machine learning (ML) has opened a new frontier in intelligent networking. The capability to extract features from traffic flows directly within switches enables real-time ML inference for tasks such as intrusion detection, anomaly detection, and DDoS mitigation.

Projects like **Taurus**, **pForest**, and **Switchtree** demonstrate how decision trees and even neural networks can be embedded into data plane logic. Approaches such as federated learning (FL) and binary neural networks (BNNs) allow for distributed and privacy-preserving ML models that are deployed directly on P4 targets.

One notable example is **CARAVAN**, a system enabling online learning with in-network ML models. CARAVAN utilizes Labeling Agents that generate labels for real-time traffic, facilitating adaptive retraining that addresses concept drift and maintains high detection accuracy. The feasibility of deploying such models is shown through performance comparisons and retraining times across datasets like CIC-IDS2017 and UNSW-IoT.

Nonetheless, the integration of ML in data planes also raises security and robustness concerns. Data drift, adversarial attacks, and limited computational capacity necessitate continual advancements in model optimization, hybrid training strategies, and system-level monitoring.

**Part 4: Open Projects and Emerging Frontiers**

The SDN and programmable networking ecosystems are enriched by a variety of open-source initiatives and research projects. Tools such as **OpenDaylight**, **ONOS**, and **P4.org** provide foundational infrastructure and community support for experimentation and deployment.

Additionally, emerging research explores **neurosymbolic AI**, a fusion of symbolic logic and neural networks, which aims to bring explainability and robustness to network security tasks. This approach enhances generalization and transparency, particularly useful in identifying zero-day attacks and generating structured knowledge from unstructured cybersecurity events.

Recent projects like **ShieldGPT** and **Mobile-LLaMA** illustrate the growing role of Large Language Models (LLMs) in network security. These models assist in tasks ranging from network traffic labeling to configuration management. Their integration, however, raises questions about trust, explainability, and automation in critical network infrastructure.

**Conclusion**

Software Defined Networking and programmable data planes are at the forefront of a major transformation in network architecture. By enabling deeper programmability, integrating intelligent mechanisms, and fostering open collaboration, these technologies promise to build networks that are not only more agile and scalable but also more secure and intelligent. As we move forward, the synergy between SDN, ML, and emerging AI paradigms like neuro-symbolic reasoning and LLMs will be key to addressing the complex demands of future digital infrastructures.

~Assay by ChatGPT

---
