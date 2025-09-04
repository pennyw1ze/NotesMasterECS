# Machine Learning - Overview
Machine learning is *programming computers to make them improve in a task using example data or past experience*. It's useful when:
- When there isn't expertise available
- Humans are unable to explain how they carry out a task
- A solution must be finetuned for specific cases
## Linear Regression
Based on the idea of using a linear function to fit the data in itself. It's a regression problem, so we have
- **Target function**:$f: X \rightarrow Y$
- $X \subset R^m$
- $Y = R$
- $D = \{<x_1, t_1>,...,x_N, t_N\}$
- The idea is to find hypothesis $h$ that best approximates $f$
Given a set of point, to get a good hypotesis function we need to minimize the *distance* between our measure and the actual real data
### Least Squared Error
Sum of the squared error between hypothesis and real data
$\sum_i (h(x_i)-t_i)^2$
This sums over all our features. The goal is to find $h$ such that it minimizes the LSE. There are other **Objective function**
# Linear Classification
In linear regression we have samples of inputs/outputs, and with that we need to fit a straight line through this such that we minimized the Sum Squared Error (Loss function), thanks to SGD.
Today we'll use **Linear Classification**, through the use of a different function, the **Logistic curve**
- Our target function will become $f: \mathbb{R}^n \rightarrow \{c_1, ..., c_k\}$
### Linearly Separable Data
We'll consider the case in which we need to find a plane (*hyperplanes, in case of multidimensional datasets*) that *separates* instances to different classes.
We find a function that has a form $y_1(x) = w^Tx$, where the output is the value associated to $c_1$.
Output of this can be $\ge 0$, in which case we select the positive value, otherwise negative. 
### Learning the parameters
How do we learn the parameters in $w$? such that the objective function is a k-class discriminant?
- **Least Squares**: may be an option, by defining the squared error over every single objective function. $E(W) = \frac12 Tr()$;
- **We classify the input using the argmax function**
This approach suffers from one drawback! It's not robust to outliers, which will "attract" the separator.
## Perceptron
We add non-linearity to our system, by adding a `step` function, after the linear function, where its output is $\{-1,+1\}$, using a threshold over the objective function.
==In this case though we cannot use SGD directly, since it's non-differentiable, and in most of the cases the gradient will be zero!==
We can, instead, apply the SGD on the untresholded unit, but we are not interested in the ERROR, but in the pure classification in itself. As long as we move in the direction of the gradient in regard of the classification of the thresholded unit. $(w_i \leftarrow w_i + \Delta w_i:\Delta w_i = \mu \sum_{<x,T> \in D}(t-sign(w^Tx))x_i)$ 
### Algorithm
- Input: Parameters of h(x) w; dataset D
- Output: w assignment minimizing classification error (wrt h(x))
**Steps**:
1. Initialize w with small random values (NOT all zeroes)
2. repeat until termination condition
3. return w
Using a linearly separable dataset, along with a small enough learning rate, we are guaranteed to converge.
# Support Vector machine
Support the concept of margin, to get a better generalization. Find separation surface that guarantees the maximum distance from the closest points.
We will skip the analytical explanation.
# Non Linearly Separable Data
We can use polynomial functions to find a more fitting solution. 
> [!WARNING]
> This solution is way more susceptible to OVERFITTING, which gets us a lot more noise
# Evaluation
In a machine learning algorithm we need to be able to know if we got better or worse. To do this, we will use indexes like:
- **Accuracy**
- **Precision**
- **F1-Score**
- etc...
## True/Sample Error/Accuracy
- **True error**: It's the probability that *h* misclassifies random instance. It's not calculable a priori, so we compute the **Sample error**
- **Sample Error** of h with regards to *f and S*
	- $\displaystyle error_S(h) = \frac1n \sum \delta(x)$, with $\delta(x) = \begin{cases}1 & \text{iff f(x) != h(x)} \\ 0 &\text{else} \end{cases}$
*How well does the sample error estimate the true error?*
It's the problem of the estimation bias, in which we try to timit the error. For unbiased estimate, *h* and *S* must be chosen independently. This is the importance of choosing a good *training set*, since the error is an optimistic value. (Or we risk **overfitting**)
## Training set, Test set, Error Estimation
To comput an independet sample error, we need to 
1. partition our dataset as $D = \{T,S\}, s.t. (T \cap S = \emptyset)$ where:
	- $T$ is the **training set**
	- $S$ is the **test set**
	- Generally, $|T| = 2/3 \cdot |D|$ as a rule of thumb
2. Compute $h$ using the training set $T$
3. Evaluate error on the *test set* $S:error_S(h) = \frac12 \sum_{x \in S} \delta(x)$
### K-Fold Cross Validation
Performs many experiments and compute $error_{S_i}(h)$ for different independent test $S_i$, by dividing our dataset $S$ into $\{S_1, ..., S_n\}$ smaller datasets.
# Performance Metric
We have 4 possibiliteis, for every prediction

|                   | Predicted: T       | F                  |
| ----------------- | ------------------ | ------------------ |
| **True Class: T** | **True positive**  | **False negative** |
| F                 | **False positive** | **True negative**  |
$error_s(h)$ are seen as $\frac{FN+FP}{TP+TN+FP+FN}$ 
Accuracy is $1-error_s(h)$ = $\frac{TN+TP}{TP+TN+FP+FN}$Other metrics:
- **Precision**: It's the ability to avoid false positives $\displaystyle \frac{TP}{TP+FP}=\frac{\text{\# true positives}}{\text{\# predicted positives}}$
- **Recall**: It's the "confidence" with which a true positive is chosen $\displaystyle \frac{TP}{TP+FN}=\frac{\text{\# true positives}}{\text{\# real positives}}$
- **F1-score**: Harmonic mean of precision and recall $\displaystyle 2\frac{precision \cdot recall}{precision+recall}$
## Confusion Matrix
It's a better and more visual way to represent the prediction, comparing the classification of the various instances. It's an $n \times n$ matrix consisisting of all $\{c_1, ..., c_n\}$ instances. The element at position $(c_i, c_j)$ it's the number of $c_i$-instances classified as $c_j$.
We are interested in seeing a strong number over the diagonals, which means that we have correctly classified the label to an object.
# Overfitting
It's the idea of having a very low error on the training set, or more generally that the error is getting lower, but with the test set it performs way worse. It's *not able to classify correctly instances that it has never seen*.
### Regularization
It's a technique to mitigate overfitting, by penalizing complex hypotheses.
$w* = argmin_w E(w) + \lambda R(w)$ 
With R(w) being the regularization function. If the function has two solutions, with one having lower parameters, it'll prefer that. It's an *addition* to the error function such that we lean towards less overfitting.
# Artificial Neural Networks
We are getting into the non-linear functions.  We also here use a parametric approach, in which we define $h = (x; \theta)$ and learn parameters $\theta$ such that $h$ gets mostly to $f$.

> [!NOTE]
> The use of non-linear functions is essential, since any combination of the linear function is still linear, and for various reasons this LIMITS the power of our training network

We are basically inspiring ourself to an actual *human neuron connections*, in which hidden layer outputs can be seen as an array of unit activations based on connection ("axons") over the previous unit.

For example, we may consider the sum of all connected neuron outputs and then use a sign output as an **activation function**.
We'll consider **feedforward** neural networks, which work *in stages*, or layers. A Neural Network with a single hidden layer is called shallow.
## Feedforward
$\displaystyle h(x; \theta)=h^{(3)}(h^{(2)}h^{(1)}(x, \theta^{(1)}); \theta^{(2)}); \theta^{(3)})$ 
FNN are **chain structures**, where the lenght of the chain is the **depth** of the network. Final layer can be also called the **output**. **Deep learning** follows this trend, but with a huge number of hidden layers.
### why?
Linear models cannot model interaction between input variables, 
## Examples
$y = \phi_0 + \phi_1 \cdot a[\theta_{10}+\theta_{11}x] + \phi_2 \cdot a[\theta_{20}+\theta_{21}x]+\phi_3 \cdot a[\theta_{30}+\theta_{31}x]$
Where $a$ is a ReLU activation function
$\displaystyle a[z] = ReLU[z] = \begin{cases} 0 & z < 0 \\ z & z \ge 0 \end{cases}$
ReLU stands for *Rectified Linear Unit*, which zeroes values below zero.

# Universal Approximation Theorem
A FFN with a linear output layer and at least one hidden layer with any 'squashing' (ReLU) activation function can approximate any Borel measurable function with any desired amount of error, provided a sufficient number of hidden layer unit. This means that we can reduce the error we need to have more hidden units.
There is a drawback regarding the deepness level of the FNN, and it's about the fact that it may get too complicated to run with.

Once we do a training path, we can update the parameters such that the error gets lessened. To do this, we need to compute the Gradient for every parameter (partial derivative). To do this, it's crucial that the loss function is differentiable (not the case for ReLU!)Look at video lecture
# Hidden Units Activation Functions
- Most common is the **Rectified Linear Units (ReLU)**:
	- Easy to optimize, since it's close to linear unit
	- Not differentiable at 0, not a problem
- **Sigmoid Funcion**
- **Hyperbolic Tangent**
# Loss function
Mean Squared Error is a good loss function.the choice for ReLU for hidden units has been shown that on average it's a good function.
### Cost Function
Model implicitly defines a conditional function $p(t | x, \theta)$. We want to maximize the probability to reach a certain output given an input, and we can oly choose $\theta$ parameters.
- Cost function: Maximum likelihood principle (**cross-entropy**)
	- $\displaystyle J(\theta) = E_{x,t}[-\ln(p(t|x, \theta))]$
	- With $\ln ...$ being the data set
	- and $E$ being the cross-entropy, which is an estimation. If we assuyme that error is gaussian noise we have exactly Mean Square Error
Example:
## Binary classification
Sigmoid unit
- Sigmoid activation function $\sigma(x) = \frac1{1+e^{-x}}$ , useful becase of its range $y\in(0,1)$ 
- $y=\sigma(\alpha)$, with $\alpha = w^Th+b$ 

Sigmoid saturates only on correct answers!
In general , in this case we have the likelihood, since it's a binary classification we have Bernoully distribution
- $p(t|x,\theta)=\sigma(\alpha)^t(1-\sigma(\alpha))^{1-t}$ 
	- this means the probability of having the actual value given x and $\theta$, which is a combination of $\sigma(\alpha)$ if $t=1$ and $1-\sigma(\alpha)$ if $t=0$

To calculate now the cost function over the average of the outputs:
-  $J(\theta) = \frac1{|D|} \sum_{(x,t)\in D} - \ln p(t, x, \theta)$ (assuming that $J(\theta)$ approximates the **cross-entropy**)
- $- \ln p(t, x, \theta) = -\ln \sigma(\alpha)^t(1-\sigma(\alpha))^{1-t} = -\ln((2t-1)\alpha)= \text{softplus}((1-2t)\alpha)$ 
This is called the **binary cross-entropy**.

## Multiclass classification
In this case the output will have $k$ units, since every unit will classify the probability of an element being in that specific domain (so that $\sigma(\alpha_i)=p(t_{i}| x, \sigma)$)
We'll use **softmax** function, which computes the exponential of the probability over the sum of all of the others. We normalize the outpus, so that the sum of all values are equal to 1.
- $\displaystyle softmax_i(\alpha) = \frac{e^{\alpha_i}}{\sum_j e^{\alpha_j}}$ 
We don't need a sigmoid at the last output anymore, since they are already being normalized between 0 and 1 with softmax.
The likelihood corresponds to Multinomial distribution (*one-hot encoding*):
- $\displaystyle p(t|x, \sigma) = \prod_{j}(softmax_j(\alpha))^{t_j}$  which means that the probability of success is the probability returned by the softmax on the correct classification.
- The cost function is the same thing, as always
## Summary
- **Regression**: Linear output unit, mean squared error loss function
- **Binary classification**: Sigmoid output unit, binary cross-entropy
- **Multi-class classification**: Softmax output unit, categorical cross-entropy
Generally, all hidden layers use ReLU as their output function
# Gradient Computation
It's basically the same as the gradient descend in the linear regression, with the difference of having multiple layers to do the gradient. **not being linear, it means that the gradient function is not convex** => we use **Stochastic Gradient Descend**.
To train our network we need to compute the gradients with respect to the network parameters $\theta$. The **backpropagation** algorithm is used to propagate the gradient computation from the cost through the whole network.
- We just need it to be *differentiable*, so that we can do the partial derivative over every parameter and use that to update the parameter values

Computing the derivative over the loss function over ALL the parameters is incredibly difficult, and it's a bottleneck. We use the chain-rule: we differentiate a composition of functions.
### Chain rule
let $y = g(x)$ and $z = f(g(x)) = f(y)$ \[derivate of a composite function]
by applying the chain rule we have:
- $\displaystyle \frac{dz}{dx} =  \frac{dz}{dy} \frac{dy}{dx}$ 
In the case of vector functions, $g : \mathbb{R}^m \rightarrow \mathbb{R}^n$ and $f: \mathbb{R}^n \rightarrow \mathbb{R}$ 
- $\displaystyle \frac{\partial z}{\partial x_{i}} = \sum_{j} \frac{\partial z}{\partial y_{j}}\frac{\partial y_{j}}{\partial x_{i}}$
 
This means that we can calculate the derivative for every node of the layer to calculate the entire derivative. backpropagation reuses a lot. In the forward pass we keep stored both  the function output and also the linear combination of it.
# Stochastic Gradient Descend (SGD)
The gradient now is calculated through backpropagation, and then it uses the gradient along with the learning rate to update the parameters.

The loss function, given that now the overlying functions are non-linear, is not linear anymore. We may get ourselves into a local minimum. The problem is mitigated by using the **stochastic gradient descent** that, instead of *computing the gradient* over the entire dataset, computes it *over a small mini-batch subset* (an **epoch** is when we have completed an iteration going through all the mini-batches). By doing that we are introducing noise, and with that *we decrease the probability of getting stuck* into a local minimum.
### Variable Learning Rate 
We want to introduce a variable learning rate, since it's safe to assume that the variations at the first epochs are larger than the ones in the last.
There are many functions that modify the learning rate based on the number of epochs. 
- AdaGrad
- RMSProp
- Adam (Best in general).
# Regularization
This is an important feature to reduce overfitting, which tries to remove the noise. This can be seen as a very low error on the training data, while on the test data error increases. We have several options to limit the issue:
- Dataset augmentation, which applies data transformations to the dataset to artificially augment the number of data points. (by image rotation, scaling, varying illumination conditions, ...)
- Early stopping
- Dropout, which randomly eliminates network units with probability $\alpha$
- Parameter norm penalty, which adds a regularization term $E_{reg}$ to the cost function. Since we are minimizing the loss we'll have that the parameters will be significantly highered only if the normalization decreases.

We'll use Feedforward Neural Networks are plenty powerful for now.
# Reinforcement Learning
We want to learn actions that are needed to reach a TASK, which is different than the goal. It's like in planning, but without the action domain, which it needs to learn by itself.
A model of learning is based on **Markov Decision Process**
## MDP
Model by which we express the model, domain and the task. Composed of:
- $S$: (Finite) Set of states
- $A$: Finite set of actions
- $\delta(s'|s,a)$: Probability distribution over transitions \[different, since in planning we don't have probabilities, not even in non-deterministic] -unknown-
- $r:S \times A \times S \rightarrow \mathbb{R}$: reward function -unknown-. depends on current state, executed action and next state.

With this model we can capture many dynamic systems, with:
- **markov property**: next action depends on current state and action, and not of any past history. In the current state there are all the necessary informations
- **full observability**: at every point in time we know the state we are in.

We have 2 variants, *deterministic* (like our learning model) and *non-deterministic*.
#### Example: Deterministic grid controller
Agent must reach from the left-most column to the right-most, moving left-right-down-up, all of this without touching the black square.
- $S$: {(r,c) | Coordinates in the grid}
- $A$: {Left, Right, Up, Down}
- $\delta$ : cardinal movements with no effect
- $r$: 1000 for reaching righ-most column, -10 for hitting obstacle, +1 for right action, -1 for left action.
The reward "guides" our task to get into the best result. In a deterministic setting we know always our state.
### Discounts
To not let our model to go into a loop, which would produce infinite results, we use the idea of **discounts**, which exploits the idea of getting a higher reward before is preferable. With the passing of time, we discount the reward and make it less as time goes on. This bounds our total reward, which is a geometric series, and we have a max: we can compare different runs.
$\displaystyle V^\pi (s) =  \sum_{t=1}^\inf \gamma^{t-1} r_t \le \sum_{t=0}^\inf \gamma^t R_{max} = \frac{R_{max}}{1-\gamma}$

In non-deterministic effects it's not as simple, a sequence is no longer enough, and we need a generalization function $\pi: S \rightarrow A$, which is called a policy. -same of non-deterministic planning-. It should be total.
This will give us, given a state, the best action to do.
### Optimality 
We may want to know, or just limit our optimal value. We'll call $\pi^*$ our optimal policy if, for every state $s \in S$ and every policy $\pi$
			$\displaystyle V^{\pi^*} (s) \ge V^\pi (s)$  
Given an MDP we want to find an optimal policy $\pi^*:  S \rightarrow A$ such that $pi^*$ is an optimal policy. For infinte-horizon (can be infinitely executed) we can always find an optimal _stationary_ policy.
$\displaystyle V^\pi (s) = \sum \delta(s, \pi(s), s\prime) (r(s,\pi(s), s\prime) + V\pi(s\prime))$
(with $s^\prime$ being the possible landing states)
this the average of the expected value of our final reward.

given all of the $V^\pi(s_i)$ , we associate them to variable $x_i$. Given $s_i$, $V_\pi (s_i)$ will give us the average value of our reward (by summing up all values)
[Finished at 56:35](https://www.youtube.com/live/oUuzJEIaJu0)
This may not be the best in terms of computational equations.
# Policy evaluation
The way is called **iterative policy evaluation**. It proceeds by approximation of the equation and it works as follows:
1. Take the equation $V^\pi (s)$ previously discussed 
2. We assign random values to all $V^\pi (s)$ and $V^\pi (s^\prime)$ -even zero-
3. If there are terminal states, the function must have value of 0.
4. Every value for $V$ has a version associated. Either the function is the correct value function for the policy -and it satisfies bellman equation- or we can use it to update our values.
assuming pobabilities and rewards are known.

$V_{k+1}^\pi (s) \leftarrow \sum \delta(s, \pi(s), s\prime) (r(s,\pi(s), s\prime) + V_k^\pi(s\prime))$ 
## example
$V_0^\pi(s) = 0 \ \forall s \in S$
now, we update the algorithm.
Given two possibilities, each with probability 0,5 and reward 1:
$\displaystyle V^\pi (s) = 0.5 (1+\gamma V^\pi(s')) + 0.5 (1+\gamma V^\pi(s'')) = 1$
Now we take 1 and we assgin $V_1^\pi(s) = 1$ 

It makes sense because the V function must satisfy the Bellman Equation, so they must be the same.

Finding solution with this is possible, but it takes ages. We stop when the difference between iteration is less than $\epsilon$ -small enough-

We now know, for every state and action, the reward if we play according to $\pi$ . What happens if we create another policy $\pi^\prime$ such that is the same of $\pi$ BUT for a specific state? The expected return is already known, since we already computed the policy evaluation.
# Reasoning
The MDP is a tuple containing all state, action, transition and reward functions. The computation of the value of a given policy is linear, and for this reason there will be a global minimum.
### Value Iteration
Given the Bellman equation:
$\displaystyle V^\pi(s) = \sum \delta(s|\pi(s), s^\prime)(r+ \gamma V^\pi(s^\prime)$

It's a recursive relation. 
There are a lot of matrix operations to do that, and we also need to know all probabilities and rewards. For this reason we evaluated an iterative approach, and the idea is that, 
- for a given $\pi$, we start for a random V value.
- We scan the states, and for this reason we can update the V value.
- We will continue iteratively, until we find a minimum. 
It's a markovian process, so a tuple $<s, a>$ will always give out the same output state, regardless of the history

The training is based on **Policy Improvement** and **(iterative) Policy Evaluation**. Evaluation steps are computationally hard, which gets you to an idea. The policy evaluation calculated is an approximation of the actual value, which will get better for every iteration. It makes sense to do policy evaluation on one single step!

Given that we know the optimal $V^*$ function and given a state $s$, we know that we can select the action which maximises the expected outcome.  
#### Update rule for value iteration
$\displaystyle V(s) = \max_a \sum_{s^\prime} \delta(s|\pi(s), s^\prime)(r+ \gamma V^\pi(s^\prime)$
# Learning
Is the part in which we don't have an entire knowledge on the MDP, and $\delta$ and $r$ are unknown. We will have a fully observable environment, in which the agent is operating in. The agent can observe and act, and whatever action acted on the environment will give us a reward.
We can _learn_ the transition function, by acting and observing the outcome, and we can do similarly over the reward. 
### Example on One-state markov decision processes (MDP)
we have $M = (\{s_0\}, A, \delta, r)$ where
- $S = \{s_0\}$
- $A = \{a_1, ..., a_n\}$
- $\delta(s_0, a) = s_0 \ \forall a \in A$ 
- $r(s_0, a, s_0) = r(a)$ 
It's a sort-of slot machine, in which every action will take you to the same initial state, with $a_i$ indicating a lever $i$, each giving a specific reward. If we don't know the reward function the only thing we can do is check them all. 
If r is non-deterministic and unknown we need to iterate over the various actions
```
init data struct (array containing average reward for all actions)
for t = 1, ..., T
	choose action a in A
	execute a and collect reward
	update struct
pi(s_0) = argmax r(a) according to struct
```
The more iterations in $T$, the better.

## Differences 
we still have our target function, which will give us the best action based on our state. But:
- Reifnorcement training example has form $(s_1, a_1, r_1), ...,(s_t, a_t, r_t)$
- Supervised learning example have form $(s_i, \pi(s_i))$ 
- The samples of the optimal policy will not be available!

a noi ha chiesto:
- cos'è la V-function
- ⁠cos'è la Q-function
- ⁠come sono legate le due equazioni
- ⁠cos'è un ambiente stocastico
- ⁠cosa cambia se applico una formula deterministica ad un ambiente non deterministico
- ⁠differenza principale tra Q-learning e DQN
## V-Function
La V-Function is one that maps a state, and the policy, to the cumulative reward $S \times \pi \rightarrow R$, where S is a given state to start with, $\pi$ is the policy, and $R$ is al of the collected reward to go from $s$ up until one of the ending states. This can be used in the case in which we know the reward function and we know that actions produce deterministic effects.

## Q-Function
The Q-Function is a 2-dimensional array $S \times A \rightarrow R$ which is useful when you don't know the reward function, and for this reason you need to keep track of what is the most high-yielding action for any given state. The Q-Function can be updated in various ways:
- $Q(s,a) = Q(s,a) - \alpha (r + \gamma * \max_a Q(s', a') - Q(s,a))$  \[Non-deterministic, our case since we don't know how the other ]
- $Q(s,a) = r(s,a) + \gamma \max_{a^\prime} Q(\delta(s, a), a^\prime)$ \[Deterministic]
Is used to represent the expected reward on executing an action given a state.
### How are these two things related?
the V-function can be used only if we already have a policy, and for that reason we already know all of the reward and the state-action, since $V^\star$ is known, and the optimal policy will be $\pi^\star = argmax_{a \in A} = r(s,a) + \gamma V^\star(\delta(s,a))$ .
If we don't know the rewards we can just "probe" for everything, and at that point $\pi^\star = argmax_{a \in A} Q(s,a)$
### What happens if I apply a deterministic formula into a nondeterministic case?
just theoretical questions, how to apply theory to practice. It doesn't work, because in nondeterministic scenarios we don't know what's the state that we get in if we apply an action over a state. This means that we will not converge.
### Q-Learning and DQL
The main differences in how they work is based on the fact that in Q-learning, to know the best strategy, we need to try every single state-action tuple, and in the case of non-deterministic also we need to try it multiple times. This, especially in big models, makes the learning exponentially more time-consuming. The idea on DQL is to apply deep learning technology to _learn_ the Q-table by observing its behaviour, and do some "bets" on it.


# Tabular VS DQN
The DQN is like tabular, but has 2 advantages:
- Can generalize over unseens paces;
- Can work also in continuous space;
Tabular plus:
- No overhead in managing data;
- Space and time efficient;
- Simple to program;