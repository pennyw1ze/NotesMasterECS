# Software estimation
How to estimate the cost of software development.

## **Direct metrics:**
### **Line of codes**
How many lines are there in your code. Is the most practical method. Usually it is used in terms of thousands line of codes (1000 LOC = KLOC).
It also involves a "fault rate":
- Internal error/kloc;
It may depend on the programming lenguage or on the style of code.
ISO standard doesn't use LOC to estimate the cost of a software software.

### Function pointer
Proposed in the 80s, it's an empirical formula based on functionality.
It is ISO standardized and expands the proposal.
Why start by considering the application a black box, and we want to measure how many external input, external output and external query does the app offers to a user.
In order to evaluate the software, we must consider the following elements:
- **ILF** (Internal Logical File): user identifiable group of data mantained within the boundaries of the application;
- **ELF** (External Logical File): user identifiable data mantained by another application;
- **EI**: process data that comes from outside of the application;
- **EO**: elementary process that sends data outside of the application. The process logic contains a mathematical formula;
- **EQ**: elementary process that sends data outside of the application. The process logic does not contains a mathematical formula;
Then we also got:
- **DET:** user identified single field with ILF/ELF;
- **RET:** user identified group of fields with ILF/ELF;
ILF/ELF complexity varies, depending on the amount of needed DET/RET;
**FTR** (File Type Referenced), read/write ILF or read ELF (Basically a modifier ILF/ELF);
Then we use a table, with entries the type of function (EI, EO, EQ), the number of calls of this function and the price for each of this call, then we multiply and sum them and get the total value. 
So, in the end, function pointers are a way to objectively determine the functions in the application through the analysis of 14 indicators, and from there get LOC estimation.
Each indicator **get assigned a value** between 0 and 5.$$AFT = Total\times (0,65\times 0,01\sum_{i=1}^{14}F_i$$
Where $0,01\sum_{i=1}^{14}F_i$ Represents the corrective factors.
This evaluation has a few advantages:
- It's a **programming-independent** way to assess functionalities;
- It is **accurate**;
- The calculation is **objective**;
- There are **certifications** for it;
And disadvantages:
- It has a **legacy terminology**;
- **Incompleteness**;
- It requires **different revisions**;

### Effort Estimation

It consists in the analysis of the requirements, and evaluates costs, effort and time spent by the software.
It basically follows this step:
- From requirements to Function Pointers;
- FP -> LOC;
- LOC -> Time/Effort;
- Effort -> Cost;
## **Indirect metrics:**
- **Service levels:** somehow is provided a grade for your software based on the percentage of the portion covered of a bigger software;

## CoCoMo 
Sometimes, software 