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
