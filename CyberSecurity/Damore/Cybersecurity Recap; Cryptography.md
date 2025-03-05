# Intro
**Cryptography** deals with secrecy of informations;

#definition
> **Perfect cipher**: Given a ciphertext C the probability that exists k2 such that $D_{k2}(C) = P$ for any plaintext $P$ is equal to the apriori probability that $P$ is the plaintext.
> Speaking about probability, we have:
> 	$Pr[plaintext = P | ciphertext = C] = Pr[plaintext = P]$

Note please that **One Time Pad** is a perfect cipher.
One time pad algorithm is an encryption algorithm that, given a plaintext $P$ and a key $K$, computes the xor operations between $P$ and $K$ and returns $P \oplus K$.
To decrypt the ciphertext, the algorithm just needs the Key $K$ used to encrypt the plaintext.

#theorem
> **Shannon's theorem**: a cipher cannot be perfect if the size of the key space is less then the size of its message space.

**Attack models**:
- **Known plaintext**: The adversary knows both plaintext and ciphertext;
- **Choosen plaintext**: The adversary can choose the plaintext;
- **Physical access**: The adversary can phisically access the device and monitor the energy spent for computation and do some calculus to achive usefull informations.

**Cryptography vs Security**:
While cryptography provides robust tools for protecting data, security is a broader discipline that encompasses policies, processes, systems, and human factors. Effective security requires a holistic approach that includes, but is not limited to, cryptography. There are a lot of other factors (human errors, social engeneering, implementations errors, ecc...)

# Symmetric Cryptography
The objective of this subjet is trying to simulate the One Time Pad algorithm, which is an icon for security, but trying to generate keys in different ways, for example via:
- **Stream Ciphers**;
- **Block Ciphers**;
## Stream Ciphers
Algorithms that, given a key used as a seed, try to generate a "stream" of keys in a deterministic way, and uses the generated key to encrypt messages by xoring them to the plaintext just like One Time Pad.
Some of the most important stream ciphers algorithms are:
##### A5/1
##### RC-4
Ronald Rinvest, MIT, 1947, easy to program and fast. His principles where, given a key, generate an apparently random permutation, that repeats eventually after a big number of iterations. Now the algorithm is **broken** because the keys where **too short**.

## Block Ciphers
Algorithms that uses a key to encrypt blocks  that have a fixed, small size.
Some of the most important are:
##### Des, 3Des:
Data encryption standard, 56 bits key, the algorithm is actually broken because of the small size of his keys.
3 Des is just the Des algorithm repeated 3 times, in order to force the attacker to bruteforce 3 times the size of the key, but in the end is much slower then other algorithms and is not used.
##### RC-2
Designed for exporting block ciphers by IBM, vulnerable to attack by using $2^{34}$ choosen plaintext
##### IDEA
1991, 128 bit keys, is a strong algoritghm, only weaker variants has been broken;
##### Blowfish
Can use keys of different lenght from 32 to 448 bits. Is still considered a strong algorithm.
### AES
AES (Advanced Encryption Standard) is the actual strongest algorithm for symmetric encryption.
It's been introduced in 2001 in the US.
Is a symmetric block cipher algorithm.

> Uses **keys** of variable **size** between 128, 192 or 256 bits.

It's resistant to all known attacks, it's quick and compact, and it's relativly simple.

>The **lenght** of his **blocks** is 128 bits.

The algorithm starts by arranging the 128 in a 4-by-4 matrix of bytes
The key is arrangend in a 4-by-n/32 size matrix of bytes, where n is the size of the key ( so if the key is 128 bit, the matrix is 4-by-4, 192 4-by-5, ecc... ).

The AES algorithm is divided into different phases:
1) Initially the key is **expanded**: the expanding procedure consists in generating a different key each round. We also need a 4x4 matrix of values over $GF(2^8)$;
2) Then there is an Add Round Key procedure;
3) Then happens to be 9 rounds in which each round apply the following procedures:
	- **Substitution**: The substitution phase operate on every byte, and it's a **non linear operation** that takes every byte of the maxtrix and performs the following operation:
			$A_{i,j}$ <- $A_{i,j}^{-1}$
	- **ShiftRows**: This operation shifts the elements of each row of the matrix to the right for a number of positions equals to the index of the actual row (ex: row 1: shift $A_{1,1} -> A_{1,2}$);
	- **MixColumns**: Considering every column as  a polynomial over $GF(2^8)$, multiply with an invertible polynomial and shift the columns according to this;
	- **AddRoundKey**: The phase in which the key is xored with the block. Then the key is expanded again.
4) At least, there is a final round as mentioned before, that does not include the MixColumns phase.

Breaking 1 or 2 round of AES is easy, but it is not know how to break 5 rounds.
Breaking 10 rounds of AES nowadays in less then a year is considered impossible nowadays.

### Modes of operations

First of all, let's remark a definition:

#definition 
> Stream ciphers can be **synchronous** or **asynchronous**. With **synchronous stream ciphers**, the bits of the key stream do not depend on the ciphertext bits. With **asynchronous stream ciphers** the key stream can be inferred (provided one knows the secret key) from previous bits of the cipher stream.

There exists several ways in which a block cipher algorithm can operate. The most significant are:

##### ECB (Electronic Code Book)

![[Pasted image 20241227155312.png]]

##### CBC (Cipher Block Chaining)

![[Pasted image 20241227155818.png]]
									encryption
![[Pasted image 20241227161049.png]]
									 decryption
- Random seed is better;
- If an error changes $C_{i-1}$ then $M_i$ changes in a predictable way;
- It's an asynchronous stream cipher;
- Errors in a ciphertext block propagates;
- **No parallel** implementation known for **encryption**;
- Decryption can be **parallelized**;
- Standard in a lot of systems;
- Messsage must be padded to a multiple of the cipher block size, or otherwise can be used a technique called ciphertext stealing.

#definition 
> **Ciphertext stealing** is a tecnique used for encrypting plaintext using a block cipher, without padding the message to a multiple of the block size, so the ciphertext is the same size as the plaintext.

![[Pasted image 20241227163131.png]]
	example of ciphertext stealing

##### CFB (Cipher FeedBack)

![[Pasted image 20241227163259.png]]
![[Pasted image 20241227163335.png]]

- Asynchronous stream cipher;
- Changes in plaintext propagates in ciphertext;
- **Encryption** can't be **parallelized**, but **decryption** can be parallelized;
- CFB has 2 advantages over CBC mode: the block cipher is only ever used in the encrypting direction, and the message does not need to be padded to a multiple of the cipher block size.

##### Output FeedBack (OFB)

![[Pasted image 20241227163833.png]]
	![[Pasted image 20241227163849.png]]

- This mode just generates keystream blocks that are xored with plaintext;
- A bit flip in the plaintext correspond to a bit filp in the ciphertext;
- Encryption and decryption are exactly the same (xor operations);
- Is a synchronous stream cipher;
- No parallel implementation known, but it can be preprocessed;

##### CTR (Counter Mode)

![[Pasted image 20241227164607.png]]

- Blocks can be encrypted in parallel;
- Consists in just, starting from a seed, compute the sequence of that seed and using it as an input to our encryption function;
- Works like a stream cipher;
- In this case we do not need to decrypt from the first block like OFB;

#### General Mode of Operations informations 
Most of the modes that we've seen so far, requries an initialization vector (IV). 
Most of the time there is no need for the IV to be secret.
Sometimes reuse an IV can **leak informations** (CBC, CFB).
Never **reuse** the same IV with the same Key.
In CBC mode the IV must be **unpredictable** at encryption time. For OFB and CTR reuse an IV completely destroys security.
Ciphers can also be **iterated** multiple times:
a common way to use encryption algorithm is applying the **EEE** or **EDE** strategy:
($E_{k_1}(E_{k_2}(E_{k_1}(m))))$ or ($E_{k_1}(D_{k_2}(E_{k_1}(m))))$.
Notice that sometimes only 2 keys are used for 3 operations.

# Authentication

The goal of the authentication procedures is to ensure the integrity of the message, even in presence of an active adversary.
The authentication is ortogonal to secrecy, so often systems requires both.

### MAC (Message Authentication Code)

Security requirement: Adversary can't construct a valid pair $(m,MAC_k(m))$, even after seeing a lot of other pairs of messages.
We describe MAC used in **CBC** Mode encryption.
- Uses a block cipher;
- Slow.
and MAC base on **cryptographic hashing functions**:
- Fast;
- No restriction on export.
Seed can be transmitted in clear.
![[Pasted image 20241228111022.png]]

In this case M1, M2, ..., Mn is the message divided into n sections.
The message is then xored with the seed ( starting by a series of zeros ) and inserted into a pseudo 
random function ($E_k$).

> **CLAIM**: If $E_k$ is a pseudo random function, then the fixed lenght CBC MAC is resistent to forgery.

CBC MAC is secure for fixed lenght message, but insecure for variable-lenght messages.
In hashing function there may appear collisions, for example if we have N messages and n tags, there will be at least $N/n$ collisions.

##### Collision resistence

#definition 
> An hashing function $h:D -> R$ is **weak collision resistant** if, given $x$, it's hard to find $x' \neq x$ s.t. $h(x) = h(x')$.

> An hashing function $h:D -> R$ is **strong collision resistant** if, it's hard to find a pair $x,x', x \neq x'$ s.t. $h(x) = h(x')$.

The hashing functions are used to map large domains to smaller domains.
The goal here is to combine in the MAC hashing function the key and the message in order to obtain a cryptographical hashing function.

A possibile attack against the hashing function is called **birthday attack** and works under the principle of the birthday paradox.
The principle of the **birthday paradox** says that if we catch a group of 23 people, the probability that between those 23 people 2 of them has the same birthday is 50%.

To combine the use of hashing function with the key, we can use the MAC procedure as follows:
- $MAC_k(m) = h(k||m||k)$: this works and it's really similar to HMAC;

#definition 
##### Cryptographical hashing function

> Cryptographical hashing functions are hashing functions that are strongly collision resistant, and hard to invert.

##### HMAC

The HMAC computes the following calculus:

>HMAC procedure: $h(K \oplus opad || h( K \oplus ipad || m))$, where:
- $opad$ is the outer padding function;
- $ipad$ is the inner padding function;
- $K$ is the key used to make the hashing function cryptographically secure;
- $m$ is the message.
Obviously, HMAC can be forged if $h$ function is broken.

### Merkle-Damgard construction

![[Pasted image 20241228130807.png]]

- The **seed** is usually a constant;
- Typically, padding is used to ensure a multiple of n;
- If the basic H function is collision resistant, so it is the extension;
- Block lenght is usually 512 bits;
- Output lenght should be at least 160 bits to prevent birthday attack;
### Cryptographic hashing functions

- MD family, MD4 and MD5, which are actually broken;
- SHA-0, SHA-1;
- RIPE-MD;
- SHA-2;
- SHA-3 (256,512, ecc...).
### SHA-1
![[Pasted image 20241228132823.png]]
Poi dopo sha mi sono rotto

# Public Key Encryption

### RSA 
RSA is based on the principle of the Galua's Field, The Eulers Theorem and the Little Farmat Theorem.

- Variable key lenght: 4096 bit is the most common;
- Slow in encryption, not used in practice (just used to encrypt for example keys);
- Message lenght sould be less then N so that $m \bmod n = m$;
- $|m^e| < |n|$, otherwise we would send the message in plaintext directly (just need to compute e-th square operation to find out m);
##### Main attack perfomed on RSA

- N factorization;
- Message easy to decrypt (0,1) or too short ( |m|  =<  N);
- Chinese remainder theorem;
- If message space is small, then adversary can compute all possible messages in the space (easy bruteforce);
- If 2 users have the same N, but different d and e, is easy to compute the other user's key;
- Choosen ciphertext attacks:
	 - adversary knows $c = M^e \bmod n$;
	  - adversary computes $c' = cX^e \bmod n$ and asks oracle to decrypt it;
	  - adversary computes $(c')^d  = c^d(x^e)^d = MX \bmod n$ so we found our message.
- Implementation attack based on the time required to encrypt the message or to decrypt it, or based on the energies required to make does computations.

## PKCS

Is the public key encryption standard. Consists in a set of standard published by RSA security groups that allow a user to safely implement RSA encryption.
For example, the first standard requires to construct the message in a particular way, by padding it with some bites (some of them are random, in order to make 2 messages sent from the same user seems different also if they are the same).

### OAEP
Optimal Asymmetric Encryption
![[Pasted image 20250103142537.png]]

--------------------------------------------------------------------------
according to RFC 8017 two encryption schemes are
expected to be supported

- RSAES-OAEP (required, to be used with new applications)

- RSAES-PKCS1-v1_5 (for compatibility with existing applications)


PKCS#1 v1.5: Uses a simpler padding mechanism, but has
known vulnerabilities

PKCS#1 v2.2 (RSA-OAEP): A more modern padding
scheme that addresses the shortcomings of v1.5

# Signature 
### Forgery

#definition 
> Forgery is the ability of the adversary to create a valid pair $(m,MAC_k(m))$, or any other validation of the signature that is accepted by a verification algorithm. M must be a message not signed in the past by the legitimate signer.

Different types of forgery:
- Existential forgery (random m);
- Selective forgery (choosen m);
- Universal forgery (for any given m);

### General signature schemes

- RSA;
- El Gamal;
- DSS;

##### RSA
Encrypt hashed message using RSA encryption system.

How does RSA authentication works with PKCS v1.5:
- Signature: code hash of m using private key;
- Construct a composed string:
	$0 || 1 || 8$ bytes $FF$ based $16$$|| 0 ||$ specification of used hash function $|| hash(M)$.
	- Notice that 8 bytes FF based 16 are randomly generated, in order to differenciate same messages encrypted with the same key.
Then it's just a matter of encrypting the message with RSA raising it to the public exponent, but we already know how to do this.
	
##### El Gamal
- Pick a prime $p$ of length 1024 bits such that DL in $Z_p*$ is hard;
- Let $g$ be a generator of $Z_p*$;
- Pick $x$ in $[2, p-2]$ at random;
- Compute $y = gx \bmod p$;
- Public key: $(p, g, y)$;
- Private key: $x$.

It relies on the discrete logarithm problem for security, and is similar to El Gamal encryption algorithm.
##### DSS
DIgital Signature Standard
Uses SHA hash function and DSA as signature scheme.
DSA is inspired by El Gamal.

So how we construct the signature ?

Private key: s secret and random, less then q-1;
Public key: p, q, $alpha$, $y = (alpha)^s \bmod p$
where p is a large prime, q is multiple of p-1, $alpha$ is choosen to be so that $(alpha)^q \equiv 1 \bmod p$.

##### Signature 
Signature of message M:
$Signature<Part1,Part2>$ where:
- Choose a random secret k s.t. $1 \le k \le q-1$.
- $Part1: ((alpha)^k\bmod p)\bmod q$;
- $Part2: (SHA(M) + s(Part1))k^{-1}\bmod q$.
##### Verification procedure
compute:
- $e1 = SHA(M)(Part2)^{-1}\bmod q$;
- $e2  = (Part1)(Part2)^{-1}\bmod q$;
- Accept signature if $((alpha)^{e1}y^{e2}\bmod p)\bmod q = Part1$.


# Authentication

Authentication with the use of third parties.

#definition 
> **Closed world assumption**: everything that is not prooved to be true, is false;


In public key authentication is foundamental to provide the right public key.
If a user is authenticated by another user with a different public key, then this is a fault in security
It must be used a Public Key infrastructure.

### Needham-Schroeder protocol

The Needham-Schroeder protocol to authenticate 2 users that wants to comunicate each other is based on a third party thrusted authority that guarantees the authenticity of public keys.
$K_{PX}$: public key of $X$, $Sig_C$ digital signature of $C$ (trusted authority
guarantees public keys)

**Mutual authentication** protocol:

- A to C: <this is A, want to talk to B>;

- C to A: $<B, K_{PB}, Sig_C(K_{PB},B)>$;

- A checks digital signature of $C$, generates nonce $N$ and sends to $B$:
  $K_{PB}(N, A)$;

- $B$ decrypt (now wants to check A’s identity) and sends to C: $<B, A>$;

- C to B: $<A, K_{PA}, Sig_C(K_{PA}, A) >$;

- B checks C’s digital signature, retrieves $K_{PA}$, generates nonce N’ and
  sends to A: $K_{PA}(B, N, N’)$;

- A decrypts, checks N, and sends to B: $K_{PB}(N’)$.

### X.509 Standard

We need a directory of public keys signed by the certification authority.
We define different protocols for authenticate:

##### One-way authentication
   
Timestamp tA
Session key KAB
B's public key PB
certA: certificate of A’s public key, signed by
certification authority

A 🡪 B : certA, DA, Sig_A(DA)
DA = <tA, B, PB(KAB)>

##### Two-way authentication
Mutual authentication:

A 🡪 B

certA, DA, Sig_A(DA) [DA = <tA, N, B, PB(k)>]

(how does A know PB?)

B 🡪 A

certB, DB, Sig_B(DB) [DB = <tB, N’, A, N, PA(k’)>]

(how does B know PA?)

tA, tB = timestamps, to prevent delayed delivery of messages; k, k’
session keys proposed by A and B; use of nonces avoids replay
attacks

criticism: in DA there is no identity of A - refer to the modified N.S.
protocol

##### Three-way authentication
three messages (A 🡪 B, B 🡪 A, A 🡪 B):

A 🡪 B: <certA, DA, Sig_A(DA)> [DA = <0, N, B, PB(k)>]

B 🡪 A: <certB, DB, Sig_B(DB)> [DB= <0, N, A, N’, PA(k)>]

A 🡪 B: <B, Sig_A(N, N’, B)>

Note: step 3 requires digital signature of nonces, making them
tied (no replay attacks)

### Certificate authority

Authority that holds all the certificates and the public keys in the domain of the signed users.
Each certificate contains informations like the name, the algorithm used to sign the key, validity, ecc.

Certificates are:
- Easy to access;
- Impossible to falsify;
- Temporary;

Certificates can be revoked:
There is a CRL (Certificate Revocation List) that must be checked upon accessing any certificate.
In the CRL there are certificates, and some fields like thisupdate (times CRL was issued), nextupdate, algorithm used in the crl, and some fields that are unique for each entry of the CRL such as usercertificate, revocationdate, ecc.

In alternative to CRL, you can use **OCSP**
Online Certificate Status Protocol used for obtaining the revocation status of an X.509 digital certificate. The URL for checking is provided in the certificate.

### Protocols for authentication

We study protocols for authenticating 2 users sharing the same key, without third party.

**Reflection attack**: If Trudy creates another session with the server to get information needed and then use them to authenticates on another session.
To prevent this attack, the server could use different keys, or different challenges;

# Password

Technique of authentication with passwords.

### Lamport Hash algorithm

![[Pasted image 20250106192350.png]]

Algorith used to safely store passwords inside a database, and to allow a user to authenticates with his password.

Can be salted to enlarge the dimension of the space of passwords and with salt we can reuse the password even after n times by just changing the salt.
New hashing to be sent (according the salt with the server): $hash^{n-1}(pswd|salt)$.

Attacks:
- **Small n attack**: just impersonate server and send small n' in order to get a certain hashing that will allow the attacker to reuse the password for $n- n'$ times.
### EKE
Encrypted Key Exchange.

![[Pasted image 20250106193839.png]]

Properties:
- Strong vs dictionary attacks and vs reply attacks;
- Mutual authentication;
- Define session key;

Is based on Diffie-Hellman key exchange.
Other variants:
- SPEKE ( use W instead of g);
- PDM (Password Derived Moduli, use g = 2 and choose p depending upon passord).

To avoid that by stealing derived data the attacker can authenticate, we use **augmentation**. In this kind of protocol it is required by the user to know the whole password, and from the server just a derived data.

![[Pasted image 20250106194748.png]]

# IPSec

IPSec is a protocol for network transimssions.
It aims to provide security in network information exchange.
It relies on:
- AH (Authentication Header) section for the authentication;
- ESP (Encapsulating Security Payload) to ensure data encryption;

**SA** (Security Association) is a one way function between a sender and a receiver that affords security for traffic flow. There are database of SA. Each SA is identified by some parameters:
- SPI (Security Parameter Index);
- IP Destination;
- Security Protocol Identifier (AH or ESP);

and each SA has some properties like:
- Sequence number counter;
- Anti-Reply Window;
- AH/ESP info;
- Lifetime;

ESP can optionally provide also authentication (uses HMAC).

(ChatGPT like slides)
IPsec (Internet Protocol Security) is a framework of protocols designed to secure communication over IP networks. It provides confidentiality, integrity, and authenticity to IP packets through various encryption and authentication methods.

### Key Features:

1. **Protocols**:
    - **AH (Authentication Header)**: Ensures data integrity and authentication but does not encrypt the payload.
    - **ESP (Encapsulating Security Payload)**: Provides data encryption, integrity, and authentication.
2. **Modes**:
    - **Transport Mode**: Encrypts only the payload of the IP packet, leaving the header intact. Commonly used for end-to-end communication (e.g., between two hosts).
    - **Tunnel Mode**: Encrypts the entire IP packet, including headers, and encapsulates it into a new IP packet. Often used for site-to-site VPNs.
3. **Key Management**:
    - **IKE (Internet Key Exchange)**: Used to establish and manage security associations (SAs), including the negotiation of encryption and authentication algorithms.
4. **Security Services**:
    - **Confidentiality**: Protects data from being read by unauthorized parties (encryption).
    - **Integrity**: Ensures data has not been altered during transmission.
    - **Authentication**: Verifies the identities of communicating parties.
    - **Replay Protection**: Prevents attackers from re-sending captured packets.
5. **Common Use Cases**:
    - Secure VPNs (Virtual Private Networks) for remote access or site-to-site connections.
    - Secure communication between hosts in a corporate network.
    - Protection of data in transit over untrusted networks.

### SSL/TLS

Transport Layer security (SSL is predecessor) provide security over networks;
Encrypt segments of network at the transpor layer end-to-end.
Current version: TLS 1.3
Previous versions (SSL 1/2/3) are compromised.
TLS v 1.1 and 1.2 are vulnerable to some attacks.
TLS protocol has 3 phases:

- **Clien/Server hello phase**: Client and Server establish a protocol to comunicate, and exchange informations like id, compression method and initialize random numbers;
- **Server** sends certificates, key exchange and request certificates;
- **Client** sends certificates and key exchange.
- At the end, they change cipher suite and finish handshake.


# SSH

Network protocol to exchange data between 2 devices via network.
The channel is made secure by:
- 1 or 2 ways authentication;
- Data confidentiality;
- Data integrity;

Allows to:
- Log into a shell on a remote host;
- Transfer file securely;
- Forward or tunnel a TCP/IP port;
- Arrange a VPN;

Different versions:
SSH1, SSH2, OpenSSH;

Has 4 layers:

- SSH **Transport Layer** **Protocol** (SSH-TRANS): It is in charge of dealing with the initial key exchange and server authentication, and arranges key re-exchange after a certain amount of data;
- SSH **Authentication Protocol** (SSH-AUTH): handles client authentication and provides a number of authentication method;
- SSH **Connection Protocol** (SSH-CONN): This layer defines the concept of channel. Each connection can host multiple channels.
- SSH File Transfer Protocol (SSH-SFTP);

In the SSH-2 version, the key are exchanged using Diffie-Hellman algorithm.
Uses pseudorandom functions for key derivation.

SSH basic usage:

````bash
ssh [options] <userid>@<hostaddress> [command]
````

some options:
- -v [-v [-v]] enables verbose modes;
- -C enables gzip compression;
- -L allows to bind a local address using a remote host as forwarding point;
- -N do not execute remote command;
- -1,-2 forces version 1, 2;
- -4,-6 forces addresses for IPv4, IPv6;
Default port: 22
hostadress is an ip address.
There is also the possibility, with the flag -X, to enable X11 forwarding, which allows the user connected to the other device to see the screen of the connected device.
X11 protocol has some security restrictions and it's not enabled by default.

# Firewalls

Barrier between local network and external network.
The goal is to control and limit the traffic, and to protect the local network from possible attacks from the outside.

Limitations:

- Does not protect with respect to **attacks that pass** the firewall;
- Does not protect from attacks **originated within the network** to be protected unless a personal firewall is used;
- Is not able to avoid/block **all possible viruses** and worms (too many, dependent on specific characteristics of the Operating Systems);

The packet filtering is performed by inspecting the headers.
Firewalls deals with incoming traffic as well as outcoming traffic.
Some filtering rules are application specific (for example, only appliable with HTTP, FTP ecc.).

There exists 3 main types of firewall:

- Packet-filtering router;
- Application-level gateway;
- Circuit-leve gateway;

### Packet filtering firewalls

For each packet, decide weather to let it pass or reject it. The decision is based on predetermined rules, based on pattern matching, using the informations available in the packet.

Packet firewalls can be:
#definition 
- **Stateless**: they do not remember of previously analized packets (faster);
- **Statefull**: they do remember of previously analized packets (slightly slower).

##### Weaknesses

Packet filtering firewalls has some weaknesses such as:
- They do not prevent application specific attacks;
- There is no user authentication mechanism;
- They are vulnerable to TCP/IP attacks;
- There might be security breaches due to missconfigurations;

A possible attack that can be performed is the Fragmentation attack, that consists in fragmenting an harmfull plaintext into different packets, and when the packets are composed following the TCP istructions, the composed plaintext will result in an harmfull plaintext that could for example generate a buffer overflow error and make the system crash.

In general:
> Ports from 1024 to 65535 are assigned to clients, ports under 1024 are reserved to server.

##### Session filtering

Basically, when the connection is changed, the security policy is checked again.
Always decisions made separately for each packet.

### Iptables

Are used to setup, maintain and inspect tables of ipv4 packet filtering.
**Chain** = list of rules that a set of packets should match to be accepted.
There can be multiple tables, and inside them we can have built-in chain (predefined chains) and user-defined chains (made by the user).

Standard targets (actions performed with packets when a match is found):
- accept = let the packet through;
- drop = drop the packet on the floor;
- queue = pass the packet to userspace (what happens depends on a queue handler, included in all modern linux kernels);
- return = stop traversing this chain and resume at the next rule in the previous (calling) **chain**;

We can explicitly declare packet matching modules with -m option declaring the module name or implicitly using the flag -p.
Flag -m is followed by the state like:
-m state [!] \--state state st
where st is {INVALID, ESTABLISHED, NEW, RELATED};
-p: we use -p flag to insert the protocol of the packet to check. Protocols are for example tcp,udp ecc...
-s -d source and destination
-j is used to declare the target for a given rule.
-f to declare that a rule refers to eventual fragments;
-n numeric: ip adress port ecc will be printed in numeric format;
the built in chain to specify with the tag -A in which we want to add rules are:
- Input: packet is going to be locally delivered;
- Forward: All packets that have been routed and where not for local delivery;
- Output: packet sent from the inside network to outside;

The following are the principle **state** that we can explicitly declare in a firewall forwarding rule:

- **INVALID** means a flawed packet and should be disregarded;

- **NEW** means a packet that has not been associated with any known packet stream, eg the SYN packet starting a TCP stream for HTTP(S);

- **ESTABLISHED** means a packet assoicated with a known packet stream, eg every packet for that HTTP(S) stream (whichever direction) after the NEW packet mentioned above;

- **RELATED** means a packet (otherwise NEW or not) associated with but not part of a know packet stream such as an FTP data stream transferring a file related to the FTP control stream which requested to send or receive the file;

by Chatgpt:
### **Circuit-Level Firewall**

- Operates at the **session layer** (OSI Layer 5).
- Verifies the **TCP/UDP connection handshake** but doesn’t inspect packet contents.
- Ensures secure communication between networks.
- **Use Case**: Often used for VPNs or as part of proxy servers.

### **Application-Level Firewall**

- Operates at the **application layer** (OSI Layer 7).
- Inspects the actual data within packets, ensuring protocol compliance (e.g., HTTP, FTP).
- Provides more granular security but is slower due to deep packet inspection.
- **Use Case**: Web application firewalls (WAF) or email filtering.

