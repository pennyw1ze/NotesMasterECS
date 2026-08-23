Note sul cryptographic binding che dovrebbe consentire allo user di presentare 2 o più credenziali contemporaneamente al verifier dimostrando che appartengono allo stesso device:

- L'ARF, per linkare due diverse attestation, suggerisce di inserire nelle attestation campi comuni ed univoci per lo user come il PID id: in questo modo lo user può mostrare al verifier che due attestation diverse sono appartenenti allo stesso user mostrando semplicemente il PID id su entrambe;
- Per 