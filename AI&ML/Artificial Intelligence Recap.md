# **Tableux**
Tableux in FOL:
![[Pasted image 20250604101534.png]]![[Pasted image 20250604101446.png]]
##### Free term
When a variable in a FOL formula does not occur in the scope of any quantifier.

1. To test 
































# Continuation of exercise
### Normal Form: Why does it work
Agent moved to $x$ using the normalized move `move(x)`, and before we wrote what changed in the system. We need to specify also *what doesn't change*. In order to do this we used the **normal form**, for the effects. 
We don't write the `forall` quantifier. 
We can describe the following effect
$$
\forall xys. AgentAt(y,s) \wedge x \neq y \implies \neg AgentAt(y,do(move(x),s))
$$
as 
$$
\forall xys. A(y,s) \wedge C(x,y) \implies \neg A(y,do(Act(x),s))
$$
$$
\forall x \forall y \forall s. \neg(A(y,s) \wedge C(x,y)) \vee (a=Act(x) \implies\neg A(y,do(a,s)))
$$
We can drop all parenthesis, since they are all conjuctions
$$
\forall x \forall y \forall s. \neg A(y,s) \vee \neg C(x,y) \vee \neg a=Act(x) \vee \neg A(y,do(a,s))
$$
..and then we associate them using the negation of disjunction rules to get
$$
\forall x \forall y \forall s. \neg (A(y,s) \wedge C(x,y) \wedge a=Act(x)) \vee \neg A(y,do(a,s))
$$
And this is the normal form for the negated effect!
$$
\forall x \forall y \forall s \forall a. A(y,s) \wedge C(x,y) \wedge a=Act(x) \implies \neg A(y,do(a,s))
$$
which, since we don't have te $x$ term in the right-hand part of the formula, we can transform it in an existential quantifier (dropping universal quantifiers)
$$
\exists x( A(y,s) \wedge C(x,y) \wedge a=Act(x)) \implies \neg A(y,do(a,s))
$$
## Explanation Closure Axioms
This helps to create SSAs that automatically solves the frame problem, by combining all action and effect axioms.
For `move(x)` we will isolate all axioms that affect the fluent.
For example, `AgentAt(x,s)` is affected just by move(x). We'll collect these two axioms with the positive and negative form:
- $\exists y. \gamma^+(x,y,a,s) \rightarrow  F(x,do(a,s)$
- $\exists y. \gamma^-(x,y,a,s) \rightarrow  \neg F(x,do(a,s)$
Getting back to te example, for `AgentAt(x)` we have:
1. $a = move(x) \rightarrow AgentAt(x,do(a,s))$
2. $\exists x. a= move(y)AgentAt(x,s)\wedge x\neq y \rightarrow \neg AgentAt(x, do(a, s)$
3. $a = exit() \wedge AgentAt(x,s) \rightarrow \neg AgentAt(x,do(a,s))$
#### Example
Assuming we are observing the following situations
- $\neg AgentAt(x) \wedge AgentAt(x,do(a,s))$ ==We know that the action $a$ must be $move(x)$, since there is **No other action that affects AgentAt**==. ($\rightarrow a = move(x)$)
- $AgentAt(x,s) \wedge \neg AgentAt(x, do(a,s))$ This could be happening for two different actions, since they have, and they both can be happening at the same time. We are explaining why we got into this situation. ($\rightarrow \gamma_1^-(x,y,a,s) \vee \gamma_2^-(x,a,s)$)
These kinds of axioms are called **Explanation Closure Axioms**
# Successor State Axioms
to get into Successor State Action we have to make an assumption. We assume that:
- Axioms satisfy the integrity conditions, and so there are no two axioms that contraddict each other. So $A(x_1, ..., x_n) = A(y_1,...,yn) \implies x_1 = y+1 \wedge ... \wedge x_n = y_n$
- Axioms need to have unique names
We can rewrite the normalized effect actions together with the Explanation Closure Axioms in the following way:
- $AgentAt(x,do(a,s)) \leftrightarrow a = move(x) \vee AgentAt(x,s) \wedge \neg(\exists y. a=move(y) \wedge x \neq y \wedge AgentAt(x,s)) \vee (a=drop() \wedge AgentAt(x,s)))$ (either it moved or it was there before executing an action, and the action didn't make it change its place)
### Formalization
Given a list of normalized effect axioms:
- $\exists \overrightarrow{y_1}. \gamma_1^+(\overrightarrow{x}, \overrightarrow{y_1},a,s) \rightarrow F(\overrightarrow{x}, do(a,s)$  
-  $\exists \overrightarrow{y_2}. \gamma_2^+-\overrightarrow{x}, \overrightarrow{y_2},a,s) \rightarrow F(\overrightarrow{x}, do(a,s)$  
- ...
-  $\exists \overrightarrow{y_m}. \gamma_m^-(\overrightarrow{x}, \overrightarrow{y_m},a,s) \rightarrow F(\overrightarrow{x}, do(a,s)$  
We can create SSAs for the fluent F like:
- $F(\overrightarrow{x}, do(a,s)) \leftrightarrow (\bigvee_i \exists \overrightarrow{y_i}. \gamma_i^+(\overrightarrow{x}, \overrightarrow{y_i},a,s)) \vee (F(\overrightarrow{x},s) \wedge \neg(\bigvee_j \exists \overrightarrow{y_j}. \gamma_j^-(\overrightarrow{x}, \overrightarrow{y_j},a,s)))$
==This **solves the frame problem**, since it specifies both when F changes and also when it doesn't.==

# Concluding - Basic Action Theory
For BAT we need to specify the domain.
The domain is based as $D=\Sigma \cup D_{UNA} \cup D_{s_0} \cup D_{AP} \cup D_{SSA}$ , where
- $\Sigma$ : Foundational axioms of SItuational Calculus
- $D_{UNA}$ : Unique Names for Actions
- $D_{s_0}$ : Description of Initial Situation (Unique)
- $D_{AP}$ : Action Precondition Axioms (character `Poss(a,s)`)
- $D_{SSA}$ : Successor State Axioms (Specifies how axioms change the state of the domain)
### Legality Task
GIven these tools we can evaluate the **Legality (or executability) task**: Given a sequence of actions $\rho = a_1, ..., a_n$, check whether every action in the sequence is executable, through the Action Precondition axioms
### Projection Task
Given a sequence of actions $\rho = a_1, ..., a_n$, and a formula $\phi$ (which must talk about a state of situation $s$) check whether $D \models \phi (do(a_n, do(a_{n-1}, ...,do(a_1, s_0))))$, that is..check whether $\phi$ holds in the situation $s_n$ resulting from executing $\rho$ starting at $s_0$.
# Regression
It's a tool used to solve the previous tasks. Considering the Projection Task, one way is to start from the initial situation, and to execute all successor-state action
