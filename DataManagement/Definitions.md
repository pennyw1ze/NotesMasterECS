
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


> **TimeStamp based**
> Algorithm:
```pseudocode
Action ri(X):
if ts(Ti) >= wts(X)
then
	if cb(X)=true or ts(Ti) = wts(X)
		then set rts(X) = max(ts(Ti), rts(X)) and execute ri(X)
	else put Ti in “waiting” for the commit or the
		rollback of the last transaction that wrote X
else
	rollback(Ti)


Action wi(X):
if ts(Ti) >= rts(X) and ts(Ti) >= wts(X)
then 
	if cb(X) = true
		then set wts(X) = ts(Ti), cb(X) = false, and execute wi(X)
	else 
		put Ti in “waiting” for the commit or the rollback of the last transaction that wrote X
else
	if ts(Ti) >= rts(X) and ts(Ti) < wts(X)
		then if cb(X)=true
			then ignore wi(X)
		else put Ti in “waiting” for the commit or the rollback of the last transaction that wrote X
	else rollback(Ti)
```


## IMPLICATIONS

![[Pasted image 20250708210018.png]]

## File Organization COMPARISON
• Heap file
	– Efficient in terms of space occupancy
	– Efficient for scan and insertion
	– Inefficient for search and deletion
• Sorted file
	– Efficient in terms of space occupancy
	– Inefficient for insertion and deletion
	– More efficient search with respect to heap file
• Clustered tree index
	– Limited overhead in space occupancy
	– Efficient insertion and deletion
	– Efficient search
	– Optimal support for search based on range
• Static hash index
	– Efficient search based on equality, insertion and deletion
	– Optimal support for search based on equality
	– Inefficient scan and search based on range