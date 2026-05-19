Ok so I found the following fonts:
- https://www.darkreading.com/application-security/google-2029-deadline-quantum-safe-cryptography#:~:text=Alongside%20the%202029%20commitment%2C%20Google,Preparing%20for%20the%20Quantum%20Era;
- https://www.scworld.com/brief/google-accelerates-post-quantum-cryptography-timeline-to-2029#:~:text=Security%20chiefs%20Heather%20Adkins%20and,key%20in%20under%20a%20week;
- https://security.googleblog.com/2026/03/post-quantum-cryptography-in-android.html#:~:text=We%20are%20beginning%20tests%20of,out%20across%20the%20operating%20system;
And official [Google security blog](https://blog.google/security/security-for-the-quantum-era-implementing-post-quantum-cryptography-in-android/#:~:text=By%20integrating%20the%20recently%20finalized,execution%20of%20applications%20distributed%20globally) saying that **Android 17 marks the first phase of Android’s post-quantum transition:**
- **Securing the foundation**: We are upholding the integrity of our attestation and Chain of Trust by incorporating ML-DSA into Android Verified Boot.
- **Empower Developers**: The inclusion of ML-DSA support within Android Keystore and Play App Signing allows developers to safeguard their users and application.
- **Ecosystem Scale**: By using hybrid signatures for APKs, developers can create a protected transition that preserves current trust while adding post-quantum defenses to block unauthorized updates.

![[Pasted image 20260502142916.png]]

Based on the analysis of the official google blog, specifically at [[Security for the Quantum Era_ Implementing Post-Quantum Cryptography in Android.pdf#page=3&selection=0,0,0,10|this ]] section, google claims that developers will be able to encrypt with ML-DSA-65 and 87 in new Android 17 software by just using default TEE Api calls.

Cosa bisognerebbe fare:
- Analizzare implementazione di ML-DSA all'interno dell'android secure element, ci sta già un paper che lo fa;
- Capire se ML-DSA in TEE soddisfa LoA richiesti di EUDI dev;
- Implementare ZK proof 

Updates:
Ho trovato diversi paper che parlano di implementazione di Post Quantum anonymous credentials system, 2 in particolare lo fanno utilizzando le tecnologie da noi prese in considerazione:
- ML-DSA signature;
- ZK stark module: zkDILLITHIUM;
Queste prove su ML-DSA con dillithium sono state già costruite da esperti e valutate le loro performance. Quello che si potrebbe fare sarebbe un montaggio di tutti questi protocolli messi insieme con conseguente analisi di performance e sicurezza raggiunta.
Queste prove sono state implementate e valutate **NON PER UN USO MOBILE** in quanto mancava la componente ML-DSA interna all'android secure element che invece noi addesso abbiamo. La novità sarebbe portarle su mobile e valutare un implementazione COMPLIANT con ARF ed EUDI wallet.

Si potrebbe anche tentare un implementazione ed ottimizzazione delle prove zk stark su ML-DSA ma sarebbe molto complicato da quello che ho capito. In caso si dovesse tentare un implementazione custom, potremmo anche implementare la modifica che volevamo apportare allo schema di Frigo Shelat sulla OR proof.

---
# Important observation!

For the post quantum transition, I just figured out that it is not necessary to have a post-quantum device binding signature. Why ?
That is because if device keys would for whatever reason being hacked and reversed, in order to find private key from secret key, this would not give to the adversary any advantage to forge device signatures.
Hacked credentials would give the adversary chance to forge signature only if able to steal user credentials. Also in that case the attacker would not be able to sign in to providers if they asks for user-binding (fingerprint login).

So for this reason it does not make particular sense to have post-quantum secure user's device signatures.
It would instead make a lot of sense to have ML-DSA issuer signatures. On top of it, a zero knowledge post-quantum secure proof should be implemented in order to allow anonimous credentials usage. 
Looking at how many instances I would need to modify at the Frigo Shelat zk statements.

Ah no wait! This is a bit of a problem because I would loose post-quantum non linkability! If the zero knowledge proof does not allow the Relying party to look at my device public key, the quantum computer will not be able to break anything (if the ZK proof is post-quantum secure). Wait, but if the device public key or private key or any kind is made visible to the quantum computer it can track me also without breaking the key! So how does it work ? 
What the adversary can see in the proof is a signed message I think, then the proof says the message has been signed with private key associated with public key on the document.

#### Conclusion:
The device binding private/public key does not need to be post-quantum secure, in fact it is never shown, only a zk proof showing possession and signing a nonce is sent, it is never possible for the relying party to see the device public/private key! Also if found, adversary would still need to steal your credentials in order to make you any damage.
Notice a problem: if the issuer stores your device public key and has a quantum computer, he can revert and get also your private key and will be able to use your credentials having both valid attestation (he is the issuer) and valid device binding keypairs (he could also sell them beign SEMI-HONEST).

---
### Statement

	$x = (pk,a,id,tr,now),\ w = (MSO,pk_{dx},pk_{dy})$
	$e1 = SHA256(MSO[0 : 183])$
	$a = MSO[id] (pkdx, pkdy) = MSO[96 : 160]$
	$tstart = MSO[48 : 56]$
	$tend = MSO[56 : 64]$
	$tstart < tnow < tend$ 
	$true = p256.verify((r1, s1), e1, PKI I )$
	$true = p256.verify((r2, s2), H(tr||hdr), (pkdx, pkdy))$

For sure the statement could be detached into:
### Split up proof

Issuer's signature proof zk-dillithium (STARK):
	$x = (PK_{II},C), w = (MSO,r)$
	$C = Poseidon(MSO,r)$
	$e1 = POSEIDON(MSO[0 : 183])$
	$true = Dillithium.verify((r1, s1), e1, PK_{II} )$

Attribute disclosure, document validity and device binding proof (LIGERO):	
	$x = (a,id,tr,now,C), w = (MSO,pk_{dx},pk_{dy},r)$
	$C = Poseidon(MSO,r)$
	$a = MSO[id]$
	$(pkdx, pkdy) = MSO[96 : 160]$
	$tstart = MSO[48 : 56]$
	$tend = MSO[56 : 64]$
	$tstart < tnow < tend$ 
	$true = p256.verify((r2, s2), H(tr||hdr), (pkdx, pkdy))$

Verifier additional check:
	STARK(C) = LIGERO(C)

In this way we would have to bound proof tho because they use the same MSO instance.
Ok, they will have to because both device binding and issuer signature are tied to MSO. So we have to figure out a way to 

THESIS: Post quantum transition for device bounded anonymous credentials: the EUDI wallet application

Secondo Lehman et al. nel paper Vision e Device bound anonymous credentials presentato all'eurocrypt 2026, e secondo SoK, nei secure element verrà implementato ML-DSA che sarà necessario implementare per ottenere un device device binding post-quantum secure.

Dove viene usata pk del device:
- Document and attestation creation da parte dell'issuer;
- Wallet Unit attestation (WUA);
- Credential presentation;

Abbiamo trovato un modo per evitare questo overhead pesantissimo e continuare ad usare ECDSA per il device binding in un setting post quantistico.
Estenzioni:
La firma dell'issuer dovrà necessariamente essere resa post-quantum secure.
- Esiste l'opzione di dimostrare che l'issuer si trova in una trusted list ma per molte situazioni non ha senso.
- Esiste l'opzione di implementare la revocation list ma non ce ne occuperemo.
- Esiste l'opzione di dare deniability alle credenziali di longfellow ma non ce ne occuperemo.
- Esiste l'opzione (?) di fare blind credential issuance dall'Issuer post-quantum ma non ce ne occuperemo.

Cambiare claim di longfellow come segue per transizione full post-quantum senza mostrare all'issuer la propria signature, ma solo un commit di essa:

Issuer's signature proof zk-dillithium (STARK):
	$x = (PK_{II},C), w = (MSO,r_1)$
	$C =$ SHA256$(MSO,r_1)$
	$e1 =$ SHA256$(MSO[0 : 183])$
	$true = Dillithium.verify((r1, s1), e1, PK_{II} )$

Attribute disclosure, document validity and device binding proof (LIGERO):	
	$x = (a,id,tr,now,C), w = (MSO,pk_{dx},pk_{dy},r_1)$
	$C =$ SHA256$(MSO,r_1)$
	$a = MSO[id]$
	$\textcolor{green}{SHA256}(pkdx, pkdy) = MSO[96 : 160]$
	$tstart = MSO[48 : 56]$
	$tend = MSO[56 : 64]$
	$tstart < tnow < tend$ 
	$true = p256.verify((r2, s2), H(tr||hdr), (pkdx, pkdy))$

This is because we are going to send the issuer a commitment of our device public key instead of the public key in order to avoid quantum secret key recovery from the issuer (notice that the issuer wouldn't still be able to do anything with that without having stolen our credentials). Since each device public/private key pair is generated singularly for each attestation, we do not need to add randomness inside the commitment sent to the issuer to be untracable (`Moreover, a Wallet Unit will present each WUA only once. Apart from preventing linkability, this is also to prevent that the public keys in the WUA are used in multiple PIDs or attestations.` ~eudi.dev)

This could also be OPTIMIZED with a **POSEIDON** instantiation (requires the issuer to hash the MSO with Poseidon instead of SHA256) and **PARALLELIZATION** of the creation of ZK instances:

Issuer's signature proof zk-dillithium (STARK):
	$x = (PK_{II},C), w = (MSO,r_1)$
	$C =$ Poseidon$(MSO,r_1)$
	$e1 =$ Poseidon$(MSO[0 : 183])$
	$true = Dillithium.verify((r1, s1), e1, PK_{II} )$

Attribute disclosure, document validity and device binding proof (LIGERO):	
	$x = (a,id,tr,now,C), w = (MSO,pk_{dx},pk_{dy},r_1)$
	$C =$ Poseidon$(MSO,r_1)$
	$a = MSO[id]$
	$\textcolor{green}{SHA256}(pkdx, pkdy) = MSO[96 : 160]$
	$tstart = MSO[48 : 56]$
	$tend = MSO[56 : 64]$
	$tstart < tnow < tend$ 
	$true = p256.verify((r2, s2), H(tr||hdr), (pkdx, pkdy))$

This implementation is FULLY compatible with POST-QUANTUM standards (ML-DSA, SHA256, LIGERO) and will be easily to merge with actual MDOCS ecc. (just changing siganture scheme and instead of getting device public key, sha256 of it), keeping ECDSA running inside secure elements.

NEW HYPOTETHICAL OPTIMIZATION:
Instead of committing $C = H(MSO, r_1)$, we commit to $C = H(MSO) \oplus r_1$ and we cut off a proof of hash preimage knowledge on the zkSTARK.

Issuer's signature proof zk-dillithium (STARK):
	$x = (PK_{II},C), w = (MSO,r_1)$
	$e1 =$ Poseidon$(MSO[0 : 183])$
	$C = e_1 \oplus r_1$
	$true = Dillithium.verify((r1, s1), e1, PK_{II} )$

Attribute disclosure, document validity and device binding proof (LIGERO):	
	$x = (a,id,tr,now,C), w = (MSO,pk_{dx},pk_{dy},r_1)$
	$C =$ Poseidon$(MSO) \oplus r_1$
	$a = MSO[id]$
	$\textcolor{green}{SHA256}(pkdx, pkdy) = MSO[96 : 160]$
	$tstart = MSO[48 : 56]$
	$tend = MSO[56 : 64]$
	$tstart < tnow < tend$ 
	$true = p256.verify((r2, s2), H(tr||hdr), (pkdx, pkdy))$


WOW THIS IS AMAZING AND SOLVES A LOT OF ISSUES!!!!!!!!
I can completly remove hashing proof from STARK!!!!!
$C = e_1 \oplus r$

Issuer's signature proof zk-dillithium (STARK):
	$x = (PK_{II},C), w = (\textcolor{green}{e_1},r)$
	$C = e_1 \oplus r$
	$true = Dillithium.verify((r1, s1), e_1, PK_{II} )$

Attribute disclosure, document validity and device binding proof (LIGERO):	
	$x = (a,id,tr,now,C),\ w = (MSO,pk_{dx},pk_{dy},r)$
	$C =$ SHA256$(MSO) \oplus r$
	$a = MSO[id]$
	$pkd = MSO[96 : 160]$
	$pkd =$ SHA256$(pkdx, pkdy)$
	$tstart = MSO[48 : 56]$
	$tend = MSO[56 : 64]$
	$tstart < tnow < tend$ 
	$true = p256.verify((r2, s2), H(tr||hdr), (pkdx, pkdy))$

