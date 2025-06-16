# Software estimation
How to estimate the cost of software development.

## **Direct metrics**
Metrics that can extimate the costs directly from the code of the software.
### **Line of codes**
How many lines are there in your code. Is the most practical method. Usually it is used in terms of thousands line of codes (1000 LOC = KLOC).
It also involves a "fault rate":
- Internal error/kloc;
It may depend on the programming lenguage or on the style of code.
ISO standard doesn't use LOC to estimate the cost of a software software.

### Function pointer
Proposed in the 80s, it's an empirical formula based on functionality.
It is ISO standardized and expands the proposal.
We start by considering the application a black box, and we want to measure how many external input, external output and external query does the app offers to a user.
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
Each indicator **get assigned a value** between 0 and 5.$$AFT = Total\times (0,65\times 0,01\sum_{i=1}^{14}F_i)$$
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

It consists in the analysis of the requirements, and  cost evaluation, effort and time spent by the software.
It basically follows this step:
- From requirements to Function Pointers;
- FP -> LOC;
- LOC -> Time/Effort;
- Effort -> Cost;
## **Indirect metrics:**

- **Service levels:** somehow is provided a grade for your software based on the percentage of the portion covered of a bigger software;

## CoCoMo
Constructive Cost Model.
Sometimes, software extimations made with file pointers are pretty optimistic, and needs to be done some adjustments with corrective measurements, expecially in big softwares.
We will extimate the effort M and the optimal time T.
The methods works with statistics and with the waterfall method idea.
We produce an effort estimation based on:
1. Analysis and planning;
2. Design;
3. Development;
4. Integration and test;

**M: Effort**
Man-time required to develop the project.
Man effort is considered in the following measure unit:
MM = 19 days of 8 hours = 152 hours.
In general is:$$M = aS^b$$
where $a$ and $b$ are parameters derived from the project type.

**T: Delivery time**
Required optimal time to deliver working software.
Is considered in Months.
In general we have that:$$T = cM^d$$
where $c$ and $d$ are parameters derived from the project type.

**ManPower**
Number of people working during the project:$$Manpower = {M\over T};$$
**Adjusting parameter**
We need parameters evaluated from very low to extra high.
- very low
- low
- nominal
- high
- very high
- extra high

We can have 3 model types, based on the detail level that we want to achieve:
- **Organic**;
- **Semi-detached**;
- **Embeded**;

In CoCoMo we do **not consider requirement analysis and planning** since they should not be done in software side.

CoCoMo is too generous (extimation error around 20%-60%).

## CoCoMo ll
We have CoCoMo ll for the following motivations:
- New lifecycle software models;
- Reuse;
- Different level of extimation precision;
This technique is based on interpolation of statistics.
We have the new formulas:
- $PM = A\times SIZE^E \times \Pi _{i=1} ^ nEM_i$
  where $E = B + 0,01\times \Sigma _{j = 1}^5 SF_j$;
- $TDEV = C(PM_{NS})^FSCED/100$
  where $F = D + 0,2(E-B)$;
- $A,B,C,D$ are constants that are calibrated depending on time.

**Backfiring**
Based on statistics, tells us how much LOC per function pointer we need.

**Effort multipliers**
There are some multipliers that are used to compute the cost of a software, that depends on different factors such has:

**product**:
RELY: SW reliability;
DATA: Database size;
CPLX: Product complexity;
RUSE: SW reusability;
DOCU: Documentation level;

**system**:
TIME: TIME constraint;
STOR: STORage constraint;
PVOL: Platform VOLatility;

**personal**:
ACAP: Analist capability;
PCAP: Programmer capability;
APEX: Application experience;
PLEX: Platform experience;
LTEX: Language and tool experience;
PCON: Personal continuity;

**project**:
SITE: Multisite development;
TOOL: Use of software tools;
SCED: Schedule constraints;

## Process Model
It's a structured collection of practices that describe characteristic of effective processes proven by experience.
It's used to:
1. To set process **measurable** objectives and priorities;
2. To ensure stable, capable, and mature **processes**;
3. As a guide for **improvement** of projects and organizational processes;
4. To diagnose/certify the state of an organization’s current practices;

### CMMI
CMMI (Capability Maturity Model Integration) is a process improvement approach.
Can be used as:
- A collection of **best practices**;
- Framework for **organizing** and **prioritizing** activities;

CMMI organize components, and components are organized in costellations:
![[Pasted image 20250615182658.png]]

There are 5 levels of achievements of a company defined in a piramid:

0. **INCOMPLETE**: no generic goal exist for this level;
1. **PERFORMED**: supports and enables the work needed to produce work products. Everything happens ad-hoc.
2. **MANAGED**: process that has basic infrastructure in place to support the process. Planned and executed with a policy. Has organization visions, even in periods of stress.
3. **DEFINED**: very well structured, tailored from the organization's set of standard processes;
4. **QUANTITATIVELY MANAGED**: process that are defined are controlled via statistics and wauntitative measurements, it's managed by the life of the process;
5. **OPTIMIZING**: improving process based on understanding of the common causes of variations inherent on the process itself. The measurements of the past improve the futures.

Any CMMI model contains multiple areas.
General goal and practitces apply to multiple process area:
- Performed;
- Managed;
- Defined;

Each process area has 1 to 4 goals, and each goal is comprised of practices.
- Process management;
- Project management;
- Engeneering;
- Support;

the more you want to go up the the pyramid, the more PAs you need.

**ISO 12207**
Standard for software lifecycle process.
Standard that deals with software engeneering.
Define structures and activities involved in the sw dev process.
Use of common lenguage to involve stakeholders.

Functional approach: activities that go from input to output.
- **Five** primary lifecycle processes related to primary involved agents:
	- buyers, suppliers, developers, maintainers, operators, managers and technicians;
- **Eight** supporting life cycle processes;
- **Four** organizational processes;

The standard is based on two basic principles:
**modularity** and **responsibility**

**5** Primary **life cycle processes**:

	5.1 Acquisition
	5.2 Supply
	5.3 Development
	5.4 Operation
	5.5 Maintenance

**6 Support processes**:

	6.1 Documentation
	6.2 Configuration management
	6.3 Quality assurance process
	6.4 Verification
	6.5 Validation
	6.6 Joint review
	6.7 Audit
	6.8 Problem solving

**7 Organizational processes**:
	
	7.1 Management
	7.2 Infrastructure
	7.3 Improvement 
	7.4 Training

Each process has a set of outcomes associated with it and is detailed in
terms of activities.

### ISO 9000
Mantained by ISO organization, address quality management.

It is intended for being used in any organization which designs, develops, manufactures, installs and/or services any product or provides any form of service.

It provides requirements which an organization needs to fulfill if it is to achieve customer satisfaction.

It includes requirement s for the continual (i.e., planned) improvement of the Quality Management System.

**Disadvantages**
- Too abstract;
- Costly to obtain and maintain;
- Lengthy time-scale to obtain certification;
- Time-consuming development;
- Difficult to implement;
- Organizational resistance to change;
- Staff resistance to change;
- Hard to maintain enthusiasm for the system;
- More documentation;

The Quality System as a **series of processes**:
1. Quality management system
	- It deals with general and documentation requirements that are the foundation of the management system
2. Management responsibility
	- know customers' requirements;
	- set policies and objectives;
	- plan how the objectives will be met;
3. Resource management
	- It deals with the people and physical resources needed to carry out the process;
4. Product/service realization
	- It deals with the processes necessary to produce the product or to provide the service;
5. Measurement, analysis, and improvement
	- It deals with measurements to enable the systems to be monitored;


## Core Process Activities

**Specification**
in this part we establish what our services needs to do:
- Functional requirements;
- Non-Functional requirements;
This is done through a 4 step process:
1. Feasibility study;
2. Requirements elicitation and analysis;
3. Requirements specification;
4. Requirements validation;
In this part we can create a system model, the user's and the system requirements, and we create a document.

**Design and implementation**
It's the process around going from specification to an actual running system. This goes through 2 different sub-phases:
- Software design, that goes through a structured design of the software without programming it;
- Implementation which the actua LOC are written and the system goesa  running state.

**Validation**
Unit and system testing, debugging phase.

**Evolution**
Software needs to be documented, easily mantainable.

# SCRUM
