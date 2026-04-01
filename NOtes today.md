how to prevent players from using their inputs to leak informations on other player's input ?

Evaluator:
We do not perform an OT for every wire and every circuit. Instead, we will perform an OT using the evalutaro input just once for each wire in a single circuit, and then recycle that bit to cover all other wires with the same label for other circuits.

Garbler:
Instead of garbling circuit for funciton f(x,y) he will compute output for function f(x,y) || H(y).
- Evaluator checks H(y) is same for majority;
- H should be collision resistant;
- H should hide y;
Replacing H with random oracle model will add some kind of syntactycal problem because we should garble the circuit of the random oracle, but it has no circuit ! SHA256 comes after defining the security of the protocol.
We cannot claim security of random oracle and then demonstrate with the circuit of SHA256, it is not secure!

