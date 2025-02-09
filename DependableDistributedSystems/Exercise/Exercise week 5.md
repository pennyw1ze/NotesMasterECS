# 1

1) Add the delivery of m1,m2,m3,m4 on the correct and deliver also m5 on p1;
2) Add the delivery of m1,m2,m3,m4 on the correct and deliver also m5 on p2;
3) Add the delivery of m1,m2,m3,m4,m5 on each process.
---
# 2

> n processes, fairloss linked, pfd.

Instance $urb$
Implements:
$fl$(fairloss links)
$P$(perfect failure detector)

Upon event $< Init >$:
	correct = $\Pi$;
	for each m: ack[m] = $\emptyset$;
	pending  = $\emptyset$;
	delivered = $\emptyset$;
	timer();

Upon event $< P, (crash,p) >$:
	correct = correct\{p};

Upon event $< urb, (BROADCAST,m) >$:
	for p $\in$ correct and p $\notin$ ack[m] :
		trigger $fl\_send(p,m)$;
	pending = pending $\cup$ {m};
	startimer($\Delta$,m);

Upon event timer($\Delta$,m):
	if m $\notin$ delivered:
		trigger $< urb, (BROADCAST,m) >$;

Upon event fl_deliver((m,p),self):
	if p $\notin$ ack[m] :
		ack[m] = ack[m] $\cup$ p;
	if (s,m) $\notin$ pending:
		pending = pending $\cup$ (s,m)
		for all q in correct\{p}:
			trigger fl_send((m,p),q)

Upon event correct $\subseteq$ ack[m] and (s,m) $\notin$ delivered and (s,m) $\in$ pending:
	trigger $< urb, (DELIVER,m) >$;
	delivered = delivered $\cup$ (s,m);
	pending = pending\{(s,m)};


1) In this implementation, if a process stop sending messages, it is either considered as faulty (So the deliver will continue without him), or all other process will wait for him to ack the message before delivering it.

2) I don't think it's possible because we could potentially have to keep all the message stored inside our array becuase fairloss could indefinitely loose them, and we will infinitely often have to resend them, and since there is not a finite number of message, the array could have to store infinite messages potentially.