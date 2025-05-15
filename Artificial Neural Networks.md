We have a target function f : X → Y , with:
- Y = {C1, . . . , Ck };
- Y = R;
- D = {⟨x1,t1⟩, . . . ,⟨xN,tN⟩};
Parametric approach:
- Define h = h(x; θ) and learn parameters θ so that h ≈ f

We create a network of "neurons", wich are cells containing a function, that evaluates an input given by other neurons that has already processed informations, and with all this inputs the actual neuron process the core function contained in the cell body, in order to determine the usual parameter.
We need the f function to be non-linear.
So what neuron does is basically applying a linear function to the input received from the previous neurons, and giving the processed data in input to the non linear function taken in exam.
Every neuron has its **own parameters**.
The output of the neural networks is $h(x;θ)$, then we compare the trained function h with the function f only on the dataset.
**Feedforward**: informations flows from input to output with no loops.
$h(x;θ) = h^{(3)}(h^{(2)}(h^{(1)}(h^{(0)}(x;θ)θ)θ)θ)$
where $h^{(m)}$ correspond to the m-th layer.

#### Feedforward N.N.
The length of the chain is the depth of the network.
We use FNN because Linear models cannot model interaction between input variables.
Shallow network is a network in which the output of all the neurons is combined into a single point.
For example, if X is a neuron we have:

           X
\___           /    \     
| x |------- X --> y
\_\___           \    /
           X

**Universal approximation theorem**: a FFN with a linear output layer and
at least one hidden layer with any “squashing” activation function (e.g.,
sigmoid) can approximate any Borel measurable function with any desired
amount of error, provided that enough hidden units are used.
It works also for other activation functions (e.g., ReLU)
Basically any function in a neural network can be approximated to the desired degree by using the right number of hidden units.
**Hidden units** = lines of neurons.