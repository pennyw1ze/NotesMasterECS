# 1
> Authenticity vs Authentication

Authenticity is a property that is granted if a user is able to understand if a messageis genuine or it has been compromised, while authentication is the procedure with whom a user is able to provide authenticity. For example, MAC algorithm provides authenticity (not authentication), as a property of the algorithm. Instead, the user authenticates a message when he computes the MAC code of a message and compare it with the received one in order to check if the message was like the original one.

---
# 2
### 2.1
> OFB Modes

The **OFB** modes of operations encrypt the plaintext, xor the output with the output of the previously encrypted plaintext and gives back the ciphertext, for each block in which our plaintext is divided. In decryption phase, the steps are inverted. We take the ciphertex, xor the successive ciphertext block and then decrypt the block in order to obtain the plaintext. In decryption phase, the blocks can be **preprocessed**, but the entire mode does not allow **parallelization**.

### 2.2
> Random IV

Yes, the algorithm needs a random IV because if it is not randomized, if the adversary is able to obtain the encrypted IV and it never changes, he will be able to compute any arbitrary encrypted string and encrypt and decrypt as he wants.

### 2.3
> KDF

A KDF is a key derivation function, a function that uses some algorithms that, starting from a key, generates a new random key with some security criteria, and that is hard to invert.

### 2.4
 ```bash
 echo "SliverSurfer" -n | openssl enc -aes-128-cbc -p -out out.enc
 ```

- echo command print on the terminal a string;
- -n flag removes the newline at the end of the echo print;
- openssl is a cryptographic tool of unix;
- enc command used to encrypt file;
- -aes uses aes encryption algorithm -128 128 bits size of the key, -cbc modes of operation;
- -out specifies the output file in wich will be inserted the output;
So our line will encrypt with aes 128 bits key cbc mode the line SilverSurfer without the newline and store it in out.enc file. The -p flag will print the keys and the IV used.

### 2.5
> ciphertext bit flip in CBC mode

If a bit is filpped in any block of the ciphertext, the current block and the next block of plaintext will be compromised, but just this 2 blocks. If the decryption(encryption) algorithm does not garant diffusion and confusion, only 2 bits of the plaintext will be flipped, otherwise the whole 2 blocks will be compromised.

### 2.6
> plaintext bit flip propagation

If a bit of the plaintext is flipped, the error is propagated to all the ciphertext.

---
# 3
### 3.1
> OCSP vs CRL

CRLs (certificate revocation list) are used to check certificate validity, but is an offline approach. OCSP are online certificate verifier. The link to check the certificate validity is provided withing the certification. The difference is that OCSP is an online real time validation while CRLs needs to be checked and updated manually.

### 3.2
> OCSP stapling

ano

### 3.3
De taisan

---
# 4
### 4.1
> Alice and Bob signing documents

The difference in the first case, if Alice signs the document payload and Bob signs the document payload too, they are just thrusting the document content. If Bob signs the whole document, including Alice signature, this implies that Bob is thrusting the document content and Alice's signature too.

### 4.2
> Inference based on blind signature verification failure

We can infer that the signature is not valid. This might happen because:
- The certificate is expired and has not been renewed;
- The certificate has been compromised, and it does not provide non repudiation any more;
More generally, without knowing the distinction, we can infer that the owner of the signature is no more responsible of that signature, also from the legal point of view.

### 4.3
The public key of whom ??? She owns her public and private key so she should be able to sign any document. This question is not well posed because it should have another subject, and anyway she should sign the document with her private key.

---
# 5
### 5.1
> Black and white listing

Whitelisting is the policy that implies the default discard of every packet, except for the one specified in the iptables rules, blacklist opposite.

### 5.2
> Command

```bash
# Assuming eth0 is the LAN interface
# Assuming eth1 is the INTERNET interface
iptables -P OUTPUT DROP
iptables -P INPUT DROP
iptebles -A FORWARD -i eth0 -o eth1 -j ACCEPT
iptables -A FORWARD -i eth1 -o eth0 -m state --state NEW -j DROP
```

--- END OF THE DUX---
