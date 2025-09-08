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

### **Function pointer**
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

Focus points:
- Agile process that focuses to deliver to most possible for a short timeframe;
- Allows to rapidly and repeatedly inspect software;
- Business gets priorities, teams self-organize to deliver the highest priority features;
- Every 2 weeks see release and choose if continue to enhance;

SCRUM is wildly used.

Characteristics:
-> Self organizing, in peer to peer way;
-> System progress in 2-4 weeks sprints;
-> Requirements are captured as items in product backlog;
-> No specific engineering practices required;
-> One of the agile process;

## Agile manifesto

>[!MANIFESTO]
> 	Individuals are interaction over process and tools  
> 	Working software over comprehensive documentation  
> 	Customer collaboration over contract negotiation  
> 	Self orgnizing teams  

## Scrum sprints

We go with sprints of 2/4 weeks, iterative process.
There are 2 types of development:
- **Sequential**: Sequential means that the development is executed step by step with a linear increase and a block assemble;
- **Overlapping**: The development is processed simultaneously and then peace of code are merged;

## Scrum Framework

### Roles
- **Product owner**: responsible for ROI, accept or reject work result;
- **Scrum master**: represents management of process, basically organizer;
- **Team**: 5/9 people, cross functional and self organized;

### Ceremonies
- **Sprint planning**: Sprint goals, items selected from backlog and high level design is considered;
- **Daily scrum**: Daily meeting that the team should do, just with the purpose to assign day-to-day tasks.
  In this cerimonies sprint review are made to present the product of the sprint. Team and product problems are overviewed.

### Artifacts
- **Product backlog:** Requirement collected in a list (what the software should do);
- **Sprint backlog:** Part of the product backlog;
- **Burndown chart:** Chart with analysis of what has been done;

### User stories

Agile development process create requirements as "user stories" which usually takes the form of:
"As a [user role], I want to [action], so I can [reason]"
with every user story there will be a story point associated with it, rating the effort needed to implement it ( usually scale 1-10 ).

### Pre-requisite collection
User stories should be:
- **S**pecific;
- **M**easurable;
- **A**chievable;
- **R**elevant;
- **T**imeboxed;

SaaS often face users
Avoid What-I-Said-But-Not-What-I-Want

## Software Quality

### ISO 25010
Evaluates software quality. We have 4 main components:
1. Code;
2. Procedures;
3. Documentations;
4. Data associated;

Types:
- Error:
- Fault:
- Failure:

We need to avoid faults/failures. We can dto this by analyzing error sources:
1. Defective requirements;
2. Customer-developer misunderstanding;
3. Deliberate deviations from requirements;
4. Design errors;
5. Coding errors;
6. Non-standard coding;
7. Test phases too short;
8. Documentation error;

Typical errors:
- Data reference: referring of non-initialized data;
- Computational errors: overflow, expressions incorrectly formatted;
- Comparison error: incompatible comparison, boolean expression incorrect;
- Control flow error: problems inherent with the algorithm itself usually;
- Interface errors: incorrect order or number of parameters, incorrect measurements units;
- IO error: EOF handling and right types;

### Cost analysis
We have:
- Cost for quality: made of prevision cost plus appraisal cost;
- Cost of insufficient quality: internal faliure cost plus external failure cost;

This 3 property cannot co-hexist ( good, cheap, fast ) in a product. Mh ok.

#### Software Quality Assurance

Set of actions that allow to have confidence that product conforms to set of technical requirements. Evaluate the process of sw productions.
The aim is also that of evaluating the process of software productions.

To have good software we want to have good quality requirements.
We have:
- User requirements;
- Technical requirements;

### SQUARE
In 1991 ISO9127 we have software engineering product quality, revised and published in 2001 but up on McCall and Boem models.
In 2011 was revised as 25010 and was called:
**S**ystem and software **QUA**lity **R**equirements and **E**valutaion.
It's unlikely that all of squares point are applicated independently in perfect way. It's also very abstract, it's not a guide on "how" to write good software.

#### Objectives
recipients are:
- users;
- developers;
- clients;
- sys admin;
- customers;

#### Quality in use
- Effectiveness: number of reached objectives;
- Efficiency;
- Satisfaction: usefulness, trust, pleasure, comfort;
- Freedom from risks: economics, health, ecc...;
- Context coverage: completeness, flexibility, ecc...;

#### Product Quality model
###### Functional suitability:
- Completeness;
- Correctness;
- Appropiateness;

###### Efficiency:
- Time complexity;
- Resource utilization;
- Capacity;

###### Compatibility:
- Co-existence;
- Ineroperability;

###### Usability:
- Appropiateness;
- Learnability;
- Operability;
- User-error protection;
- Aesthetics of interface;
- Accessibility;

###### Reliability:
- Availability;
- Fault tolerance;

###### Security:
- Confidentiality;
- Integrity;
- Authenticity;

###### Maintanability:
- Documentation;
- Reusability;
- Analysability;

###### Portability:
- adaptability;
- Replaceability;

#### Quality cost
It's an equilibrium between costs of non compliance and conformity.
Qualities are SOFTWARE SPECIFIC.
ISO (**I**nternational **S**tandard **O**rganization) is the international organization for general standards.

#### Measurements
3 types of meassurements:
- **Direct**: Detectable without influence of external factors;
- **Indirect**: Derived from measurements of one or more attributes; 
- **Indicators**: Measurements derived indirectly from other measurements;

Measurements has:
- **Dimension** -> Memory footprint;
- **Occurrences** -> Number of comp. wans;
- **Time** -> Time to elaborate data;

#### Create measurements:
- Dividing by time (rates/frequences/time proportions);
- Occurences (proportions, mean, time interval);
- Size (densities, efficiency);

## DevOps
Means synergy of software development and operations.
It's a tool for automating some of the process, it increase velocity, reduces downtime and human errors. It's proven by experience to be working.

DevOps creates a sense of shared responsibility, increasing collaboration across development and "business", and automates the software delivery process and infrastructure changes.

#### The basics
Code Repo -> Build -> Test -> Deploy -> Monitor -> Devs
    |-------------------------------------------------|
It is an iterative approach that emphasizes importance of quick software delivery. Collaboration between custmer and developer for continuous improvement is foundmental in DevOps.

#### Concepts
###### Continuous integration
It's the process of integrating code into a mainline code base.
Uses the concept of a source control server, that integrates developers code into shared repository multiple times a day.
May be difficult to set up first, especially for the automated testing phase. Partial code could completely make any test fail. This is **made by the developer**.

###### Continuous delivery/deployment
Having continuous deliver means that software is always production-ready, being able to be released quickly and automatically.
Continuous deployment means to actually release the good builds to the users, it's next step to continued delivery. This process is **completely automatized**.

CODE -> BUILD -> INTEGRATE -> RELEASE -> DEPLOY
|--- Continuous Integration ---|
|--------- Continuous Delivery -------------|
|-------------- Continuous Deployment ----------------|

#### Creating DevOps toolset

- **Collaboration**: Sharing knowledge by comunication;
- **Planning**: Provides transparency to stakeholders;
- **Source control**: Tools that make up building blocks for the entire process;
- **Issue tracking**: Increase visibility of issues;
- **Configuration management**: Tools to guarantee consistency at scale;
- **Database devops**: Database tool;
- **Continuous integration**: Provide feedback loop by merging code;
- **Automated testing**: They test the code;
- **Development**: Tools to do the actual deployment;

## SysML
Creation of systems, not just softwares. Abstract modeling of generalization of complex system.

Different parts:
BDD = Class diagram, with we want to design system, very similar to UML.

Associations possible:
-> Generic: Two blocks "cooperate" in some way;
-> Composition: Component blocks can exist in context of the owner "composite" block.
-> Aggregation: Composite contains the components, but components exist outside the composite
-> Generalization/Specialization: Specialized blocks has all the properties of the generalized, but can add some more;

##### Internal block diagram:
Defines the use of blocks in composition. Internal structure becomes expliciting with signaling and comunication.

###### SysML Ports
Specifies interaction pouint on blocks and ports
Integrate behaviour with structure, the syntax is:		
			`portname:TypeName`

Types of ports:
- Standard port (UML) operation oriented;
- Flow port uses for physical signals, and are typed by blocks;

###### Parametrics Diagrams
Can be used to express constraints between value properties. Constraint are defined by another block.
Bounding of constraints parameters to value properties of blocks (ex. vehicle mass bound to param m in $F = m\times a$).

###### Behavioural diagram
Is an activity diagram. Transformation of an input into an output through controlled sequence of actions (similar to dataflow).
SysML adds support for continuous flow modelling (continuous-time system).

Represent wide range of applications:
- Tradeoff between generality vs understandability;
Application spectrum is between 2 endpoints according to way activities accept input and provide output.
Non-streaming activities: activities that accept input only when start, and output only at the end.

At the opposite, streaming activities pass items between each other anytime while executing.

Modelling is advantage: obliges you to question yourself about the system you want to create.
Engineer build and design first, we don't want to represent reality.

### Foundamentals of Informations systems interoperability

#definition **Interoperability**
> Ability of two or more systems to adapt on a same kind of file, format or protocol

Examples are in amazon maneplace -> payment system -> payment -> shipment -> customer center

All modern systems are modules that work with other servers (by other information systems too).

###### Integration
Integration is the main issue. 
It is possible in different application domain level:
- GUI integration: done through Portals and mashups (should be done only when other options not necessary);
- Process integration: Process engines;
- Function integration: Message-passing, publish-subscribe system;
- Data-information integration: Is query based, no procedure to carry on, but just data;

###### XML Foundation
XML (Extensible Markup Lenguage) is an important standard for exchange of data, usually web-based application.
It's comprehended in trees, with elements containing texts, in which that can be added some attributes.
It supports the addition of generatores, pagers, ecc.
Features:
- Namespaces;
- Queries;
- Transformation;

Another common scenario is the creation of standards to describe domain specific informations utilizing XML files. Creates common data structures.
Well formed: Doc is written to syntax in xml;
Valid: Document is compliant with the schema;

XML is a good way to exchange data!
For example, we may want to export database information from a relationa DB. We can translate it through XML as a way to exchange data.

RPC (Remote Procedure Call) can be exposed to internet with standard.
We might have problems with security. Difficult to work over firewall for example. inver XML based protocol that could work over HTTP.

#### SOAP
Inter-application communication through HTTP, martialled over XML to encode. We have a SOAP envelope (standardized) with inside it a SOAP body, containing requests.
Response is the same.
Everything is standardized to use HTTP POST.

PROS:
- HTTP reliant;
- Portable and interoperable;
- Universally accepted;

CONS:
 - Too much reliant over HTTP;
 - Stateless;
 - Serilaization by value, not by reference;

It comunicates by RPC or by documents, used maybe more as a request-reply of docs.

The **Service Description** is a way for clients to discover interfaces, XML that provides intefaces using WSDL (Web Service Description Lenguage).
It's a contract between requestor and replier, divided in 2 different parts:
- Definition -> how to use it;
- Implementation -> where to find it;


#### Restful services
Representational status transfer is a paradigm, references to simple application interfaces transmitting data over HTTP without any additional layers (requires specific archytectural styles).
Metaphorically, we have URIs as nouns and HTTP commands as verbs. Has state, meaning having application state.
Methods have been previously defined -kinfd of RPC-.
Example:
`getStokcPrice` cannot be linked to a verb (GET, POST, PUT).
if we change it to `currentStockPrice` it does
Verbs are:
- `GET`
- `PUT`
- `POST`
- `DELETE`

##### E-Services
Application accessible via the web, that provides functionality to business or individuals.
Another definition, more popular and mature, defines component as an e-service following 3 points:
- **Openness**: Indipendent, as much as possible, of specific platforms and computing paradigm;
- **Developed mainly for inter-organization applications**: Used mostly between different organization/application;
- **Easily composable**: Well bounded, so that it can be used as building block for other applications.

##### Web-Service
W3C standardized also tis term: SW system identified by URI, whose public interfaces and bindings are defined using XML.
Definition is discoverable by other systems, and used mostly by other systems.
XML because REST wasn't still invented.
For building an e-service, a designer may need to implement many web-services.

##### Process orchestration
We need to orchestrate everything so that every information acquired using interoperability is correctly used.
This can be done by modelling it through a SysML diagram.
Internally, other than having diagrams to represent orchestration, usually YAML or XML gets used to actually orchestrate.
For projects, we need to have:
- Containers
- Orchestration
Docker is used to compose XML.
PetriNCT is a mathematical semantic model to describe orchestration, and is useful for formal verification.


### Domain Driven Design and microservices
We have small, indipendent modules contanerized, that can communicate between each other. Microservices are deployed per container.
Idea is agile methods and devops, in which we deliver small chunks of functionalities, derived also with user stories.
It's a MODEL-DRIVEN design approach, based on collection of principles and pattern that helps crafting elegant systems.
A DDD is a way to get set of priorities to deal with complicated domains.

A **domain** is a sphere of knowledge or activity. Whan an organization does and the world it does it in.

A **model** is a system of abstractions that describes selected aspects of a domain and ignores extraneous details. It's a distilled version of a domain. It should model EVENTS-OF-LIFE, and it is independent from the methodology of programming.

It's ubquitous lenguage, in which models are explored in collaboration between domain and software practicioners.
Focuses on the core domain, that bounds models and implementation together.
A change in lenguage means a change in model and code.
Model expression is agnostic to the modellng lenguage, and it's expressed in the code.

###### Big design upfront
When building a model with this way, at the they'll be created "clusters of concept" similar to one another.
EX:
e-commerce
- promotion
- shipping
- product building
- inventory
- ecc...

###### Bounded contexts
Clusterizes concepts related to one another ( that they'll be candidates for microservice building ).
Domain can be generalized int 3:
- **Core**
- **Supporting**
- **Generic**

###### Layers
We have multiple layers:
- User interface (presentation layer);
- Application (app. logic);
- Domain (app. logic);
- Infrastructure.

The infrastructure is a supporting library for all other layers.
The domain layer contains the idea of the domain. Persistence is infrastructure's job.
The application layer is an orchestration that coordinates information presentation.
Concepts will be modelled as:
- Entities;
- Factories;
- Value object;
- Aggregates;
- Services;
- Association;
- Modules;
- Repositories;
