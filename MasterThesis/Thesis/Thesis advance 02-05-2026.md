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