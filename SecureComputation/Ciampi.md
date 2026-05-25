**Honest Verifier Zero knowledge**: 
In sigma protocol, we can always produce a', c' and z' that are indistinguishable from a real view of a sigma protocol.

**Special Soundness**: 
If we have a, c and z, and we have a, c' and z' we can find the witness.

**Proof of Knowledge**: 
Having an extractor, we can extract the witness knowing only the theorem x. Example: rewind schnorr and get c',z' for same a. In fact:$$Special\ Soundness \implies PoK$$

Rember the simulation on schnorr protocol:
c, z randomly choosen. We compute $a = {g^z \over x^c }$


**Zero knowledge against arbitrary adversary**:
The output of an aribtrary verifier adversary and a simulator that takes in input a statement should be indistinguishable.

**Witness indistinguishability**:
Implied by ZK. 

---
### Multi-party computation
CRS = Common Random String. This assumption allows users to have common strings.
In the simulation paradigm this can be used to extract informations just like random oracle model.

Impossibility result:
We cannot have a 4 rounds coin tossing protocol.

\\\\\\



