# 1

Oral answare

---
# 2

**a)** F
**b)** T
**c)** F
**d)** F
**e)** F

---
# 3

![[Pasted image 20250209115000.png]]

3)

$(e1,e3),(e2,e3),(e5,e7),(e6,e8),(e6,e7),(e3,e7),(e8,e9/10/11)$

---
# 5

Upon event send(m,Id):
	if Id > my_id:
		pp2p_send((m,Id),my_id+1)
	else:
		pp2p_send((m,Id),my_id-1)
Upon pp2p_deliver((m,Id),self):
	if Id = my_id:
		trigger (instance, DELIVER,m)
	else
		trigger send(m,Id)