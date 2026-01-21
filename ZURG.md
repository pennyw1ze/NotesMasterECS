# Information Flow
### 1. 
 Considering 
```
String hi;
String lo;
```
Which program fragments may cause problems if hi has to be kept confidential?
1. hi = lo (chill)
2. lo = hi (leak of information)
3. lo = '1234'
4. hi = '1234' (leak)
5. println(lo) (chill)
6. println(hi) (leak)
7. readln(lo) (chill)
8. readln(hi) (chill)
Confidentiality is violated whenever information flows from **hi (secret)** to **lo (public)** or to public output, so for this reason issue is with 2 and 6.
### 2.
Considering 
```
int hi;
int lo;
```
 Which program fragments may cause problems for confidentiality?
 1. if (hi > 0) {lo = 99;}
 2. if(lo > 0) {hi=99;}
 3. if (hi > 0) {print(lo);}
 4. if (lo > 0) {print(hi);}
all of them are problematic, given that we are violating the confidentiality by a form of side/hidden-channel. By reading/Writing lo we can do inference or outright know the value of hi. 
### 3.
Describe Java Stack Walking
The java stack walking algorithm is structured like this
```
RequirePermission(caller, P):
	CheckPermission(caller, P)
	if explicitPermission
		accept
	if explicitNegation
		refuse
	RequirePermission(parent(caller), P)
```

This is a balance between ease-of-use of permission management (not each function needs to have all of their permission explicitly set) and security. This means that, unless specified explicitly, the callee gets the permissions of the caller.
For example, if A calls B that calls C, A has writePermission, and C requests it, the stack walker:
1. checks Cs permission, not finding an explicit allow/deny goes to B
2. checks Bs permission, not finding an explicit allow/deny goes to A
3. Checks As permission and finds an explicit writePermission
### 4. 
Justify the type system rule of command $\huge \frac{\gamma \vdash e : t \ \ \gamma \vdash c : \text{cmd } t }{\gamma \vdash \text{while } e \text{ do } c\text{ cmd } t}$ 
This means: given the expression e with information flow of type T, and the command C of type t that are well typed, the while command that deals with types t is well typed.
This, although formally correct, doesn't deal with the non-interference relative to the non-termination rule, which means that if we deal with information level HIGH we can leak info based on timing/non-termination. Although way more restrictive, this is protected if we provide the while only with expressions/actions that deal with type LOW
### 5. 
**2(a). Non-interference (ignoring non-termination and timing)**
Program P is non-interferent if, for any two initial states that agree on the value of **LOW**, the final states after execution also agree on **LOW**, regardless of differences in **HIGH**. This means confidential information in **HIGH** cannot influence public outputs.

**2(b). Non-interference with exceptions and covert channels**  
When exceptions are considered, P is non-interferent if both the final public state and the occurrence (or absence) of exceptions observable at the **LOW** level are independent of **HIGH**. This prevents leaks through exceptional control flow or termination behavior.

# Design Principles
### 1.
1. An insecure fail can lead of information leaking. This can be shown in examples like a side-channel SQL injection, which leaks informations about thruth values, for examples, by timing/analyzing the error output
```sql
SELECT *
FROM users
WHERE username = 'root' and psw = '1234' OR 1/0
```
the 1/0 generates an exception, which if not dealt internally can leak informations.
2. With Virtual Machines we strenghten some of the design systems, like defense in depth, compartimentalization, minimizing attack surface. THis is because the hypervisor is the one in charge of keeping the guest system as physiscally isolated as possible. 
### 2.
Describe three principles of secure design. Provide examples of violation of the chosen principles:
1. **Using secure defaults**: Using secure defaults means that a system should be safe immediately after installation, without requiring users to change settings. By default, features should be configured with the most restrictive and secure options enabled. This reduces the chance of human error or misconfiguration causing security weaknesses. Secure defaults assume users may not fully understand security implications.  A violation of this principle occurs when software installs with default passwords like “admin/admin”. Another violation is when encryption is disabled by default and users must manually enable it. Open network ports enabled by default also represent insecure defaults.
2. **Security in depth**: Security in depth (defense in depth) means using multiple layers of security controls rather than relying on a single mechanism. If one layer fails, other layers continue to protect the system. These layers can include firewalls, authentication, encryption, and monitoring. This approach reduces the impact of any single vulnerability. A violation occurs when a system relies only on a firewall but has no authentication or input validation. Another example is storing sensitive data unencrypted because access control is assumed to be sufficient. If one control is bypassed, the entire system becomes exposed.
3. **Minimize attack vectors**: Minimizing attack vectors means reducing the number of ways an attacker can interact with or exploit a system. This is achieved by disabling unnecessary services, removing unused features, and limiting exposed interfaces. Fewer entry points make the system easier to secure and monitor. Every additional feature increases potential risk. A violation occurs when unused services are left running on a server. Another example is exposing administrative interfaces to the public internet. Allowing multiple unnecessary APIs or open ports also increases the attack surface and violates this principle.
4. **Least Privilege**: The principle of least privilege states that users and processes should only have the minimum permissions necessary to perform their tasks. This limits the damage that can occur if an account or process is compromised. Privileges should be carefully assigned and regularly reviewed. Temporary privileges should be removed when no longer needed. A violation occurs when users are given administrator rights for convenience. Another example is an application running with root or system-level privileges without necessity. Shared accounts with broad permissions also violate least privilege and increase security risk.
5. **Fail securely**: failing securely means that when a system encounters an error, it should default to a secure state rather than an open or unsafe one. Errors should not grant additional access or expose sensitive data. This prevents attackers from exploiting system failures. Secure failure behavior limits damage during unexpected conditions. A violation occurs when a system crashes and disables authentication checks. Another example is when error messages reveal database credentials or system paths. Allowing access when validation services are unavailable is also a failure to fail securely.
### 3.
Given the specifications of the protocol, there are few tests that need to be done:
1. Messages that are malformed in their lenght (also empty messages)
2. Messages that are malformed in their contents
3. Messages that are malformed in their order
4. Missing message
5. Replayed messages
6. Correct message chain, maybe even with timeouts
A form of testing may be using a fuzz testing with its generator purposefully created for this protocol. This form would go through all of these specifications, because it would be programmed that way
Symbolic execution can be used if the source code of the protocol implementation is available. Instead of concrete inputs, the program is executed with symbolic values, allowing exploration of different execution paths and protocol states. This can reveal missing checks, logic errors, or security-critical branches that can be bypassed. However, symbolic execution is computationally expensive and does not scale well for complex protocols with loops or many states, so it is usually applied only to small or critical components.
# Race Conditions
### 1. 
TOCTOU is an inherent vulnerability regarding any multithreaded workflow. It consists of the issue that presents when a specific piece of code needs to check input before doing something. Once the input has been checked, it is deemed safe, while another thread could be able to modify it before running the main piece of function on it.
When, for example, a webserver that has a global counter `x` and runs the code `x = x + 1` we are dealing with two different operations:
1. A retrieval of `x`
2. An assignment on `x`
if two thread retrieve `x` one after the other but before assigning `x+1` on i, after the execution we will have `1`, even though both threads have summed `1` on it.

# Program Verification
### 1. 
1. Runtime monitoring observes a program while it executes and can detect policy violations in real runs, but it introduces runtime overhead and cannot guarantee that violations will not occur in unobserved executions. Formal verification analyzes the program before execution and can prove that certain properties always hold, but it is complex, time-consuming, and requires precise specifications.
2. Proof Carrying Code is based on the idea that a code producer supplies executable code together with a formal proof that the code satisfies a given safety policy. The consumer only needs to verify the proof instead of re-verifying the program itself. This makes PCC more efficient for the consumer than full formal verification, since proof checking is significantly cheaper than proof construction.
### 2.
Static analysis examines a program without executing it and reasons about all possible executions, while dynamic analysis analyzes a program during runtime by observing actual executions and concrete inputs.

Program analysis is limited by undecidability, scalability issues, and incomplete knowledge of runtime behavior. These limitations are addressed by overapproximation, where the analysis safely includes all possible behaviors, possibly reporting false positives but guaranteeing that no real errors are missed.

# Java Programming Rules
### 1. 
Describe one of the rules for secure Java Programming
Never return a mutable object to untrusted code, and never store a reference to a mutable object obtained from untrusted code. This is because of attacks such as TOCTOU, which exploit the fact that the checking phase and the execution phase are distinct. After a check, a malicious user may be able to change the mutable object so to make it not valid anymore.
# Code Obfuscation
### 1.
Two techniques are data obfuscation and control flow obfuscation, and two measures of code complexity are **Cost** which measures how much computational overhead it adds to the obfuscated app, and **Stealth**, which measures the way in which how much can the obfuscated code blend in with "normal" code.
Control‑flow obfuscation transforms a program so that its execution order becomes difficult to understand, while preserving its original behavior. It introduces misleading structures such as unnecessary loops, conditional branches, or opaque predicates (conditions that always evaluate to true or false but are hard to reason about). This technique increases complexity metrics (especially cyclomatic complexity), making reverse engineering and static analysis more difficult.