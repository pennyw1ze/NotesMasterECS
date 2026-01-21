# Actual Exam Questions (and answares)

### Information Flow (max 6pts)
1. Describe what the Java stack walking algorithm is and how it works.

2. Consider a program P with two variables, HIGH (with confidential information) and LOW (with public information). $s_1 =H s_2$ indicates that the two states associate the same value for the variable high, similarly for$s_1 =L s_2$. 	(a)Define what it means for the program P to be non-interferent (ignoring non-termination and timing); (b) Now assume that the program P may throw exceptions. Define what it means for the program P to be non-interferent considering exceptions and covert-channel.

3. In the type system approach to information flow, illustrate and justify the “while” rule.$$γ ⊢ e : t\ \ \ \ \ γ ⊢ c : cmd\ t \over γ ⊢ while\ e\ do\ c:cmd\ t$$What is the main problem with this rule? How can it be fixed?

#### Answare
1. **Java stack walking** is a runtime access-control mechanism used by the JVM to enforce security decisions based on the **entire dynamic call stack**, not just the immediate caller.
   When a sensitive operation (e.g., file or network access) is requested, the JVM **walks the call stack**, examining each stack frame’s **protection domain** and its associated permissions. The operation is allowed **only if every method on the call chain has the required permission**; if any caller lacks permission, access is denied.
   Sometimes it may be too restrictive, so for that reason there exist stack walk modifiers like `enable_permission(P)` that give caller permission for a given right.
   Beware of **TOCTOU attacks**, in which mutable objects may be checked but modifier after their checks (an attacker could change the value of filename after the checks, in a multi-threaded execution).
2. (a) Program P is **non-interferent** if changes in the confidential variable **HIGH** cannot affect the observable public output **LOW**. Formally:
   For all initial states $s_1, s_2$ if $s_1 =_L s_2$ (same public inputs), then after executing P,$$P(s_1) =_L P(s_2)$$That is, final public states are equal regardless of differences in **HIGH**.
   (b) When exceptions are allowed, program P is **non-interferent** if **both public outputs and observable exceptional behavior** are independent of **HIGH**. Formally:
   For all initial states $s_1, s_2$​, if $s_1 =_L s_2$​, then executions of P from $s_1$​ and $s_2$:
	- Produce the same public outputs (**LOW**);
	- Either both terminate normally or both throw the **same observable exception**.
   Thus, neither **LOW values nor exception behavior** may leak information about **HIGH**, preventing exceptions from acting as **covert channels**.
3. The expression e and the command c are both typed at security level t.  This ensures that any information used to control the loop (the guard) is at most as sensitive as the information possibly modified by the body. Thus, no information at a higher level than t can be leaked through the loop’s effect on variables of lower level. If the condition e depends on a **HIGH** variable, then the number of loop iterations may depend on HIGH, leaking information through a **termination covert channel**, even if no LOW variables are explicitly assigned. To fix this, it must be ensured that LOW security level is applied to the loop condition:$$γ\ ├\ e : L\ \ \ \ \ γ\ ├ c :cmd\ L\over
γ├\ while\ e\ do\ c : cmd\ L$$

### Java Programming Rules and Obfuscation (max 6 pts)
1. Describe one of the rules for Secure Java Programming
2. Name two techniques for code obfuscation and two measures of code complexity (relative to obfuscation). Describe at least one of the two techniques and provide an example.
#### Answare
1. (EASY)**Prevent representation exposure**: Never return a reference to a **mutable object** (including arrays) to untrusted code.(HARD)**Do not rely on package protection**: Because anyone can extend a package or create subclasses (unless the package is sealed or the class is final), package-level visibility is not a reliable security boundary;
2. **Program slicing** is the technique of selecting only the statements that influence a given variable or program point, garbling the rest to make the code harder to understand. An opaque predicate is a boolean expression whose value is known to the obfuscator but hard for an attacker to determine. Example: add an if condition like (x\*x + 2)%2 == 0. Measures of the code complexity are potency, resiliance, stealth and cost.

### Spot the defect (max 3 pts)
Find the vulnerability in the code and suggest changes to remove it. Do you think Flawfinder would have found it? motivate your answer.

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int bof (char *str) {
	char buffer [ 24 ] ;
	strcpy ( buffer , str ) ;
	return 1;
}

int main ( int arg c, char ∗∗arg v ) {
	char str [ 517 ] ;
	FILE ∗badfile ;
	badfile = fopen(”badfile”,”r”);
	fread(str, sizeof(char), 517, badfile);
	bof (str);
	print(”returned properly\n”);
	return 1;
}
```
#### Answare
- [bof] No control performed on str size overwrite the stack in strcpy function;
- [bof] No string termination character added to buffer;
- [main] No terminator character inserted in the string array;
- [both] No check on file or argument passed to functions;
- [both] No error handling;
Fixes:
- Check every input, add error handling and bound the strcpy to size 23. After strcpy and fread, add null terminating bytes to buffer and str.


### Language-Based Security
1. What is memory safety and what guarantees it offers?
2. Which of the following type-casts are safe in C? Justify the answer.
```C
1. char ∗x; int ∗y = ( int ∗ ) x
2. int ∗x ; void ∗ y = ( void ∗ ) x
3. void ∗x; int ∗ y = ( int ∗ ) x
4. int x ; char ∗ y = ( char ∗ ) x
5. char ∗ x ; int y = ( int ) x
6. int x ; float y = ( float ) x
7. float x ; int y = ( int ) x
```
3. Let us consider
```C
String hi; //s e c u r i t y l a b e l   s e c r e t
String lo; //s e c u r i t y l a b e l   p u b l i c
```
Which program fragments (may) cause problems if hi has to be kept confidential?
```Java
1. hi = lo;
2. lo = hi;
3. lo = ”1234”;
4. hi = ”1234”;
5. println ( lo );
6. println ( hi );
7. readln ( lo );
8. readln ( hi );
```
#### Answare
1. A language is considered memory safe if it prevents programs from performing invalid memory operations, corrupting his memory or the memory of other processes on the system. A memory safe language guarantees prevention from buffer overflow, provides a garbage collector (deallocating unused memory) and prevents unsafe memory access with pointer arithmetic.
2. 
	1. NO: pointer not aligned to int;
	2. YES: 
	3. NO: no guarantees x is pointing to a proper integer;
	4. NO: x might not be interpretable as a char;
	5. YES: every char is an integer;
	6. YES: adding 0s as floats;
	7. YES: ignoring floats;
3. 
	1. NO problem;
	2. YES revealing secret;
	3. NO problem;
	4. NO problem;
	5. NO problem;
	6. YES revealing secret;
	7. NO problem;
	8. NO problem;


### Design Principles and Testing
1. Describe three principles of secure design. Provide examples of violations of the chosen principles.
2. Suppose that for some protocol you know the format that messages have and the order in which these messages have to be sent. If you have to test an application that implements this protocol for security flaws, how would you do this? Mention at least two different forms of security testing that you could use for this, and explain the different kinds of security flaws that these forms of testing can reveal.
#### Answare
1. List:
	1. **Using secure defaults**: Using secure defaults means that a system should be safe immediately after installation, without requiring users to change settings. By default, features should be configured with the most restrictive and secure options enabled. This reduces the chance of human error or misconfiguration causing security weaknesses. Example: default password "admin";
	2. **Security in depth**: Security in depth (defense in depth) means using multiple layers of security controls rather than relying on a single mechanism. If one layer fails, other layers continue to protect the system. These layers can include firewalls, authentication, encryption, and monitoring. Example: storing unencrypted data because access control is active;
	3. **Minimize attack vectors**: Minimizing attack vectors means reducing the number of ways an attacker can interact with or exploit a system. This is achieved by disabling unnecessary services, removing unused features, and limiting exposed interfaces. Example: leaving unused service online;
	4. Fail securely, Least priviledge or others...
2. We could use intelligent fuzzing technique like generative fuzzing, in which given a specific format you can create an intelligent fuzzer that generates custom input and increase fuzzing efficiency. Another technique might be symbolic execution which might loose efficiency in presence of loops, but gives us mathematical security. A fuzzer is going to explore specific inputs in a more rapid and automatic and fast way, symbolic execution will exploit every possible vulnerability and every branch of the execution flow and can reveal also unexpected flaws.


### Spot the defect (max 6 pts)

1. Spot security flaws in the following Java code:
```java
public class String {
    public char[] contents;
    public int offset, len;
    // contents[offset ... offset + len]
    String() {
        contents = null;
        offset = 0;
        len = 0;
    }
    String(char[] a) {
        contents = a;
        offset = 0;
        len = a.length;
    }
    String substring(int take) {
        if (take <= 0) throw new NegativeSizeException();
        String s = new String();
        s.contents = this.contents;
        s.offset = 0;
        s.len = Math.min(take, this.len);
        return s;
    }
    int getLength() { return len; }
}
```
2. Spot security flaws in the following C code:
```C
char buff1[MAX_SIZE], buff2[MAX_SIZE];
if (!isValid(url)) return;
if (strlen(url) > MAX_SIZE - 1) return;
out = buff1;
do {
    if (*url != ' ')
        *out++ = *url;
} while (*url++ != '/');

strcpy(buff2, buff1);
```
#### Answare
1. Flaws:
	1. Confidentiality is violated here because the substring is sharing the same content of the full string. Technically there is no substring at all, the string is returned with the full content. 
	2. Also there is a collision with the class String in java.lang package. 
	3. The content, offset and length fields are public so anyone can modify them.
	4. When a new string is created given in input char[] a, this should be cloned before assigning to contents, because otherwise external attacker might be able to modify actual strings. 
	5. Also when a substring is created, the previous offset is ignored. This is a logical mistake.
2. Flaws:
	1. The logic of the program is wrong, why are we copying the url until we find the '/' character ? We are cutting away an important part of the url;
	2. Should add null terminating char to buf1;
	3. The url might not contain the '/' char at all, ending up in an infinite loop, with the cycle accessing unknown zone of the stack after the url string ended;
	4. `strcpy` on non null terminated buffer leads to unknown behaviour.
	5. Length check should be performed before checking if the url is valid.



### Design Principles (max 6 pts)
1. Show an example of how an insecure fail may lead to sensitive information leakage.
2. Which principle can be satisfied with the aid of virtual machines? What is the role of the hypervisor in a virtualization system?
#### Answare
1. For example, login with the wrong credentials. If the server answare like (SQL DATABASE ERROR: password not found), I leaked the type of database, the fact that the username existed but the password was incorrect and so on. Even by providing errors like 1/0 we can leak informations based on the time of response and on error logs.
2. Virtual machines help satisfy the **principle of isolation (and least privilege)** by running different systems in separate, isolated environments so that a compromise in one does not affect others (and reduce attack surface). The **hypervisor** is the trusted component that manages and enforces this isolation by controlling access to hardware resources (CPU, memory, I/O) and by mediating all interactions between virtual machines and the physical machine.

### Race Conditions (max 6pts)
1. Explain briefly what is TOCTOU (Time-Of-Check/Time-Of-Use) and show what can happen when two concurrent execution threads both execute the statement $x = x + 1$ in java.
#### Answare
The TOCTOU attack consists basically in exploiting that short interval of time that last between the time a check is performed and an an istruction which had to met some conditions is executed. Hackers use this attack to bypass security check, modify shared object (also via aliasing), inject filenames and instructions. The attack is hard to perform, due to the short interval of time, but that interval can be enlarged by pausing the checking process, and inject wathever before continuing his execution. In the case of x=x+1, it is more a concurrency problem. 2 threads might not achieve the goal by summing 1 instead of 2, grabbing x togheter before each other is able to sum 1 to x. A lock is needed in order to avoid this behaviour.







# end

---
# Exam Question Guessing

Possible questions of the exam.
## Trending now! 🔥

Just the more quoted:
1. How does the `mkdir` concurrency attack works ?
	   
2. Difference between bug and flow ?
	
3. Spot the vulnerabilities in the following smart contract, then write an attacker contract to exploit them and a fixed version:
```C
function withdraw(uint _amount) public {
	// Money transfer
	(bool success, ) = msg.sender.call{value: _amount}("");
	require(success, "Transfer failed");
	// Balance update
	balances[msg.sender] -= _amount;
}
```
4. Difference between safety and security ?
	   
5. Is the following type cast safe in C?
```C
4. char* x; int* y = (int*) x
5. int* x; void* y = (void*) x
6. void* x; int* y = (int*) x
7. int x; char* y = (char*) x
8. char* x; int y = (int) x
9. int x; float y = (float) x
10. float x; int y = (int) x
```
6. Which program fragments (may) cause problems for confidentiality?
```C
4. if (hi > 0) { lo = 99; }
5. if (lo > 0) { hi = 66; }
6. if (hi > 0) { print(lo);}
7. if (lo > 0) { print(hi);}
```
7. 
---
## Random 🎰

Added to cover every topic of the course:
1. Ways to analyze a secure software;
	   
2. SDL phases;
	   
3.  Capabilities levels in CMM;
	   
4. List the main language security features and briefly describe them;
	   
5. Main programming language abstractions and safety mechanism;
	   
6. Why is typing important in a programming language;
	
7. List everything you remember about the security programming guidelines in Java;
	
8. Advantages and disadvantages of aliasing techinques;
	
9. Pros and Cons of fuzzing system;