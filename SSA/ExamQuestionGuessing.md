Possible questions of the exam.
# Trending now! 🔥

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
# Random 🎰

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