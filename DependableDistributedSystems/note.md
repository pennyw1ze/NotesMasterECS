Dubbi su algoritmi:
- Algoritmo URB:
	Dubbi un po in generale. Tendenzialmente dovrebbe fare piu' delivery del RB ma a me sembra che abbia piu' restrizioni.
	Inoltre non capisco perche' dobbiamo aspettare gli ack mettendo in pending prima di fare delivery. Se tutti aspettano
	ack per fare delivery, nessuno deliverera' mai nulla.
- Algoritmo Floading consensus:
	Dubbio sull'if round = round -1 non ho capito cosa succede e a che serve
	Non ho capito a cosa servono i round. Pensave fosse un round per ogni decisione ma mi sa di no.
- WUTO is a total ordering but does not respect the agreement property! in this example: