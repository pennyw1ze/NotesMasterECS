### CFU

9 CFU =

3 CFU for a **project**, 2 ours a day in a week project in couples for machine learning
+
6 CFU **written exam** on Artificial Intelligence

---
# Introduction

AI is about devising agents (programs or devices) that acts intelligently.
The definition is just blurry because we do not know what intelligence actually is.
How to test if an entity is an artificial intelligence ?
##### Turing test
To state if we are talking about an artificial intelligence, we need to perform a test to state if a machine is acting as a human. I put a wall between a man and an entity. The man does not know if on the other side of the wall there is a human or a machine. Now, if you are not able to tell if the entity on the other side of the wall is a machine or a human, the entity passed the test, and the program is defined as intelligent.

##### Act ractionally
Is the main approach because it also involves all the other approaches. It measures if a machine acts as to achieve the best result (?) #checkathome

## Formal definition
Devising agent that behave so as to maximize an objective function.

### Machine learning
It is an area of AI, which has grown so much that has become separate from AI.

## Inductive vs Deductive AI

**Deductive** start from general rules, facts, requirements, and deduce from knowledge, throught some form of reasoning, including probabilistic reasoning. Basically makes strict logical inference.

**Inductive** approach, starting from collected data, learn input output relationship underlying data, and use learned lwas for your purposes. Basically it means learning from data patterns.

## History

Official birth of AI: '56.
First artificial neurons in the middle of 50s, then, since there was not enaugh computational power, there was a loss of interest in this subjects. The interest has grown again when people started to see results. A huge problem was the data, not digital, and where physically provided and manually digitalized. Thanks to big data, we started to make systems learns very fast.

### Today
Autonomous vehicles, automated translation, legged robots, game playing, medicine, image recognition.
AI can't proove anything, can hardly explain output decisions, can't behave ethically, can't distinguish right, wrong, ecc. 

---
# Agents

Ai is about agents, as we told. So now we need to define agents.
An agents is basically an entity (a  program, a device), that can percept something from an environment, and can perform actions in an environment.
We can also act on ourself as part of the world.
Example: enviroment: chess board, agent: moove pieces, access board states.
An agent can perceive through sensors and act through actuators.
Chess rational agents: rewards for capturing piece (+10), penalty for loosing, high reward for checkmating opponent, 0 for tie, ecc. With this rules, we can ask an agent to optimize this values.
How to design agents to deliberate correct actions?
First of all, we must reason on the nature of the environment. Different environments leads to different approaches.
Question: How to design (and realize) agents able to deliberate so as to maximize
their utility?
We can start visualizing logic-based agent.

## Wumpus World
We have a square 4 $\times$ 4, where each box represents a state. In the entire map the spawn is always in the 1 $\times$ 1 cell. There are pits (holes) in wich we do not want to fall, threre is a wumpus (random spawn), there is the gold wich we want to find, we can shoot an arrrow which is going to kill a wumpus if it is in the direction of the arrow, and the wumpus is surrounded by cells with smell, and pit are surrounded by cells with breeze.
Now we want to model this problem with logic.
In mathematical terms, we could assign costs and profits for killing, finding gold, falling in pits ecc. and try to optimize this function. Let's see what we can do in Logic.

---
# Logic

## Propositional logic
Logic of proposition and sentences.  We must provide:
- **Syntax:** how to compose a sentence;
- **Semantics:** meaning of a sentence;
- **Inference:** how can we infer new knowledge;

### Syntax
The syntax is composed by an alphabet.
An alphabet is a countable set of atomic propositions, a set of logical connectives, and other symbols such as the parenthesis symbol.
We define **Formulas** in propositional logic as a set of symbol well assembled,
Not all sequence of characters in the alphabet are formulas. We can generate new formulas by having previous ones and applying logical inferences.

If φ and ψ are propositional formulas then the following are propositional
formulas:
- ¬ψ
- φ ∧ ψ
- φ ∨ ψ
- φ ⊃ ψ
- φ ↔ ψ
- (φ)
Nothing else is a propositional formula.
Examples of formulas generated in the wumpus world field:
- s1,2 - there is stench in [1,2]
- s1,2∧p3,3 - there is stench in [1,2] and a pit in [3,3]
- ecc...

All the operators have a precedence order, which is the following:
1. ¬
2. ∧
3. ∨
4. ⊃
5. ↔

### Semantics
Semantics defines when a formula is true or false, depending on whether the
propositional symbols it contains are true or false
In order to assign a truth value to a formula, we need to know the truth value of his symbols.
To assert the truthfulness of a formula in propositional logic, we need an interpretation.
An interpretation is a law I that assigns a truth value to each proposition which is syntactically valid in the lenguage.
Propositional interpretations are sometimes called propositional models
For example, in the wumpus world, we can assert that:
$I(s_{1,2}) \implies I(b_{2,2}) = F; I(b_{1,2}) = T$
We define when a formula φ is true under an interpretation I (written I ⊧ φ)
inductively as follows:
- if φ is an atom, then I ⊨ φ iff φ∈I
- if φ=¬ψ then iff I⊭φ
- if φ=ψ∧γ then I ⊨ φ iff I ⊨ ψ and I ⊨ γ
- if φ=ψ∨γ then I ⊨ φ iff I ⊨ ψ or I ⊨ γ
- if φ=ψ⊃γ then I ⊨ φ iff I ⊭ ψ or I ⊨ γ ( this is a slightly different semantics to say: $γ \implies ψ$);
- if φ=ψ↔γ then I ⊨ φ iff I ⊨ ψ⊃γ and I ⊨ γ⊃ψ
- if φ=(ψ) then I ⊨ φ iff I ⊨ ψ
When I ⊨ φ we also say that I satisfies φ and call I a model of φ.
Also, a literal p is true if $p \in I$, and false if it is not in I. 
We can also define a semanthics using the truth tables.

Let's now define the wumpus world propositional logic base:
px,y
- pit in x,y
wx,y
- Wumpus in x,y
bx,y
- breeze in x,y
sx,y
- stench in x,y
lx,y
- agent in in x,y
for x,y ∈ {1,2,3,4}

