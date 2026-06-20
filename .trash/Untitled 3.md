## Theorem 1  
Assuming semi-honest OT, for any n-input functionality F there is an n-party protocol that (n-1)-securely computes F (for semi-honest adversaries).  
- This works because of two phases:  
1. **Input-sharing phase**: The player $P_i$ with input $x^i$ computes $x^i = x_1 \oplus ... \oplus x_n$ and sends $x_j$ to $P_j$ .  
_Note: $x_1, ... x_{n-1}$ are computed randomly, and_ $x_n = x^i \oplus x_1 \oplus ... \oplus x_{n-1}$  
2. **Gate evaluation**: We consider evaluating a NOT gate, an XOR gate and an AND gate. In each case the invariant holds for the input wires to the gate, and show how the parties can ensure it holds for the output wires.  
3. **Output Reconstruction**: If party $P_i$ is supposed to learn the output of some output wire $z$, then all parties sends their private share $z_i$ to player $P_i$.  
### Evaluations:  
- **NOT gate**: Say $y = \neg x$ and parties hold $x_1, ..., x_n$ with $x_1 \oplus ... \oplus x_n = x$. Can compute shares of $y_i$ by having $P_i$ set $y_i = \neg x_i$  
- **XOR gate**: Say $z = x \oplus y$ and the parties hold all appropriate shares. They can compute shares of z by having every party set $z_i = x_i \oplus x_y$  
- **AND gate**: say $z = x \wedge y$ and all parties hold all appropriate shares. In contrast to the previous two cases, parties cannot compute shares of $z$ through local computation. They will need to rely on oblivious transfers.  
$z = x \wedge y = (x_1 \oplus ... \oplus x_n) \cdot (y_1 \oplus ... \oplus y_n) = \bigoplus_{i} x_iy+i \bigoplus_{i<j} (x_i \cdot y_j \oplus x_j \cdot y_i)$  
Each player needs to communicate with just another player to compute the shares of $(x_i \cdot y_j \oplus x_j \cdot y_i)$. This is done through 1-out-of-4 OT functionality:  
1. $P_i$, the sender, chooses uniform random bit $z_{ij}$, that will be used to mask his private share. It will then set $z_{ji}^{ab} = z_{ij} \oplus x_i\cdot b \oplus a\cdot y_i$. With $a,b \in \{0,1\}$.  
2. $P_j$, playing the role of the receiver, sends $(x_j, y_j)$ to the OT functionality, and receives in return $z_{ij} = z_{ji}^{x_jy_j}$  
Finally, each party $P_i$ computes $z_i = x_i \cdot y_i \oplus (\bigoplus_{i \neq j} z_{ij})$, forming shares of z. In fact $z_{ij} \oplus z_{ji}^{x_jy_j} = z_{ij} \oplus z_{ij} \oplus x_i\cdot y_j \oplus x_j \cdot y_i$ . The first two terms cancel each other, so we are good.