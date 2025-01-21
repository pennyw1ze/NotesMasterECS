# 1
### 1.1
> A lot of stuffs

Simmetric ciphers are based on the concept of OTP, xoring a key with the plaintext. They start from an initial key and (Stream ciphers) from that key, generates continuously new keys in an arbitrary way and xor them with plaintext or (Block ciphers) divide the plaintext in blocks and encrypt single blocks with the key and optionally use some IV or seeds or counter that are xored during the process to enstrenghten the algorithm. Block ciphers can adopt different modes of operations, different ways in which the block are encrypted. For example, an algorithm that operates on blocks could choose to encrypt the message and then xor it to a seed and get the ciphertext, or xor the plaintext with a counter and then encrypt it, and so far. If a block algorithm uses an information derived from the previous block to encrypt the current block and get the ciphertext, it is said to be a asynchronous algorithm, otherwise it is said to be synchronous.

### 1.2
> RC-4

RC-4 Invented by Ronald Rivest, an MIT member, in the middle of the 90's, is a Stream Cipher algorithm. Given a key, the algorithm starts a process of key expansion in which for 256 times, the key is manipulated in order to obtain a new different key that is used to encrypt the plaintext. The algorithm has been broken and now it is no more in use. The reason is that the algorithm used small keys.

### 1.3
> CTR vs OFB

**OFB** (Output Feed Back) is a mode of operation that given a random IV, encrypts it and then xor it with the plaintext to obtain the first block of the ciphertext. The successive block of ciphertext are obtained by encrypting the encrypted IV (encrypted the same number of times as the number of blocks already encrypted) and then xoring it with the plaintext.
The **CTR** mode takes a counter, encrypts it and then xor it with the plaintext. Each block of ciphertext is obtained by xoring the encrypted counter (wich increase at each iteration) with the plaintext.
CTR is parallelizable both in encryption and decryption phase, but OFB no. OFB is asynchronous, CTR is synchronous. OFB allows preprocessing both in encryption and decryption phase. The IV must be randomly choosen, as well as the counter.

---
# 2
### 2.1
