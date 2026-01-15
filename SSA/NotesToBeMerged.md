COMPLICATIONS
- **Concurrency**: even though the actual program might be satisfying some assertion, this might be invalidated by a concurrent program which is modifying the same variable.

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
}
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

Sandboxing is the idea of creating barriers between processes so that interactions between processes or process to OS comunication is safeguarded.
### Compartmentalization
Is the idea of physically separating "modules" so that for each of them a smaller TCB (Trusted computing base) is necessary. For example a SIM provides a small TCB for small sets of trusted functionality, so that untrusted applications with larger TCB can safely interact with it.