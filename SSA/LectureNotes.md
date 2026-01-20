# Security in Software Application

Buffer overflow section completely skipped due to previous knowledge.

---
# Format string
In C language, when try to format a string there might be different hacks.

There are some programs that in absence of arguments, prints random area of the stack:
EX:

```C
int main () {
	printf("Mary has %d cats");
}
```

This programs can be used to produce a buffer overflow, crash applications by accessing reserved memory areas or read the stack and store stack addresses.

#definition 
> A **format string** vulnerability occurs when a function is called that either contains a format string under the attacker control or is not followed by an appropiate number of arguments;

**Prevention**: Considered very bad practice to place a string to print as the format string argument. If possible, make the format string a constant.
Is really hard to detect weather there is a format string vulnerability. Arbitrary code execution is a usual consequence.

To inject arbitrary code and execute it shell metacharacters are used:
- ‘\`’ to execute something (command substitution)
- ‘;’ is a command (“pipeline”) separator
- ‘&’ start process in the background
- ‘|’ is a pipe (connecting standard output to standard input)
- ‘&&’ , ‘||’ logical operators AND and OR
- ‘<<‘ or ‘>>’prepend, append
- \# to comment out something

A possible solution to sanitize user's input is to whitelist all the allowed characters 
```C
static char ok_chars[] =

"1234567890!@%-_=+:,./\
abcdefghijklmnopqrstuvwxyz\
ABCDEFGHIJKLMNOPQRSTUVWXYZ";
```

and check if the user input contains only those char (blacklisting is considered a bad approach).

# Input validation
The lack of input validation can lead to multiple vulnerabilities, such as:
- Command injection;
- SQL injection;
- XSS (Cross Site Scripting);

**Command injection**:
For example, if we have a program that run the following code:
```shell
cat thefile | mail clientaddress
```
we might provide the following client address:
```
pippo@di.uniroma1.it | rm -rf
```
and basically destroy the machine.
To prevent this kind of attacks we need:
- Validate inputs;
- Run with minimal priviledges;

## SQL injection
Basically force into forms that interacts with database particular queries that are able to fool the system and obtain informations on the database or just bypass security check.

Fix:
Prepared statement. Example:
```C
PreparedStatement login =
con.preparedStatement("SELECT * FROM Account

WHERE Username = ? AND Password = ?" );

login.setString(1, username);
login.setString(2, password);
login.executeUpdate();
```

---
# Race conditions

Race conditions can happen randomly and are difficult to debug or to repeat, and when exploited can lead to misuse of shared objects.
Happens when:
- Objects access same shared var;
- Algorithm doesn't enforce access order;
Avoid by:
- Use semaphores/locks;
- Make the instructions `atomic`;
Example: `mkdir` instruction.
TOCTOU time attack is used between the check of the folder existence and the actual creation of the folder to obtain permissions, or ownership of arbitrary files by swapping them in the victim's memory.

---
# Secure software design

ISO/IEC 12207:2008. Saltzer and Schroeder have designed some principles:
- Least common mechanism;
- Psychological acceptability;
- Least priviledge;
- Economy of mechanism/simplicity;
- Open design;
- Complete mediation;
- Fail-safe defaults;
- Separation of privilege;

Ways to analyze a secure software are:
- **attack-centric**: Starting with an attacker, we evaluate ther goals and how to achieve them;
- **design-centric**: Starting with system design, step through the model and looking for types of attack;
- **asset-centric**: Start from key assets entrusted to a system;
---
# Secure software engineering

#definition 
Concept of creating mantaining computer application by applying computer science and project management theories.

Start with **SDL** - Secure Development Lifecycle - based on 3 components:
- Repeatable process;
- Engineering education;
- Metrics and accountability;
### SDL
![[Pasted image 20260113104648.png]]

- **Requirement** phase:
	In this phase, all the system and security requirements are gathered, analyzed and verified. CIA and other security needs are checked. Also proper documentation and early planning is developed;
- **Design** phase:
	Architectural and component level are defined (i.e. language, interface, ecc...). Security design: threat model, input identification and so far;
- **Verification** phase:
	Testing phase: code is tested, security is tested, vulnerability are tracked and code is reviewed. Documentation about this phase is crucial to specify details about possible threats;
- **Release** phase:
	Focus on secure deployment and operational readiness. Defined procedures for secure management, system monitoring and security updates. This ensure the product is safely introduced and maintained in production;
- **Response/maintenance** phase:
	Feedback, incident reports ecc. drives ongoing improvements. Maintainance is performed, updates are released, ecc.

---
# Capability Maturity Models (CMM)

They provide a reference on mature practices, that have been tested "on the job". They are useful to identify the potential areas of improvement, while not giving operational guidance. We have 3 different CMMs:
- **CMMI**: provides the latest best practices related to **development**, **maintanance**, **acquisition**. In general it has been thorougly tested by more than a 1000 organizations and 5000 projects;
- **iCMM**: iCMM is widely used in the Federal Aviation Administration (FAA-iCMM). Provides a single model for enterprise-wide improvement and it integrates a lot of standards, like ISO9001 and CCMI-SE/SW and iso 15504.
- **SSE-CMM**: ISO/IEC 21827. Purpose is to fill the lack of a framework for evaluating security engineering practices against principles. 129 base practices organized into 22 process areas, designed to apply across the entire lifecycle of an enterprise.

### System security engineering CMM

Process areas assemble related for ease of use, and relates to security engineering services. Every process areas include all base practices required to meet the goals of that PA.
![[Pasted image 20260113113009.png]]
**Capability levels** are a way to assess the level of integration and organization has with regards to SSE-CMM and it has 5 levels:
![[Pasted image 20260113113121.png]]

## Bug/Flaw difference

In general both are caused by a defect, which can remain dormant:
- **Bugs** are issues regarding **implementations**, which can carry significant impacts, like a buffer overflow or a format string attack;
- **Flaws** are issues in a deeper level, usually in the **designing** phase like wrong access controls, priviledged block protection issue.

To factor this there are three pillars:
- **Applied risk management**: Analysis of architectural risk, and construction of a risk management platform creating a lifecycle of riskanalysis and mitigation;
- **Software security touchpoints**: Ideas on how to have software security, like:
	1. Code review;
	2. Architectural risk analysis;
	3. Pentesting;
	4. ecc.
- **Knowledge**: Involves gathering, encapsulating and sharing security knowledge, cataloguing principles, guidelines, rules, vulnerabilities, exploits, attack patterns and historical risks.

---
# Building Security IN

The **Building Security IN** approach focuses on engineering software so that it continues to function correctly even under malicious attack. In this framework, security is treated as an **emergent property** of the entire system rather than a feature added at the end. It is built upon **three fundamental pillars**:

1. **Applied Risk Management**: This involves identifying and managing business and technical risks throughout the entire software development lifecycle (SDLC) using a **Risk Management Framework (RMF)**.

2. **Software Security Touchpoints**: These are process-agnostic best practices applied to various software artifacts. Key touchpoints include **Code Review** (to find implementation "bugs") and **Architectural Risk Analysis** (to identify design "flaws").

3. **Knowledge**: This pillar focuses on gathering and sharing critical security information, such as **attack patterns**, principles, and historical risks, to ensure developers do not repeat past mistakes.

The primary goal is to **shift security to the earliest stages** of the lifecycle to drastically reduce the **cost of fixing defects**, which increases exponentially as the project moves toward maintenance. This proactive stance is essential to combat the "Trinity of Trouble": the rising **connectivity, extensibility, and complexity** of modern software.
 
---
# Re-entrancy attack

A **re-entrancy attack** is a type of vulnerability commonly found in smart contracts, where an attacker exploits a function's ability to call external contracts before it finishes executing.
This attack typically targets **withdrawal** mechanism in decentralized finance applications.
The malicious contract repeatedly calls the vulnerable contract, causing it to execute the same code multiple times, often leading to unintended outcomes, such as draining the contract's funds.
**Prevention**:
- Use semaphore/lock in order to **prevent** the smart contracto from doing any action until the call has ended and the balance has updated;
- Update the state **before** calling external contracts;

Vulnerable contract sample:
```Solidity
// Withdraw function vulnerable to re-entrancy attack
function withdraw(uint _amount) public {
	require(balances[msg.sender] >= _amount, "Insufficient balance");
	
	// External call (could be exploited by attacker)
	(bool success, ) = msg.sender.call{value: _amount}("");
	require(success, "Transfer failed");
	
	// State update after external call
	balances[msg.sender] -= _amount;
}
```
Attacker contract:
```Solidity
// Attack function
function attack() public payable {
	victim.deposit{value: msg.value}();
	victim.withdraw(msg.value);
	}
// Fallback function to re-enter the victim contract
fallback() external payable {
	if (address(victim).balance > 0) {
		// Re-enter victim contract and withdraw again
		victim.withdraw(msg.value);
	}
}
```
Fix:
```Solidity
modifier noReentrancy(){
	require(!lock, "Lock is up!");
	lock = true;
	_; //placeholder for the modifier code to go to
	lock = false;
}
```

---
# Language-based security

More than applying memory-safe function, validating user input, a better alternative to this is use language that providen security features, for example:
- **Memory-safe**;
	Must avoid:
	1. unchecked array bounds;
	2. pointer arithmetic;
	3. null pointers (only when behavior is undefined);
	So they basically never access unallocated de-allocated or uninitialized memory.
- Good **access control** like visibility restriction;
- **Information flow control**;

### Safety vs Security
| Safety                                        | Security                                 |
| --------------------------------------------- | ---------------------------------------- |
| System protected against accidental failures. | System protected against active attacks. |
Safe programming languages impose restrictions and **abstractions** to programmers.
For example:
- Programming lenguage **constructs** are abstractions from CPU instructions;
- **Variables** are memory abstractions;
- **Function** calls are slack mechanism abstractions;
- Virtual **memory**, virtual **CPU**, sandboxing ecc.;
Abstractions reduce bugs since there are multiple audits over single code.
The safer the language is, the more abstractions are imposed to the programmer.
Some mechanism used to preserve safety are:
- **Compile-time checks**;
- **Run-time checks**;
- Automated **memory management** with garbage collector;
- **Safe execution engine**;
- Eliminating **undefined behaviours**;
Race conditions are usually addressed in code, with mutexes and semaphores.

### Type checking 
A type is a specification of data or code in a program. Types are applicable to both functions and variables. We also use:
- Structure types;
- Array types;
- Pointer types;
- ecc.
Type checking consists of being sure to provide the right input for the function, so that the right data at memory-level gets passed around and doesn't run into undefined behaviour.

#definition 
A program is called safe if it's not stuck and has not crashed, so a language is **type-**
**safe** if well-typed programs always remain safe.

Type casting is one of the reason why C is type-unsafe, specifically with upcasting memory pointers (example char -> int).
Languages can be divided into:
- **Non typing** (bash,Perl): Usually interpreted;
- **Weakly typed** (C): Often allows unsafe cast like char[8] to char[];
- **Strongly typed** (Java): No unsafe cast;

### Mini-C
![[Pasted image 20260120115008.png]]
**Semantic of expression**s: We use $\sigma$ to represent a context in which we actually do reductions. For example:$$\sigma ⊳ e \rightarrow e'$$
**Semantic of commands**: Represent change between a state prior to the command, and a state that follows after the command is executed:$$\sigma;c\rightarrow\sigma'c'$$
Type check for expressions is done like:$${\Delta \vdash e_1: int \ \ \ \Delta\vdash e_2:int\over \Delta \vdash e_1 + e_2: int}$$

---
# Information flow

The idea of information flow is that:
- No confidential information should be leaked;
- No untrusted input from network should be used as it is;
Information flow properties can be about confidentiality or integrity. It refers not only to data access, but also on what you do with it.
We may have 2 or more levels of confidentiality: high or low in case of 2, and top secret, secret, classified, unclassified ecc. in case of more.
- **Soundness** of the type system:
	programs that are well-typed do no leak;
- **Completeness** of the type system:
	programs that do not leak can be typed.

---
# Java programming rules

Java platform offers security features, like:
- Memory safety;
- Strong typing;
- Visibility restriction (public/private);
- Stackwalking-based sandboxes;
Even with buggy code we can always guarantee some security features.
Java Secure Programming Guidelines:

• **Do not use public or protected fields**: All fields should be **private** to preserve integrity and prevent unauthorized changes;

• **Limit access to classes, methods, and fields**: Follow the **principle of least privilege**. Make them private unless there is a documented need for more visibility;

• **Do not rely on package protection**: Because anyone can extend a package or create subclasses (unless the package is sealed or the class is final), package-level visibility is not a reliable security boundary;

• **Beware of Reflection**: This feature can bypass visibility restrictions (e.g., accessing private fields via `setAccessible(true)`), though the default security manager usually prevents this;

• **Make components final**: Classes, methods, and fields should be declared **final** unless there is a specific need for them to be subclassed, overridden, or modified;

• **Prevent malicious subclasses**: Making a class `final` ensures that its security properties cannot be subverted by an attacker-controlled subclass;

• **Thread-safety**: Fields should be `final` to ensure consistent behavior in multi-threaded environments;

• **Prevent representation exposure**: Never return a reference to a **mutable object** (including arrays) to untrusted code;

• **Secure object storage**: Never store a reference to a mutable object obtained from untrusted code, as the external code could modify it later;

• **Clone arrays**: When passing arrays into or out of a class, they should be **cloned** to ensure the class maintains exclusive control over its internal state;

• **Avoid inner classes**: In Java bytecode, inner classes are transformed into package-visible classes, which can inadvertently expose the **private fields** of the outer class to other classes in the same package;

• **Control inheritance**: Use the `final` keyword to prevent the creation of malicious subclasses that could compromise the intended logic;

• **Make classes non-cloneable**: Prevent unauthorized duplication of objects by not implementing the `Cloneable` interface or by restricting access to the clone method;

• **Make classes non-serializable and non-deserializable**: Avoid implementing `Serializable`. Specifically, do not implement `writeObject` or `readObject` unless absolutely necessary, as these can be used to bypass constructors and create insecure object states.

---
# Aliasing

In computing, **aliasing** describes a situation in which a data location in memory can be accessed through different symbolic names in the program. Thus, modifying the data through one name implicitly modifies the values associated with all aliased names, which may not be expected by the programmer.

#### Confined types
Some system have been proposed for alias control, with many of these requiring special annotations in code like `@rep` to express the programmer.
For example objects of type `@rep` are not assignment compatible.
The design challenge is to define a system that is:
- Flexible and expressive;
- Simple to be understandable and statically checkable;

#### Immutability
Immutability prevents aliasing, given that an immutable object cannot be changed by any of its references. In fact the java.lang.String class is immutable.
This is an advantage, because it's not needed to clone an object to share it around, but it's also a disadvantage because for every change we need a new object.

---
# Testing

Necessary part of development process, we'll begin listing all of the possible testing
procedures:

- **Scenario testing**:
	Used to ensure that **requirements** are complete and consistent, by doing validation efforts, for example abuse and misuse cases in UML. Steps:
	- Identify trust relationships and attack them;
	- Which resources are open to me ? How can they be abused ?
	- Are there insecure defaults or things that could easily be misconfigured or badly coded ?
	**Key idea:**
	"If someone interacts with this system in a particular way, does it behave safely?"
	
- **Specification testing**:
	Proves the correctness and completeness of the specification by **formal proof** or by **symbolic execution**. It's a rare type of testing, usually done for high-assurance projects. Many networking protocols lacks specification testing, and have spefication vulnerabilities;
	**Key idea:**
	“Is the design mathematically correct?”
	
- **In-line testing**:
	Execution testing, like ASSERT. In this case, checks are placed inside the code to ensure the program is always in a valid state. 
	Main features:
	- Allows verification of **invariants**;
	-  Is usually turned off in the final product;
	- Happens earlieri thanother forms of testing;
	**Key idea:**
	“This condition must always be true at this point.”
	
- **Unit testing**:
	The single functions get tested, also a good time to test against buffer overflow and low-level coding mistake.
	**Key idea:**
	“Does this function behave correctly for all expected and unexpected inputs?”
	
- **Integration testing**:
	The natural follow-up to unit testing, tests for discrepancies in assumptions and security models, and does the security testing of access control.
	**Key idea:**
	“Do these modules agree on assumptions?”
	
- **Human interaction testing**:
	Regards to the user experience part. 
	Examples:
	- Users clicking through security warnings;
	- Confusing permission dialogs;
	- UI elements that enable phishing or spoofing;
	**Key idea:**
	“Can a user accidentally or unknowingly compromise security?”
	
- **Acceptance testing**:
	Tests whether the system meets customer expectations in real usage.
	- Customers are not security experts;
	- Customers have no responsability;
	**Key idea:**
	“Is this acceptable to the user?”
	
-  **Final testing**:
	Testing of the final software as a whole, but fault injecting random or grammar generated input and see if sw breaks. If system crash is usually difficult to find the flaw.
	**Key idea:**
	“What happens if we throw chaos at the system?”

---
# Testing suites

To test a system we need to have:
- A test suite, or more informally, a collection of data;
- A test oracle which is able to say if the test has passed or not;

**Code coverage** are stats that are used to check how good a test suites is, and we have two or more kinds of coverage:
- **Statement Coverage:** Percentage of program's instructions executed by at least one test;
- **Branch Coverage:** Percentage of conditional branches covered by at least one test (if-else);

High coverage criteria may discourage defensive programming, since it's difficult to correctly test in the case there is defensive code which corrects/abort in case of erros.
**Normal testing** looks for correct behaviour, with sensible input and checks in borderline conditions, while **security testing** looks for unwanted and wrong behaviours, which means that it's imperative to use negative check cases: normal use of the system is more likely to reveal functional problems than security problems.

## Fuzz testing
Basic idea: generate (semi-automatically) random input to see if the application crashes, to check if there are "special" inputs. The original form of fuzzing generated very long input to see if the system crashes with segmentation fault.
**PROS**:
- Little effort required (automatized);
- 100% true positives;
**CONS**:
- Not all bugs will be found;
- Crashes are not easy to analyze;
- Specific-input bugs will not be found;

## Fuzzing web apps
Fuzzing web apps: Could a fuzzer detect SQL injections or XSS vulnerabilities ?
- For SQL injection, monitor database for error messages;
- For XSS, see if the website echoes HTML tags in user input.
There are various tools in web apps, that might produce false positives/negatives.

## Smart fuzzer
- **Mutation based fuzzing**:
	By supplying well-formed input, fuzzer generates random changes from input. This has stron bias by the initial input, but it's very easy to setup and isn't dependent on program specification.
	
- **Generation-based fuzzing**:
	By supplying a specification over file format, or communication protocol, the generation-based fuzzer tries and generate slightly malformed packets or hit corner cases.
	Useful, for example, in testing incorect lenghts, zero-length headers, or payloads too long/short.
	- It's way more **complete** in its search;
	- To test, a specification and an ad-hoc generator is needed;
	
- **Whitebox fuzzing**:
	The idea is that we have the actual source code available, we can search deeper into all branches. Symbolic variables are used, and multiple "symbolic executions" of the program are performed in order to look for vulnerabilities in each possible branch. Each branches generates a new **constraint** and complex software like SAGE are used to solve this mathematical problems.
	- Mathematical proof of security;
	- Gives results unexpected from any other form of testing;
	- Might be computational infeasible, expecially in presence of loops;

---
# Program verification

Program verification deals with the issue of mathematically and formally proving that a program satisfies some properties. It's not the same as testing, wihch is a weaker form of verification.
Program verification need:
- A formal semantic of the programming language;
- A specification language to express properties;
- A logic to reason about programs and specifications;
- A verification tool;
We have some tools at our disposal:
- **Symbolic execution**;
- **Weakest preconditions** (or strongest postcondition): basically inserting asserts inside code to make sure a certain property is satisfied at a certain point in the program;

**Complications**:
1. **Loops**: mpossible to follow all paths. Solution: **Loop invariant**: code to ensure that a certain condition is met at every iteration of a loop;
2. **Modularity**: since variables are held in the heap, it isn't always possible to monitor weather they are changed in a 
3. **Concurrency**: even though the actual program might be satisfying some assertion, this might be invalidated by a concurrent program which is modifying the same variable.

#### VCGen
A standard approach to program verification is to use Verification Condition Generator, or VCGen. Using annotations in the program, VCGen produces a set of logical properties, called verification conditions.
If the verification conditions are true, the annotations are correct and the program satisfies the specifications.


#### JML- Java modelling language

A formal specification language for java, which can be checked by runtime checking or program verification.
They are added as special java commitment, like `//@` or between `/*@` and `@*/`.
Properties are specified using java syntax with some added:
- Operators like `old()`,`\result`,`\forall`,`\exists`,`==>`,ecc.
- Keywords like `requires`, `ensures`,`invariant`, ecc.
Example:

```java
public class ChipKnip{
	private int balance;
	//@ invariant 0 <= balance && balance < 500;
	
	//@ requires amount >= 0;
	//@ ensures balance <=old(balance);
	//@ signals (BankException) balance == \old(balance);
	public debit(int amount) {
		if (amount > balance) {
			throw (new BankException("No way"));
		}
		balance = balance – amount;
}
```

- Assertion:
```java
  /*@ assert(\forall int i; 0<= i && i<a.length; a[i] != null);@*/
```
  - Loop invariants:
``` java
/*@ loop_invariant 0<= n && n < a.length &
	(\forall int i; 0<= i & i < n; a[i] != null );
@*/
```
By itself, JML doesn't provide a way to automatically provide program verification.
Various tools such as ESC/Java2 to check JML specification
- runtime checking;
- program verification;

---
# Sandboxing

Sandboxing is the idea of creating **barriers** between processes so that interactions between processes or process to OS comunication is safeguarded.
### Compartmentalization

Is the idea of physically separating "modules" so that for each of them a smaller TCB (Trusted Computing Base) is necessary. For example a SIM provides a small TCB for small sets of trusted functionality, so that untrusted applications with larger TCB can safely interact with it.
Sandboxing is one of the standard way to compartimentise software, so we have policy and permission control.

#### Compartmentalization in Chrome

Works by having a deeper browser kerne, which stores cookies, and psw, network stack and the window management, then for each tab we have one separate rendering engine which doesn't have access to local file system and to each other.


### OS access control

Although good, still limits program access to sensitive data, there are some issues like **size of TCB**, too **much complexity** and **not enough granularity**.
For example, UNIX access control uses 3 permissions and 3 categories of users for files and directories, while Windows XP used 30 permissions, 9 categories of users and 15 kinds of objects. This increase **complexity** and arise misconfigurations.
Also, OS cannot distinguish components within process, so can't differentiate access control for or between them.

### Language-level sandboxing

With language-level sandboxing, part of that is passed through an execution engine, like the JVM or the .NET VM which provides security and sandboxing guarantees, so that also different modules within the same process provide some sort of isolation.

#### Stack walking
Every resource access or sensitive operation protected by a demandPermission calls that appropiate permission P, so that only authorized calls get passed. This algorithm is based on stack walking.
```
access allowed iff
	all components of the call stack have the right to access the resource
```

sometimes it may be too restrictive, so for that reason there exist stack walk modifiers like `enable_permission(P)` that give caller permission for a given right.

```
DemandPermission(P):
1. for each caller on the stack, from top to bottom:
    if the caller:
	    a) lacks permission P;
	    b) has disabled permission P;
	then:
		throw exception
	elif:
		c) has enabled permission P;
	then:
		return;
		
2. check inherited access control context.
```
Beware of **TOCTOU attacks**, in which mutable objects may be checked but modifier after their checks. An attacker could change the value of filename after the checks, in a multi-threaded execution.

### Hardware-level sandboxing

In the case of unsafe langauge (like C), there is no sandboxing techniques, so we use the support provided by underlying hardware/linux kernel (like VM and contenerization).

- **Secure enclaves** isolates part of the code together with its data. It's less flexible than stack walking, but it's more secure because of OS & Java VM are not in the TCB;
- **Intel SGX** provides the hardware support for enclaves, which provides a form of trusted execution environment even with regards to the OS.
---
# Program obfuscation

It's not encryption, it's not packing, it's a way to render source code unreadable. It's done to protect reverse engineering and illegal modifications. Transform the application into one that is functionally identical to the original but is more difficult to reverse engineering.
Quality of obfuscation is evaluated according to four criteria:
1. **Potency**: How much obscurity it adds to the program;
2. **Resiliance**: How difficult is to deobfuscate;
3. **Stealth**: How well obfuscated the code is;
4. **Cost**: How much computational overhead it adds to the obfuscated app;

Works in 3 different ways:
- **Layout obfuscation**: changes or removes informations, removing variable/function name, removes comments, ecc;
- **Data obfuscation**: changes the data structures in the program, like doing variable encoding, splitting, merging, ecc;
- **Control flow obfuscation**: affects the original control flow logic by reordering statements, introducing dummy control flows, aggregation, spurious computations via opaque predicats.

## Program slicing

	To avoid program comprehension, we use a technique called program slicing, which consists of slices. A slice of a value v in a particular code part p consists of all the program code that happens before p that affect the value v. With this is possible to create a system dependency graph to avoid reverse engineering.
To avoid obfuscation it's possible to insert fake dependencies so that automatic slicers will need to include that part too.