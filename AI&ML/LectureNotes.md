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

Basics on satisfiability and unsatisfiability ( We now ).
**Validity**: all the possible interpretations of a formula must satisfy it.
To check if a pit is in a certain cell, we need to check if the conditions imposed by the semantic of the logic are satisfied or not. To do this, we accumulate a knowledge base, storing all the experience and the inference that we are able to collect during our journey in the Wumpus world.
To automatically check satisfiability we need an algorithm to solve sat.

### Logical implication
We says that φ logically implies ψ (φ⊨ψ) if every interpretation that satisfies φ satisfies also ψ.

##### **Properties of Propositional Logical Implication**

Reflexivity: if φ∈Γ then Γ⊨φ
Ex falso (sequitur) quodlibet: if Γ is unsatisfiable then Γ⊨φ for every φ
Monotonicity: if Γ⊨φ then Γ∪Γ'⊨φ
Cut: if Γ ⊨ φ and Γ'∪{φ} ⊨ φ' then Γ∪Γ' ⊨ φ'
Compactness: if Γ ⊨ φ then there is a finite Γ'⊆ Γ s.t. Γ' ⊨ φ
Deduction Theorem: if Γ∪{φ} ⊨ φ' then Γ ⊨ φ→φ'
Deduction Principle: Γ∪{φ} ⊨ φ' iff Γ ⊨ φ→φ'
Refutation Principle: Γ ⊨ φ iff Γ∪{¬φ} is unsatisfiable

### Expansion rules

| ψ∧γ |
| --- |
| ψ   |
| γ   |

| ψ∨γ |     |
| --- | --- |
| ψ   | γ   |

ecc...

The goal of the expansion rules is to reach a situation in which all the or, and, or other symbols are cancelled and simplified, and the formula is now reduced to raw literlas.
In this way, we can see if the formula is satisfiable by just checking for a contraddiction, ergo the same literal must be set to true and false to satisfy the formula.
The tableaux are built in such a way, reducing a formula to literals.
Once we expanded the nodes to all possible branches, we can check if the formula is either satisfiable or not.
A tableaux branch is closed when all the formulas are expanded.
Logical implication:
REFUTATION PRINCIPLE:
$\Gamma \models \varphi$ iff {$\Gamma\ \cup$ $\neg \varphi$} is unsatisfiable

### Tableux
Is a technique that expands a logical formula, generating 2 branches if there is an or, or 1 branches that has 2 raw.
There is a priority order in which we have to expand the single formulas.
This procedure generates a tree. When the leafs of the tree contains only literals, ($a, \neg a$), then the computation has ended.
The formula analyzed is satisfiable if there exists at least one brench with no contraddictions ( on the brench there is not a literal and his negation).

### DPLL
Devis putnam.
Algorithm to solve CNF formulas (Conjunctive Normal Form).
Four types of decision problems Model Checking(I, φ): 

Satisfiability(φ):
∃I . I |= φ Is there a model of φ?

Validity(φ):
|= φ Is every interpretation for φ a model of φ?

Logical implication(Γ, φ): 
Γ|= φ Is every model of the set of formulas Γ a model of φ as

Logical inference(Γ, φ): 
$\Gamma ⊢_Σ φ$ Is there a proof of φ in Σ from Γ ?

**Tseitin’s transformation** converts any propositional formula φ into an equi-satisfiable formula φ in CNF with only a linear increase in size.

For every formula ψ, T(ψ) is satisfiable if and only if ψ is. Moreover, T(ψ) is 3-CNF formula
whose size is linear with respect to the size of ψ.

We still need to understand what CNF does. We proceed to analyze all cases.
CNF(q ≡ ¬p) = (¬q ∨ ¬p) ∧ (p ∨ q)
CNF(q ≡ (p ∧ r)) = (q ∨ ¬p ∨ ¬r) ∧ (¬q ∨ p) ∧ (¬q ∨ r)
CNF(q ≡ (p ∨ r)) = (¬q ∨ p ∨ r) ∧ (q ∨ ¬p) ∧ (q ∨ ¬r)
CNF(q ≡ (p → r)) = (¬q ∨ ¬p ∨ r) ∧ (p ∨ q) ∧ (¬r ∨ q)
CNF(q ≡ (p ≡ r)) = (q ∨ p ∨ r) ∧ (q ∨ ¬p ∨ ¬r) ∧ (¬q ∨ p¬r) ∧ (¬q ∨ ¬p ∨ r)

The DPLL procedure is composed of different phases:

```pseudocode
UnitPropagation(φ, I)
while φ contains a unit clause {λ}
if λ = p, then I(p) := true;
if λ = ¬p, then I(p) := f alse
φ := φ|λ;
end;
return (φ,I)
```

If the procedure returns $(\{\},I)$, the formula is satisfied by the (partial) interpretation $I$.

```pseudocode
DPLL(φ, I′)

(ψ, I) := UnitPropagation(φ, I′);
if ψ contains {}
then return ({{}}, ∅)
elseif ψ = {} then return ({}, I)
else select a literal λ ∈ C ∈ ψ;
if DPLL(ψ ∪ {{λ}}, I) = ({}, I′′)
then return ({}, I′′)
else return DPLL(ψ ∪ {{¬λ}}, I)
```

##### Example of exam exercise

$$\phi = \{\{p,q,r,s\}, \{\neg p, q, \neg r\}, \{\neg q, \neg r, s\},\{p, \neg q,r,s\},\{q, \neg r, \neg s\},\{\neg p, \neg r, s\},\{\neg p, \neg s\},\{p, \neg q\}\}$$
DPLL first round:
1. No unit to propagate;
2. Pick a random literal $(\neg q)$, and append it to the formula $\phi$. Run again DPLL;
3. Run till you reach and end clause ({{}}, $I$) or ({},$I$) or another.

##### Exam exercise

$$\phi = (a <- b) \wedge (\neg c \vee d) \wedge (\neg e \vee \neg f) \wedge (e \vee \neg f \vee \not b)$$
is $\phi$ valid ?

Check if $\exists$ an interpretation $I$ that satisfies $\neg \phi$.

$(\neg b \wedge a) | (c \vee \neg d)\ ecc...$
satisfied on left branch (no contraddiction)
So $\phi$ is not valid, because our partial interpretation $(a,\neg b)$ staisfies negated phi $(\neg \phi)$.

## First order logic (FOL)

Elements of the first order logic:
- **Terms:** costants or variables, like Mary, John, 1, 2, blue, ecc...;
- **Functions:** fatherOf(), motherOf(), ecc...;
- **Predicates:** Mortal, Prime, ecc...;
- **Quantifiers**: like $\exists,\ \forall$ ecc...;

The interpretation is providing a meaning.

EX:

F(x,y) is a predicate: F/2 takes 2 arguments
I(F) $\in \Delta^2$.
I(F) = {<s1,s2>,<s2,s1>}
end of example

Assignments is an set $\alpha$ that relates all the variables to a value.

#### Free Variables:
Variables are free if they appear in at least one clause not related to an existential or universal operator ($\exists , \forall$).

#### Formulas definition:
A formula φ is **ground** if it does not contain any variable.
A formula is **open** if it contains at least one free variable, **closed** otherwise.

$\forall x.course(x)\exists y,z.student(y) \cap student(z) \cap attend(y,x)\cap attend(z,x) \cap \neg y = z$.
A student can attend at most 2 courses:
∀x∀y∀z∀w(attend(x, y) ∧ attend(x, z) ∧ attend(x, w) →
(y = z ∨ z = w ∨ y = w))


At least n students:
$\exists x_1,...,x_n ( \cap_{i=1...n}\phi(x) \cap \cap_{i\neq j = 1...n}x_i\neq x_j$)

At most n students:
$\forall x_1,...,x_{n+1}.(\cap_{i=1...n+1}\phi (x) -> \cup_{i \neq j = 1...n+1} x_i = x_j)$

### Tableau and FOL
We've seen how to expand propositional logic formulas with tableux, but we do not know how to expand existential and universal quantifier.

We can translate this formulas as follows:

$\neg \forall x \phi(x) = \exists x.\neg \phi(x)$
$\neg \forall x \phi(x)$ can be expanded in tableux as $\neg \phi(x)$

When we expand the existential quantifier, you can insert that the condition expressed by it is valid only for one object in the domain, while when you expand a universal quantifier it must be kept in mind that the constraint is valid on every object of the domain.

EX:
- Test validity (which is equal to check for unsatisfiability);
- Apply tableux;

$\neg ( (\exists x.P(x)\cup Q(x)) \iff (\exists x.P(x)\cup \exists x.Q(x)) )$
---
EXPAND:

1. $(\exists x.P(x)\cup Q(x))$
2. $\neg (\exists x.P(x)\cup \exists x.Q(x))$

OR

3. $\neg (\exists x.P(x)\cup Q(x))$
4. $(\exists x.P(x)\cup \exists x.Q(x))$
---
APPLY RULES


1. ->BETA RULE
$(P(x) \cup Q(x))$
-> DELTA RULE
P(b)
In this case I cannot choose a again to create a contraddiction so I cannot close the brench.


2. -> ALPHA RULE
$\neg \exists x.P(x)$
$\neg \exists x.Q(x)$
-> GAMMA RULE
$\neg P(a)$

---
OTHER BRENCH
ecc...

---

# FOL Queries

We use the formula as a query, and the interpretation as a database.
A FOL boolean query is a FOL query without free variables.
The answare is true when the query contains the empty tuple, and false otherwise.
Query evaluations makes sense if the interpretation $I$ is finite.

EX:
$\phi(x) = \exists y.Person(x,y)\cap Lives(x,ny)$

---
# Wumpus
Setting up wumpus in FOL.

## Rules
Let's define the rules of the game.
#### Wumpus
$\exists x.Wumpus(x)\cap Square(x)$
$\forall x\forall y.Wumpus(x)\cap Wumpus(y) \implies x=y$$\forall x.Wumpus(x)\cap Square(x) \implies( \exists y.Right(x,y) -> Stench(y)\cap \exists y.Left(x,y) ->  Stench(y)\cap \exists y.Up(x,y) -> Stench(y)\cap \exists y.Down(x,y) -> Stench(y))$
---
# Planning
## Classical Planning

|       |     |     | $\star$ |
| ----- | --- | --- | ------- |
|       |     |     |         |
|       |     |     |         |
| Agent |     |     |         |
Actions:
up, down, right, left

Properties:
- Positions of the agent; (fluent)
- Position of the star; (not fluent)
- Structur of the grid; (not fluent)

> [!NOTE]
> We call **fluent** all the elements which position can change

We call this elements **Domain Definition Lenguages**
One of the most used is **PDDL**.

An example of classical planning is Rubik cube.

### State space
State can be finite or infinite.
$S = \{s_1,s_2,...,s_n\}$
### Action
To move between states
$A = \{a_1,a_2,...,a_n\}$

### Transaction
Transaction functions
$\delta :S\times A -> S$
So we will have $\delta (S,A) \in S$

Ex:

|       |     |     | $\star$ |
| ----- | --- | --- | ------- |
|       |     |     |         |
|       |     |     |         |
| Agent |     |     |         |

Actions: Right ->

|     |       |     | $\star$ |
| --- | ----- | --- | ------- |
|     |       |     |         |
|     |       |     |         |
|     | Agent |     |         |

We will create a table for our function, that given a state and an action will give us the resulting state in which we will end up.
Since $\delta$ is a funciton, the state resulting from an action could be just one.
We define a goal set:
a set of final status in which we want to end up.
We can also define a **PLANNING PROBLEM**
A planning problem is composed of:
- D a domain( D is composed of <(S,A,$\delta$)>;
- $S_0$ an initial state;
- G a set of goals;

> [!NOTE]
We add $\alpha$ to define a set of actions that can be executed from a certain state, since is not possible to execute the same set of actions from every cell.

If we find a set of actions that are valid and bring us from the initial state to the desired state is called a (solution) **PLAN**.

To solve the problem, we can turn the gird into a graph. 
Each node is a state, each edge connects the states which are reachable and corresponds to an action.
I start from the initial state and keep a fronteer variable. The fronteer contains all the neighbors of the visited states.
If a goal state is in the fronteer, I reach it and I found a valid plan, otherwise I continue the exploration.

We start with a fronteer, a set of pairs, plan and state. A plan is  a set of actions that brought us from $S_0$ to $S$.

##### Flaws:
- We have to save the whole space in memory;
- We have a random algorithm that picks a state from the fronteer without a specific criteria.

#### Improvements
We could use a known algotithm to pick state from fronteer that takes into account also the states previously explored.
Let's think about a **BFS**.
We want a function that given a set of states and plans, tells us which is the most desirable couple state plan to pick.
In the BFS case, the most desirable plan is the shortest path.
This algorithm is called **Best First** search.


### Properties
Specify 2 properties for this algotihms:
- **Soundness:** If the algorithm gives an output, the output is a solution to the problem;
- **Completeness:** if exists a solution to the problem, the algotihm returns it;
- **Optimality**: the returned plan is guaranteed to be optimal wrt a given measure.

Instead of the max, we will use the minimum as a criteria function to choose for the next state.

## Informed search
A search when the functions that picks states from the fronteers actually uses some knowledge cumulated during the explorations.
Example of function:
Evaluate the number of steps missing to reach the goal state(s).
Let's now consider a situation in which we have obstacles in the grid.
We create an evaluation function that computes the cost of a state, which is the number of steps needed to go from the initial state to a certain state following a certain path.
For example, if goal state is S3 and it takes 2 steps to reach it from S1, then our function g will say:$$g(S_1)=2$$images
So we must drop all the path that leads us to the same state as other paths but with more steps than other paths that we found in the past.
We also introduce another function h to extimate the cost of reaching the goal from a certain state.
And we create a function f to evaluate the best state to continue the path which is defined as follows:$$f(n)=g(n)+h(n)$$
Pruning: throw away an entire part of the expansion tree if we see that it reaches a state in more steps then another path.
So in the new algorithm I will remove from the fronteer all the nodes which have higher costs than other nodes with an inferior cost to reach the same state.

##### So:
We will expand at first the state with the lower euristic funtion h, so the state which we think is the closer to the goal state.

> Admissible heuristic function: an heuristic function is admissible if it does never extimate the cost of reaching a goal state.

If A* uses an admissible heuristic function then it is optimal.

About the heuristic function h, we say that if we have h1 and h2, and h2>h1, since h1 < h2 < step to reach goal state, we have that h2 is better then h1.

Remember that we are working under the CLOSED WORLD ASSUMPTION.

---
# PDDL
Planning Domain Definition Lenguage

There exists 2 languages:
- STRIPS (STanford Research Institue Planning System).
- ADL (Action Description Lenguage);

Let's study a new problem:
##### BOX world
STRIPS representation of the problem:
( define (domain BlocksWorld)
  (:requirements :strips :negative-preconditions)
  (:predicates 
    (on ?b1 ?b2 - object) 
    (onTable ?b - object)
    (clear ?b) // We avoid to write object, it is implicit
   )
   (:action moveFromTable
	:parameters (?b1 ?b2)
	:precondition (and (clear ?b1) (clear ?b2)) // The blocks must not have any other blocks // on top of them
	:effect (and (on ?b1 ?b2) (not onTable ?b1) (not clear ?b2))
   )
)
Everything not mentioned  does not changes

Now we can try to describe the box world problem:

(:define (problem boxworld)
(:domain BlocksWorld)
(:objects A B C)
(:init
	ontable A
	ontable B
	ontable C
	clear A
	clear B
	clear C
	)
(:goal and
	on (C B)
	on (A C)
	)


)

So we will have a domain D = (S,A,$\alpha,\delta$)
S is a set of ground facts. Example: on table a or on clear a.
A is a set of all possible ground actions.
$\alpha (S)=\{a\in A | s \models precondition(a)\}$
$\delta :S\times A\rightarrow S$
So this is a compact way to represent the domain.
In classical planning you can also use the universal and existential operators.

---
# Fully Observable Non Deterministic (FOND) planning

When we moove from a state, performing an action, we do not know precisely in what state you will end up.  
So $\delta$ is not a function anymore, besides it is a relation in the sense that for an element in the domain there are multiple corresponding elements in the codomain.
$\delta (S,a) = \{S,S',S''\}$
$\delta :S\times A \rightarrow 2^S$
We have to define a **policy**, a table that maps actions from a determinate state.
We create a tree of possible actions, listing the possible state after executing an action from a state, and we discard path where loops occurs since we risk to end up in an infinite loop which will not lead us to a goal state.

### And-Or-Search

Policy AO-SEARCH(D = <S,A,$\alpha,\delta$>, $S_0, G \subseteq S$){
	return OR-SEARCH(D,$S_0$,G,$\epsilon$);
}

Policy OR-SEARCH(D,$S$,G,$\sigma$){
	if S $\in G$ return $\Pi _0$;
	if S occurs in $\sigma$ return fail;
	for every action a $\in \alpha(S)$
		$\Pi$ = AND-SEARCH(D,$S$,a,G,$\sigma$ extended with s');
		if($\Pi \neq fails$ ) return $\Pi \cup$ {<S,a>};
}
return failure;

Policy AND-SEARCH(D,$S$,a,G,$\sigma$){
	$\Pi$ = empyt policy;
	for every s' $\in \delta(S,a)${
		$\Pi$' =  OR-SEARCH(D,$S$',G,$\sigma$ concatenated to a);
		if $\Pi$' = fail return fail;
		$\Pi \cup= \Pi'$
		}
		return $\Pi$;
}

# Non Deterministic Planning
**How to represent domain, in a compact way?**
we'll introduce a variant of PDDL such that we can express non-deterministic actions, using the `oneof` key. 
- **oneof**: allows to specify a set of possible effects.
- `oneof(e1 ... en)`, where each $a_i$ is a deterministic effect. 
  After executing the action, one and only one effect will be applied, which is not known before the execution.

We'll use back the BlocksWorld domain
```
(define (domain BlocksWorld)
	(:requirements :non-deterministic :equality :typing)
	(:types block)
	(:predicates
		(holding ?b - block) ; arm holding block ?b
		(emptyhand) ; arm is not holding anything
		(clear ?b - block) ; block ?b not having blocks on top
		(on-table ?b - block) ; block ?b is on table
		(on ?b1 ?b2 - block) ; block ?b1 is on top of ?b2
	)
	
	; Action list
	
	(:action pick-up
		:parameters (?b1 ?b2 - block)
		:precondition (
			(and 
				(not (= ?b1 ?b2))
				(emptyhand)
				(clear ?b1)
				(on ?b1 ?b2)
			)
		)
		:effect ( ; block may slip off of the arm and fall
			oneof(
				and( ; effect accounting of successful execution
					(clear ?b2)
					(holding ?b1)
					(not (emptyhand))
					(not (on ?b1 ?b2))
					(not (clear ?b1))
				)
				and( ; effect of failing execution (block falls on table)
					(clear ?b2)
					(not (on ?b1 ?b2))
					(on-table ?b1)
					; emptyhand and clear ?b1 DID NOT CHANGE from precon
				)
			)
		)
	)
	
	(:action pick-up-from-table
		:parameters (?b - block)
		:precondition(
			and( (on-table ?b) (emptyhand) (clear ?b))
		)
		:effect(oneof
			(and ) ; unsuccessful event
			(and (not (clear ?b)) (not (emptyhand)) 
				(holding ?b) (not(ontable ?b))
			)
		)
	)
	
	(:action put-on-block
		:parameters (?b1 ?b2 - block)
		:precondition ( and( (holding ?b1) (clear ?b2)) )
		:effect(oneof
			(and (on ?b1 ?b2) (clear ?b1) (not (clear b2)) 
				(emptyhand) (not (holding ?b1)))
			(and (on-table ?b1) (clear ?b1) (emptyhand) (not(holding ?b1)))
		)
	)

	(:action put-down
		:parameters (?b - block)
		:precondition ( (holding ?b) )
		:effect ( and (ontable ?b) (emptyhand) (not (holding ?b)) (clear ?b) )
	)
)
```


A:
- Parameters
- Precondition
- effect
Same as in classical planning;

---
# Situation calculus

FO lenguage to specify(deterministic) dynamic domains.
Functions in FOL.

Functions symbols:
- Symbols representing functions: parent-of/1;

Terms: either a variable v or a constant c or a function term.
function term: if f/n is an n-ary function symbol then f(t1,t2,...,tn) where ti is a term, is a term.
Interpretation I is a pair $<\Delta,I>$function s.t. for every constant c,: I(c) $\in$ I.
For every predicate P/n:
I(P) $\subseteq \Delta ^n$ 


For every function symbol f/n:
$I(f) = f^I$ where $f^I:\Delta^n \rightarrow \Delta$ 
Notice that is a TOTAL function.

## Sorted language
Feature of situation calculus, which has object sorts (kind of as types).
Object domain can be partitioned into three subsets:
$\Delta = (Objects, Actions, Situations)$
- Actions: Represent the actions that can be executed in the domain
- Situations: Represent possible world histories
### Actions
We need syntax to denote features: we'll use Action Types, which corresponds to PDDL schemas. $A = \{A_1/a_1, ...,  A_n/a_n\}$
Example:
- $A = \{put-on-table/1, pickup-from-table/1, put-on-block/2 ,pick-up/2\}$
$pick-up(b_1, b_2)^I = act_4$#

### Situations
World history
do: function to do actions.

- $S_0$ Initial situation, where no action has been performed.
- do(a,s) where a is an action term and s is a state.


EX:

S0: all blocks on the table.

Action: pick-up(b1),$S_0$;


$\Sigma$ Set of constraints, foundational axioms of Situational Calculus.
	
We have a tree of state.

Fluents are predicates whos values can change from situations to situations.

Actions are represented with calligrafic letters.
For example $\mathcal{A}$;
Functions example: do(a,s) -> Do action a from status s, resulting in a new state.

PRECONDITION

Poss(a,s): specifies when action a is executable in situation s.

Exercise:
represent initial state:
$D_0 = \{AgentAt(A,S_0)\}$
Where A is the first cell.
$S_0$ Is the initial SITUATION, not state.
We need to write a constraint to disallow agent to stay in more then 1  cell at the same time.
$$\{AgentAt(A,S_0) \wedge \forall x(AgentAt(x,S_0) \implies x = A)\}$$
We needs to state the every item is either a  triangle or a circle:
$$\forall x .itme(x)\iff (x = circle \vee x = triangle)$$
We define right:$$\forall x_1,x_2.right(x_1,x_2)\iff(x_1 = A \wedge x_2 = B) \vee (x_1 = B \wedge x_2 = C) \vee (x_1 = C \wedge x_2 = D)$$
and the exit:$$exit(x)\iff x = D$$
$$\forall x.\neg AgentHas(x,S_0)$$
$$\forall x.ItemAt(x,y,s_0) \leftrightarrow (x=\bigcirc \wedge y = B) \vee (x=\bigtriangleup \wedge y = C)$$

This is the knowledge base that specify the initial situation.

### Precondition Axioms
move(x) can be executed if agent is in cell y s.t. x is next to the right of y.
$$Poss(move(x), S_n) \leftrightarrow \exists y.AgentAt(y, s) \wedge Right(y,x)$$
$$Poss(\forall s.drop(x,s)$$
We can exit if we are on an exit cell and we have all items:$$Poss(exit(),s)\iff \exists y.AgentAt(y,s)\wedge Exit(y) \wedge \forall z.item(z)\implies AgentHas(z,S_n)$$

---
Recap:
We define an initial set of formual that defines our state and does not allow any fluency.
For this reason we defines Poss (preconditions) and formulas as constraint on the predicates that restrict their effect to their semantics.
What we defined in the example above is also called the set of formulas that defines the initial situation (theory of initial situation).
We call $D_{AP}$ the set of action-precondition Poss constraints formulas.

### Effect Axiom

Now we have to write an effect axiom (we defined constraint and precondition, now we describe the effects of a funciton).

For example, if I move(x) in s', I will have that AgentAt(x,s').
How do I write it ?$$\forall x\forall s.AgentAt(x,do(move(x),s))$$
But we also have to specify the fact that we are leaving the actual cell in which we were before moving.
So we have to add a condition:$$\neg AgentAt(y,s)$$
supposing that the agent was in the cell y before moving.
When the agents leaves a cell, he gets all the items that where in the new cell, and bring with himself all the items that he had. $$\forall x\forall y\forall s.ItemAt(x,y,s) \rightarrow AgentHas(x,do(move(y),s))$$$$\forall x\forall y\forall s.\neg ItemAt(x,y,do(move(y),s))$$
We have also to specify what does not changes.
For example, what was already in the bag must remain there, and if we do not specify that, the AI will not know that it keeps there.
Since there are a lot of stuffs that remains unchanged, situation calculus has a succint way to specify this.

We say **NORMAL FORM** of a clause is the following:
a = move(x)
... Some clause... -> AgentAt(y,do(a,s));

So for example we convert $$a = move(x) \wedge \forall x\forall y\forall s.ItemAt(x,y,s) \rightarrow AgentHas(x,do(move(y),s))$$
we just added a = move(x).
Here:
$$\exists y.a = move(y) \wedge \forall x\forall y\forall s.ItemAt(x,y,s) \rightarrow AgentHas(x,do(a,s))$$
and here we normalize:
$$a = move(y) \implies\forall x\forall y\forall s.\neg ItemAt(x,y,do(move(y),s))$$

Now, effect axioms for action drop:

1. $$a = drop()\implies \forall x.\neg AgentHas(x,do(a,s))$$
2. $$a = drop() \wedge AgentAt(x,s)\wedge AgentHas(y,s)\implies Item(x,y,drop(a,s))$$
End for exit:
1.$$a = exit() \wedge AgentAt(x,s)\rightarrow \neg AgentAt(x,do(exit(),s))$$
In this Effect axioms we are not specifying what does not changes.

But there would be a lot of stuffs to specify in this case!
We will specify what changes, and assume that all the rest does not change. How ?
start having a normal form:
#### Normal form:
- right side:
	atomic formula mentioning 1 fluent and the result of executing an action on situation s;
- left side:
	condition under witch the fact becames true or false;
We replace all the actions inside the do() predicate/function into a, for example like this:
$\neg A(y,do(Act(x),s)) = (a=Act(x)\rightarrow \neg A(y,do(a,s)))$
Notice that, we specific transformations, we can always turn an effect axiom into a normal form like ($\forall$ statements are implied):$$\exists x.(A(y,s)ecc...a=Act(x)\implies \neg A(y,do(a,s))$$
To solve our previous problem, now we introduce:
### Successor state axioms (SSA):
How to construct them?
We take all the effect axioms that affects the fluents (for example move(x)) affects AgentAt(x) and also exit() affect AgentAt.
First, if we have:
$\neg AgentAt(x,s) \wedge AgentAt(x,do(a,s))$
then we know that a is move.
$\neg AgentAt(x,s) \wedge AgentAt(x,do(a,s)) \implies a=move(x)$
$a = exit() \wedge AgentAt(x,s) \implies \neg AgentAt(x,do(a,s))$
So we know that if AgentAt has been affected, either the agent has moved or it has exited.
We can take the $\gamma$ conditions of the move or exit statement and say that:$AgentAt(x,s) \wedge \neg AgentAt(x,do(a,s)) \implies \gamma_1^-(x,y,a,s) \vee \gamma_2^-(x,a,s)$
where $\gamma_1^-$ is the condition for move and $\gamma_2^-$ is the condition of exit.
We call this axioms the **Explanations closure axioms**.
#### Integrity of effect axioms:
We place constraints for example:
Unique names for actions:$$A(x_1,...,A_n) = A(y_1,...,y_n) \implies x_1=y_1\wedge ... \wedge x_n=y_n$$
Or other costraints like:$$A(x_1,...,A_n) \neq B(y_1,...,y_n)$$
So we get **SSA** for example:$$AgentAt(x,do(a,s))\iff a=move(x)\vee AgentAt(x,s)\wedge \neg (\exists y.a= move(y)\wedge x\neq y \wedge AgentAt(x,s) \vee (a=exit()\wedge AgentAt(x,s))$$
So the agent is in x after an action only if he moved in x or he perfomed an action that does not move him or exit.

---
So we will build our effect axioms and normalize them in the form:
$\exists y_1.\gamma _1(x,y,a,s) \implies F(x,do(a,s))$
...
and many more axioms like this (we can have $\gamma^+$ or $\gamma ^-$ if we have positive or negative axioms).
We build SSAs for fluent F:
$F(x,do(a,s)) \iff \vee _i \exists y_i. \gamma ^+(x,y,a,s) \vee (F(x,s) \wedge \neg(\vee _j \exists y_j.\gamma ^-(x,y,a,s) )$

This tells us if F changes, but also if it does not changes because is an if and only if condition.

So from scratch we do:
1. Collect what Fluent changes for every actions and we write the effect axioms;
2. Then we write SSA from the fluent;


---
So basically we could describe
$D = \Sigma \cup D_{UNA} \cup D_{S_0} \cup D_{AP}  \cup D_{SSA}$
Where:
- $\Sigma$ = Fundational axioms of STATCALC;
- UNA = Unique Names for Actions;
- S0 = description for initial state;
- AP = Actions precondtions;
- SSA = Successor State Axioms;

This is a set of first order logic.
Whit the situation calculus we can do also planning.
We will see 2 tasks:
- Legality for executability: given a sequence of actions, check weathere every action in the sequence is executable (POSS is true when the action is executed);
- Protection task: given a sequence of actions $p  = a_1, a_2, ... , a_n$ and a formula $\phi(s)$ that talk about a state of situation s, check weather the following holds: $D \models \phi(do(a_n, a_{n-1},...,a_1))$ so starting from S0 you execute the actions and you end up in a state where $\phi$ still holds.

---
# Regression

$AgentAt(B, do(move(c),do(move(B),do(move(A),S_0))))$
This is the situation resulting from moving agent from A to B to C, starting from initial situation $S_0$, but is pretty long to represent.
Our aim si to produce a new formula which is equisatisfiable with respect to the previous one.
Let's see regression operator:
Take a formula$$\phi = \exists x.AgentAt(x,do(B,S_0))\wedge \exists z.RightOf(x,z)$$
$R[\phi]$
- Regression operator R;
Now we have different cases:
- If $\phi$ is a situation independent atom, e.g.:$\exists x.Right(x,y)$, then:$$R[\phi] = \phi$$
- If $\phi$ is a (relational) fluent of the formula $F(x,S_0)$, then:$$R[\phi] = \phi$$
  For example: $\phi(x) = AgentAt(x,S_0), R[\phi(x)] = \phi(x) = AgentAt(x,S_0)$.
- If $\phi = Poss(A(t),\sigma)$ and precondtion axiom $\phi \iff \Pi_A(x,s)$, then:$$R[\phi] = R[\Pi_A(x,S_0)]$$
  Example:$$\phi = Poss(move(B),do(drop(),S_0))$$$$Poss(move(x),s)\iff \exists y.AgentAt(y,s)\wedge Right(x,y)$$
  $$R[Poss(move(B),do(drop(),S_0))] = R[\exists y.AgentAt(y,do(drop(),S_0))\wedge Right(y,B)]$$
  where $\sigma = do(drop(),S_0)$ and $t = B$.
- If $\phi$ is a (relational) atom of the form $F(x, do(\alpha, \sigma))$, with $\alpha$ being action term and $\sigma$ the situation term with SSA $$F(x,do(a, s)) \leftrightarrow \Phi_F(x,a,s)$$
  Example:
  $\phi = AgentAt(B,do(move(B),S_0)), \alpha = move(B)$ and $\sigma = S_0$.
  We will have:$$R[F(x,do(\alpha,\sigma)] = R[\Phi_F(x,\alpha,\sigma)]$$
  So "do" does not occur in the new formula, which is apparently good for us!
  So, back to the example we have that:$$AgentAt(x,do(a,s))\iff a=move(x)\vee AgentAt(x,s)\wedge \neg (\exists y.a= move(y)\wedge x\neq y \wedge AgentAt(x,s) \vee (a=exit()\wedge AgentAt(x,s)) $$
  $$R[AgentAt(B,do(move(B),S_0)] = R[a = move(B) \vee \neg (\exists y.a = move(y)\wedge B\neq y \wedge AgentAt(B,S_0) \vee (move(B)=exit()\wedge AgentAt(B,S_0))]$$
  So we replace x with B, a with move(B) and s with S0 in the SSA, we will end up with the right regressive axiom.
- Then we have the following properties:$$R[\neg \phi] = \neg R[\phi]$$$$R[\phi \vee \gamma] = R[\phi] \vee R[\gamma]$$$$\phi = \exists x.\psi \implies R[\phi] = \exists x.R[\psi]$$
### Regression theorem
if $\phi$ is a regressable formula, and we want to check if it safisfies every situation, we can just check if his regression satisfies the initial situation:$$D \models \phi \iff D_{S_0} \cup D_{una} \models R[\phi]$$
because we get rid of any situation $\Sigma$, of the axiom precondition $D_{AP}$ and of the successor state axioms $D_{SSA}$.
We can get rid of them because every time we regress, we are already considering them (if we regress a formula about a successor situation we consider SSA and when we regress a formula about Poss we consider the AP).
Remember that $D_{UNA}$ is here only to remember us that different action terms refers to different actions.

---
## Regression exercise exam 10_06_2024

Check whether p = move(B)move(C)move(D) is executable in S_0 for D as above.
We need to check if:
- Move(B) is executable in S0;
- move(C) is executable in the state that follows move(B),S0
- move(D) like before;

We call:
- $S_1 = do(move(B),S_0)$;
- $S_2 = do(move(C),S_1)$;

**1st condition**
$D\models Poss(move(B),S_0)$
$R[Poss(move(B),S_0)] = Poss(move(B),S_0)$
$D\models Poss(move(B),S_0) \iff D_{S_0}\cup D_{UNA}\models R[Poss(move(B),S_0)]$
and since $R[Poss(move(B),S_0)] =Poss(move(B),S_0)\iff \exists y.AgentAt(y,S_0)\wedge Right(B,y)$
So:
$R[Poss(move(B),S_0)] = R[\exists y.AgentAt(y,S_0)\wedge Right(B,y)] =$
$= \exists y.R[AgentAt(y,S_0)\wedge Right(B,y)]$=
$=\exists y.R[AgentAt(y,S_0)]\wedge R[Right(B,y)]=$
$=\exists y.AgentAt(y,S_0)\wedge Right(B,y)]$
Now we can apply the regression theorem:
$D_{S_0}\cup D_{UNA} \models \exists y.AgentAt(y,S_0)\wedge Right(B,y)$
and since $D_{S_0}$ contains this axioms:
$AgentAt(x,S_0)\iff x=A$
and
$Right(x,t)\iff (x=A\wedge y=B) ecc...$
So the first condition is executable!

**2nd condition**
$D\models Poss(move(C),S_1)$ ?
$R[Poss(move(C),S_1)] = R[Poss(move(C),do(move(B),S_0))]$
Since we know that:
$Poss(move(C),do(move(B),S_0)) \iff \exists y.AgentAt(y,do(move(B),S_0))\wedge Right(y,C)$
So we replace into the regression formula:
$R[\exists y.AgentAt(y,do(move(B),S_0))\wedge Right(y,C)]$
And then we split all the regression like before.
$∃y.R[AgentAt(y, do(move(B), S_0))] ∧ Right(y, C)$
ecc...

---

# **MACHINE LEARNING**

example:
I give you a table of input/output data.
Then I ask what is the output on an input, and the machine returns data based on what she learned.

Usefull when:
- No experties available;
- Humans can't explain how they carry out task;
- General solution must be tuned for specific case;

#### Supervised learning
Input-output example based, the task is to learn approximate a function that relates input to output.
Two specific tasks:
- Classification: given input data, return class (e.g., dog, cat, horse)
- Regression: given input data, return (real) value (e.g., stock price)

#### Unsupervised learing
Tracking data are just input, no outputs, and the task is to cluster similar data.
#### Reinforcement learning
Tracking data according to state action reward schema.
The task here is to learn an optimal policy.
We have to give to the machine different rewards for task completation.

---
## Linear regression

Hypotesis are linear functions:
$h(x:w) = w_0 + w_1x_1 + ... + w_mx_m = w^Tx$
where 
$x = [1 x_1 x_2 ... x_m]^T$
$w = [w_0 w_1 x_2 ... w_m]^T$
For example, we could use ML to predict price of houses in relation with the size of the houses.

We have to best fit the data in our model.
We have to measure error with quadratic distance wrt ground truth.

**Loss function: squared errors (wrt D)**
	$$E(w) = {1\over2} \sum_{<X,t>\in D}(t-h(x;w))^2 = {1\over2}\sum_{<X,t>\in D}(t-w^Tx)^2$$
This is the error functin and we will always try to minimize it.
Minimize errors: $w*=argmin_wE(w)$
So we have to do the gradient (partial differential), and compute the partial derivatives of the error function in order to minimize it.
##### **Recap**
So to contruct error function, given our approx function (w1x + w0):
- We take an $x_i$ on the axis;
- We take the the corresponding data on that $x_i$ wich will be $t_i$;
- We compute the difference between this 2 values ($(w1x_i + w0)-t_i$);
- We square it and we generalize it for every x:$$((w_1x_i + w_0)-t_i)^2$$
Since we compute the sum for every x in the domain, x is know.
The unknown values are the one of the w array.
We add the 1/2 constant because we want to simplify the differentiation calculus.

Then we claim that our error function is a **CONVESS** function.
We can claim this because it is a linear function of squared elements, so we are using also a quadratic error.
This means that our function will have one point of absolut minimum.
So to minimize the error function we will have to compute:$${dE(w)\over dw} = 0;$$
So we have to compute all the partials derivatives and we end up with a system of n linear equations.
To do it, we have to compute the **design matrix**:
X =  [1 x11 ... x1m]
	...
    [1 xN1 ... xNN]
Where each raw is a copy of the input tuples <x1,t1>,...,<xN,tN> at wich we add a 1 at the beginning.
Then starting from the design matrix:
- We compute the pseudo invers;
- We multiply by T;
- And we have the solution;

So know the point is to update the gradient iteratively, in order to continuously decrease the errors.
We will introduce a parameter called learning rate, wich will be fixed before the computation and will be used by our AI to reduce the errors.
So w will be reduced by this quantity:$$w = w - \gamma {dE(w)\over dw}$$
where gamma is our learning rate (>0).
This kind of parameter decided before even looking to the function and used from the AI to learn are called hyperparameters.
We can repeat our optimization operation until we reach a **termination condition**.
So an hypotethical algorithm could be:
- Init w with random values;
- while (not termination condition):
	- for each $w_i$:
		- $w_i = w_i - \gamma {dE(w)\over dw_i}$
This works because the function is covess so we do not have local minimum.
We might have a function wich is not differential and we should approximate it in order to make it become differential.
Obviously, since the points in the Domani could be a lot, the operation of sum might be really expensive.
So sometimes instead of considering all the data, we consider just a subset of them (**batch**).
Obviously this will speed up the update but is an approximation so will reduce accuracy.

#### Stochastic Gradient Descent
- We compute a random subset of elements in D: $S \subset D$;
- We perfor the sum only on S elements: ${dE(w)\over dw_i}=-{1\over2}\sum_{<X,t>\in S}(t-w^Tx)x_i$
We can expect this subset to be representative of the data due to random pick.
This is used in practice by a lot of algorithms.

##### Squared errors
Why we use it ?
because there might be some gaussian noise that affects the measures we take.
Using least squared errors we maximize the probability that our function, effected by noise, will return the right value.


### Linear model for classification

Classification problem:
- Target function $f:X\rightarrow Y$
- $X\subseteq \mathbb{R}$
- $Y = \{c_1,...,c_k\}$

We call our set **linearly separable** if there exists an hyperspace that separates instances from different classes.
Ex:
$X\subseteq \mathbb{R} ^2$, then we will have our plane $Y = \{+,-\};$

Linear discriminant function y(x) defines separate hyperplans.
With k classes to distinguish $(x = x_1,...,x_m)$, we will have:
- $y_1(x_1,...,x_m) = w_{11}x_1 + ... + w_{1m}x_m + w_{k0}$
- ...
- $y_k(x_1,...,x_m) = w_{k1}x_1 + ... + w_{km}x_m + w_{k0}$
- Prediction: $h(X) = c_i, i = argmax_{i = 1,...,k}y_i(X)$
- Separating hyperplanes: $\{⟨x_1, . . . , x_m⟩ | y_i(x_1, . . . , x_m) = y_j(x_1, . . . , x_m)\}$

We can use a compact notation by means of matrix and transposed matrix rapresentation.
Now we just have to find w that minimize the least squared errors upon the distance between the data and the hyperplan that we define to distinguish the data.
![[Pasted image 20250507093020.png]]
Obviously we get errors if there are outliers since our function is linear.

### Perceptron
We add non-linearity to our system, by adding a **step function**, after the linear function, where its output is $\{-1,+1\}$, using a threshold over the objective function.
In this case though we cannot use Stocastic Gradient Discend directly, since it's non-differentiable, and in most of the cases the gradient will be zero!
We can, instead, apply the SGD on the untresholded unit, but we are not interested in the ERROR, but in the pure classification in itself.

We can also update the parameters of the w variable by using weighted-update variant.
We use the sign function to express the fact that we are going to sample only 1 or -1.
Support Vector Machines (SVM) for Classification
maximum margin for better generalization
We maximize the distance of the line in order to obtain a better approximation.

### Non linearly-separable data
What if we have non linearly separable data ?

Example:
Polynomial curve fitting:
We must be carefull, because if our machine is too powerfull it could perfectly fit the input provided (dataset) but could not fit well against unknown inputs.
![[Pasted image 20250507113326.png|600]]
Another option to correctly fit non linear separable data is to transform them in order to make them linearly-separable (for example, we could use polar coordinates).
Sometimes it is hard to understand what transformation to apply.

### Classification Evaluation
In this situation, we sample over a set of instances, and we want to compute the error.
How can we do this?
$error_d(h) = Pr[f (x) ̸= h(x)]$
probability that h misclassifies random instance x∼d
We can compute error_d on all the space and error_s on just a sample space subset of the domain. 
We can define the accuracy as:
$accuracy(h) = 1 - error_s(h)$
How well $error_s(h)$ extimate $error_d(h)$ ?
Well, if we recognize very well over a sample of the domain we are not guaranteed to work well on the wall domain space.

How to compute $error_S(h)$
1. Partition data set as D = {T, S} (T ∩ S = ∅), where:
	- T is the Training Set;
	- S is the Test Set;
	- |T| = 2/3|D| (rule of thumb);
2. Compute h using training set T;
3. Evaluate error on test set S: $error_S (h) = {1\over n}\sum _{x\in S} \delta (x)$;

How to detect overfit:
Hypothesis h ∈ H overfits training data if for some alternative h′ ∈ H:

errorT (h) < errorT (h′) (h′ performs worse than h on T ) 
but 
errord (h) > errord (h′) (estimated as: errorS (h) > errorS (h′))
(h′ performs better than h on unseen instances)


##### K-Fold Cross Validation:
Perform many experiments and compute errorSi(h) for different independent test sets Si

**K-Fold Cross Validation Algorithm**:
1. Partition dataset D = {S1, S2, . . . , Sk };
2. For i = 1,...,k do:
	Use $S_i$ as test setm and remaining data as training set $T_i$:
	- $T_i = {D - S_i}$;
	- $h_i = L(T_i)$;
	- $\mu_i = error_{s_i}(h_i)$;
3. Return $\mu = {1\over k}\sum_{i = 1}^ k \mu_i$

## Artificial Neural Networks
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

the choice for ReLU for hidden units has been shown that on average it's a good function.
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
To train our network we need to compute the gradientrs with respect to the network parameters $\theta$. The **backpropagation** algorithm is used to propagate the gradient computation from the cost through the whole network.
- We just need it to be *differentiable*, so that we can do the partial derivative over every parameter and use that to update the parameter values

Computing the derivative over the loss function over ALL the parameters is incredibly difficult, and it's a bottleneck. We use the chain-rule: we differentiate a composition of functions.
### Chain rule
let $y = g(x)$ and $z = f(g(x)) = f(y)$ \[derivate of a composite function]
by applying the chain rule we have:
- $\displaystyle \frac{dz}{dx} =  \frac{dz}{dy} \frac{dy}{dx}$ 
In the case of vector functions, $g : \mathbb{R}^m \rightarrow \mathbb{R}^n$ and $f: \mathbb{R}^n \rightarrow \mathbb{R}$ 
- $\displaystyle \frac{\partial z}{\partial x_{i}} = \sum_{j} \frac{\partial z}{\partial y_{j}}\frac{\partial y_{j}}{\partial x_{i}}$
 
This means that we can calculate the derivative for every node of the layer to calculate the entire derivative. backpropagation reuses a lot. In the forward pass we keep stored both  the function output and also the linear combination of it.

---

### Policy evaluation
In the assumption that the MDP is known, we want to evaluate the Policy.
How to evalutate the policy ?
Given a policy $\pi$, we want to know, starting from state S, what will return can I expect?

the choice for ReLU for hidden units has been shown that on average it's a good function.
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
To train our network we need to compute the gradientrs with respect to the network parameters $\theta$. The **backpropagation** algorithm is used to propagate the gradient computation from the cost through the whole network.
- We just need it to be *differentiable*, so that we can do the partial derivative over every parameter and use that to update the parameter values

Computing the derivative over the loss function over ALL the parameters is incredibly difficult, and it's a bottleneck. We use the chain-rule: we differentiate a composition of functions.
### Chain rule
let $y = g(x)$ and $z = f(g(x)) = f(y)$ \[derivate of a composite function]
by applying the chain rule we have:
- $\displaystyle \frac{dz}{dx} =  \frac{dz}{dy} \frac{dy}{dx}$ 
In the case of vector functions, $g : \mathbb{R}^m \rightarrow \mathbb{R}^n$ and $f: \mathbb{R}^n \rightarrow \mathbb{R}$ 
- $\displaystyle \frac{\partial z}{\partial x_{i}} = \sum_{j} \frac{\partial z}{\partial y_{j}}\frac{\partial y_{j}}{\partial x_{i}}$
 
This means that we can calculate the derivative for every node of the layer to calculate the entire derivative. backpropagation reuses a lot. In the forward pass we keep stored both  the function output and also the linear combination of it.