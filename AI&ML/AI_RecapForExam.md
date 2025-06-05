# DPLL

We need Tseitin’s transformation to turn a propositional logic formula into an equisatisfiable 3 CNF formula.
The equisatisfiable 3 CNF formula will be later used to run DPLL and find staisfiability or validity.

- CNF($q \iff \neg p$) = $(\neg q \vee \neg p) \wedge (p \vee q)$;
- CNF($q \iff (p\wedge r)$) = $(q ∨ ¬p ∨ ¬r) ∧ (¬q ∨ p) ∧ (¬q ∨ r)$;
- CNF$(q ≡ (p ∨ r))$ = $(¬q ∨ p ∨ r) ∧ (q ∨ ¬p) ∧ (q ∨ ¬r)$;
- CNF($q ≡ (p → r)$) = $(¬q ∨ ¬p ∨ r) ∧ (p ∨ q) ∧ (¬r ∨ q)$;
- CNF(q ≡ (p ≡ r)) = $(q ∨ p ∨ r) ∧ (q ∨ ¬p ∨ ¬r) ∧ (¬q ∨ p ∨ ¬r) ∧ (¬q ∨ ¬p ∨ r)$;

```Algorithm
DPLL(φ, I′)
	(ψ, I) := UnitPropagation(φ, I′);
	if ψ contains {}
		then return ({{}}, ∅)
	elseif ψ = {} 
		then return ({}, I)
	else select a literal λ ∈ C ∈ ψ;
		if DPLL(ψ ∪ {{λ}}, I) = ({}, I′′)
			then return ({}, I′′)
		else 
			return DPLL(ψ ∪ {{¬λ}}, I)
```

# First order logic (FOL)

Elements of the first order logic:
- **Terms:** costants or variables, like Mary, John, 1, 2, blue, ecc...;
- **Functions:** fatherOf(), motherOf(), ecc...;
- **Predicates:** Mortal, Prime, ecc...;
- **Quantifiers**: like $\exists,\ \forall$ ecc...;


# Tableux

Tableux in FOL:
![[Pasted image 20250604101534.png]]![[Pasted image 20250604101446.png]]
##### Free term
When a variable in a FOL formula does not occur in the scope of any quantifier.

1. To test a formula $\phi$ is **valid**, we form a tableau starting from $\neg \phi$. If the tableau is closed off, then $\phi$ is valid;
2. To test **logical implication**( weather $\Gamma \models \phi$ ), we can check if $\Gamma \cup \neg \phi$ is unsatisfiable. If it is unsat, then the implication is respected;
3. To test **satisfiability**, we can just check on the formula given in input if we find an open path appliyng the tableau rules and that's it;

Different from propositional logic, in FOL models can be **infinite**!


# Classical Planning

We will define a planning problem, composed of:
- $D$ = Domain  ($D= <S, A, \delta>$)
- $s_0$ = Initial State
- $G$ = Goal set

Evaluation function uses $f(n) = g(n) + h(n)$, where
- $g(n)$ is the actual cost of reaching $n$;
- $h(n)$ is an _estimate_ of cost to reach the goal from $n$;

# PDDL

Planning Domain Definition Lenguage.
We always define 2 files: the domain file, and the problem file.
The **domain file** describes predicates and actions, with their parameters, preconditions and effects. The **problem file** specify the domain file, describes the initial situation and the specific goal of our problem, and the objects that we will use in our problem.
We define the domain of ou problem at first instance, and then our problem hiself.
We have 2 main lenguages: **STRIPS** and **ADL**.
Remember that we are always working with the **CLOSED WORLD** assumption.


#### STRIPS example
Domain file:
```STRIPS
(define (domain BlocksWorld)
	(:requirements :strips :negative-preconditions) ; This is a comment
	
	(:predicates
		(on ?b1 ?b2 - object)
		(onTable ?b - object)
		(clear ?b) ; not necessary to write type if is `object`
	) ; This allows us to represent the States
	
	(:action moveFromTable
		:parameters (?b1 ?b2)
		:precondition (and (clear ?b1) (clear ?b2) (ontable ?b1))
		:effect (and (on ?b1 ?b2) (not (onTable ?b1)) (not (clear ?b2))) ; We
				; have negative-precondition 
				; to add the negative precondition
	)
	(:action moveToTable
		:parameters (?b1 ?b2)
		:precondition (and clear(?b1) (on ?b1 ?b2))
		:effect ( and(not (on ?b1 ?b2)) (onTable ?b1) (clear ?b2))
	)
	(:action moveFromBlock
	:parameters (?b1 ?b2 ?b3)
	:precondition (and (clear ?b1) (clear ?b3) (on ?b1 ?b2))
	:effect (and (not (on ?b1 ?b2)) (clear ?b2) (on ?b1 ?b3) (not( clear ?b3)))
	)
)
```
Problem file:
```STRIPS
(define (problem BlocksWorldProblem)
	(:domain BlocksWorld [^1])
	(:objects A B C)
	(:init (onTable A)
		   (onTable B)
		   (onTable C)
		   (clear A)
		   (clear B)
		   (clear C)
	) ; we are working with the closed world assumption (like DBs) where
	  ; everything that isn't mentioned is automatically false
	
	(:goal (and (on B C)
				(on A C)
		)
	)
)
```

#### ADL example
Domain file:
```ADL
(define (domain BlocksWorld)
	(:requirements :adl) ; quantifiers and negated preconditions
	(:types block)
	(:predicates
		(ontable ?b - block)
		(on ?a ?b - block)
	)
	(:action move
		:parameters (?b1 ?b2 - block)
		:precondition (and    ; calculated before applying action
			(forall (?b - block) (not (on b ?b1)))
			(forall (?b - block) (not (on b ?b2)))
		)
		:effect (and          ; calculated after applying action
			(on ?b1 ?b2)
			(forall(?b - block) (when(on ?b1 ?b) (not(on ?b1 ?b)))
								; conditional effect!
			when(ontable ?b1) (not(ontable ?b1))
		)
	)
)
```


# Situation Calculus

Reasoning about dynamic domains.
The main elements of the situation calculus are the **actions**, **fluents** and the **situations**.
We define our domain with a set of formulas in which we include the Initial state situation described with formulas and constraint.
Initial state situation will look like this:
$D_0 =$
- $\{\forall x. AgentAt(x,s_0) \leftrightarrow x=A$
- $\forall x.ItemAt(x,y,s_0) \leftrightarrow (x=\bigcirc \wedge y = B) \vee (x=\bigtriangleup \wedge y = C)$
- $\forall x. \neg AgentHas(x, s_0)$
- $\forall x.Item(x) \leftrightarrow (x = \bigcirc \vee x = \bigtriangleup)$
- $\forall x.Exit(x) \leftrightarrow x=D$
- $\forall x_1 \forall x_2 Right(x_1, x_2) \leftrightarrow ( x_1 = A \wedge x_2 = B) \vee ( x_1 = B \wedge x_2 = C) \vee ( x_1 = C \wedge x_2 = D)$ 

We add the set of Precondition axioms to our domain. This set specify when is possible to perform an action in a certain state.
It will look like this:

$D_{AP} =$
- $Poss(move(x), s) \leftrightarrow \exists y.AgentAt(y, s) \wedge Right(y,x)$;
- ecc ...

Then we will have the effect axioms. 
Effect axioms are used to express the effects that an action have on the current state.
Since we are does not have the closed world assumption, we will have to specify what keeps unchanged in the effect axioms. We can avoid this procedure, which would be really long, by using normal form to represent effect axioms and implementing successor state axioms.

So from scratch we do:
1. Collect what Fluent changes for every actions and we write the effect axioms;
2. Then we write SSA from the fluent;

Given a list of normalized effect axioms:
- $\exists \overrightarrow{y_1}. \gamma_1^+(\overrightarrow{x}, \overrightarrow{y_1},a,s) \rightarrow F(\overrightarrow{x}, do(a,s)$  
-  $\exists \overrightarrow{y_2}. \gamma_2^+-\overrightarrow{x}, \overrightarrow{y_2},a,s) \rightarrow F(\overrightarrow{x}, do(a,s)$  
- ...
-  $\exists \overrightarrow{y_m}. \gamma_m^-(\overrightarrow{x}, \overrightarrow{y_m},a,s) \rightarrow F(\overrightarrow{x}, do(a,s)$  
We can create SSAs for the fluent F like:
- $F(\overrightarrow{x}, do(a,s)) \leftrightarrow (\bigvee_i \exists \overrightarrow{y_i}. \gamma_i^+(\overrightarrow{x}, \overrightarrow{y_i},a,s)) \vee (F(\overrightarrow{x},s) \wedge \neg(\bigvee_j \exists \overrightarrow{y_j}. \gamma_j^-(\overrightarrow{x}, \overrightarrow{y_j},a,s)))$

# Basic Action Theory

So basically we should describe
$D = \Sigma \cup D_{UNA} \cup D_{S_0} \cup D_{AP}  \cup D_{SSA}$
Where:
- $\Sigma$ = Fundational axioms of STATCALC;
- UNA = Unique Names for Actions;
- S0 = description for initial state;
- AP = Actions precondtions;
- SSA = Successor State Axioms;

# Regression

$AgentAt(B, do(move(c),do(move(B),do(move(A),S_0))))$
This is the situation resulting from moving agent from A to B to C, starting from initial situation $S_0$, but is pretty long to represent.
Our aim si to produce a new formula which is equisatisfiable with respect to the previous one, but that involves the initial status.

How does the regression operator works:
- If $\phi$ is a situation independent atom, e.g.:$\exists x.Right(x,y)$, then: $R[\phi] = \phi$;
- If $\phi$ is a fluent of the formula $F(x,S_0)$, then: $R[\phi] = \phi$;
- If $\phi = Poss(A(t),\sigma)$ and precondtion axiom $\phi \iff \Pi_A(x,s)$, then: $R[\phi] = R[\Pi_A(x,S_0)]$;
- Then we have the following **properties**:
	- $R[\neg \phi] = \neg R[\phi]$
	- $R[\phi \vee \gamma] = R[\phi] \vee R[\gamma]$$
	- $\phi = \exists x.\psi \implies R[\phi] = \exists x.R[\psi]$


