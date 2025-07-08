
> **OCSR**
> A schedule S is order preserving conflict serializable if it is conflict equivalent to a serial schedule S’ and for all t, t’ Î tran(S): if t completely precedes t’ in S, then the same holds in S’. OCSR denotes the class of all schedules with this property


> **COCSR**
> A schedule S is commit order preserving conflict serializable if for all ti, tj $\in$ tran(S): if there are conflicting actions p $\in$ ti, q $\in$ tj in S such that p precedes q in S, then ci precedes cj in S


> **Recoverable**
> A schedule S is recoverable if no transaction in S commits before all other transactions it has “read from”, commit


> **ACR**
> S is ACR, i.e., avoids cascading rollback, if no transaction “reads from” a transaction that has not committed yet


> **Strict**
> We say that a schedule S is strict if every transaction reads only values written by transactions that have already committed, and writes only on transactions that have  already committed


> **Rigorous**
> We say that a schedule S is rigorous if for each pair of conflicting actions ai (belonging to transaction Ti) and bj (belonging to transaction Tj) appearing in S, with ai appearing before bj, the commit command ci of Ti appears in S between ai and bj


> **2PL**
> Legal sequence (no lock while an elem is already locked), valid (only one lock per transaction per element), ordered lock (no lock after unlocks)


> **Strict 2PL**
> A schedule S follows the strict 2PL protocol if it follows the 2PL protocol, and all exclusive locks of every transaction T are kept by T until either T commits or rollbacks


> **Strong Strict 2PL**
> A schedule S follows the strong strict 2PL protocol if it follows the 2PL protocol, and all locks of every transaction T are kept by T until either T commits or rollbacks


## IMPLICATIONS

![[Pasted image 20250708210018.png]]