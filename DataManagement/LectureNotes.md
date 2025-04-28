# **Data Warehousing**
There are several **layers** in an application:

---

								Presentation

---

								Application

---

								   Data

---
The idea is to have one clean and well organized database, but in the reality there are a lot of replicas of them with variation in the real life applications.
It is possible also to have incoherent database in the same application.
It is also possible to have different database, with different types of storage (like a csv file and a relational database and so far).
There are also some data that must be accessed outside of the application boundary because they are stored in an external database.

**Open data**: data reachable from everyone published by an organization.

**OLTP**: On Line Transaction Processed: for example registration of a student or a professor in the database, that passes truth the presentation, application and data level.
ex: INSERT, DELETE, UPDATE, SELECT.

**OLAP**: The goal of On Line Analitica Process is to analyze online data, not inserting or removing or get just one data, but the process of analyzing them. For example, Machine Learning is based on data analysis and therefore OLAP.

#definition 
> A **data warehouse** is a repository that integrates and reorganize data collected from different sites and makes them available for OLAP.

OLAP is used to make analysis, decision, predictions and ecc.
For this reason data are called the new oil, because they will be the fuel of the digital transaciton.

There are 1, 2 and 3 layer architectures to deal with data and requests, to build a data warehouse.

## ETL
ETL consists in four phases:
 - **extraction;**
 - **cleansing (or cleaning);**
 - **transformation;**
 - **loading;**

who knows the fuck does this means?
He said he will not go into detail of ETL. Sbrudulain.

What's **inside** DW ?

![[Pasted image 20250306121418.png]]

The multidimension is given for example when I want to know how many items have been sold by a certain store in a certain date. I have 3 dimension: store, item, date.
In the dimension that we define, we can define a hierarchy like days > weeks > months ecc ...
This hierarchy could be usefull when a lot of datas comes across our hands and we have to handles them.
We must remember that this cube is just a relation, and could be represented as a table.
We can turn also hierarchies in relations and in tables.

### Hierarchy
We can instanciate hierarchies between the data objects that we analyze.
Hierarchies are tree-structured, and represents the data in a hierarchical way.

#DeductionvsInduction
> Deductions happens from logical rules that I already know, 
#### Data mining
Data mining is based on looking the data and search for patterns into the data that you stored.

### OLAP session
A task that execute a particular navigation path while exploring the data
Olap is the most popular way to eploit information from a data warehouse.

#### Operators:

- **Rollup**: consent to navigate up the files
example: Roll up of time, if we are given months, will go up and give us a representation with quarters.

ex:
| Month | Money |
|--------------|--------------|
January |        10 |
February|        5  |
March   |         8  |
April     |          6 |
becames
Q1       |            29|


**Drill down**
Opposite,going back in a more detailed view of my data.
Obviously if we have our original view, we cannot do drill down since we already have the most detailed view possible for our data.

**• Slicing:** reduces the number of cube dimensions after
setting one of the dimension to a specific value
**• Dicing:** reduces the set of data being analyzed by a
selection criterion

**Pivoting**
Raws becomes columns.

Drill across
We can merge 2 queues of the same type of ìdata.
The data must be of the same type in order to be drilled across.

This operators are just an abstractions, every language implements the operators in a different way.

ROLAP 
Implementing multidimensional data model realtional.
MOLAP (not relational multidimensional data model).

First thing to remember when modeling data with ROLAP:
- Take the multidimensional cube and turn it into a table (remember the relational model);


So we can have a schema representation ( the tree ).
In the relational form, the tree is represented with a table.
for example:
h:
C -> B -> A
becomes:
h:

| A   | B   | C   |
| --- | --- | --- |
and we have the functional dependencies:
A -> B, A -> C, B -> C where the arrow stends for "functionally determines".
The we can state that A is a key.
For example if we have some data: (a1, a2, ecc...)

| A   | B   | C   |
| --- | --- | --- |
| a1  | b1  | c1  |
| a2  | b1  | c1  |
| a3  | b2  | c1  |
| a4  | b2  | c1  |
| a5  | b3  | c2  |
This is the resulting hierarchical table.
To represent the data in terms of their position in the multidimensional cube, we need to create a table and insert the dimensional coordinates as the entries in the table with a foreign key pointing to the hierarchical one.
We must also add a foreign key to fully represent the hierarchy.

Under the assumption that the table is static (cannot be  updated or modified), the data representetion obtained in the data warehousing technique that we have seen now.
Remember that a data warehouse is **NOT DYNAMIC**.
Facts are never deleted, but may be only added.
So in the contest of data warehousing, this kind of tables that join all the data togheter (also informations that in principle should be separated) are acceptable. They are called denormalized.

We need a design metodology to design a data warehouse.
### Design methodology

Data layer:
 - conceptual design;
 - er schema;
 - logical design;
 - relational schema;

**DATA WAREHOUSE:**
Here we have the idea of dimension, so we must implements another topology.
- Conceptual design phase;
- Dimensional fact modeling (DFM) and we obtain DFM schema;
- Logical design (ROLAP);
- Relational schema.


# DFM Schema

![[Pasted image 20250313120202.png|500]]

We have:
- **Fact:** 
- **Measures:**
- **Dimensions:**

Hierarchies are trees or graph structures associated with dimensions.
We can represent hierarchies in this way:

![[Pasted image 20250313120720.png|700|center]]


There can also exists descriptive attributes that are not usefull for the realization of the code, but is usefull for the understanding of the schema.

We can also have an attribute who's value depends on attributes of different dimensions.

DFM also has optional attributes. Also dimension can be optional, and in this case optional dimension cannot be part of the key.

Since attributes can be cross dimensional, we do not have a tree anymore, but a DAG.

Objects can also share hierarchies.

We can also have multiple arcs for example a book can have more then one author, so we will have more then one arch between 2 nodes.

Addivity
we can restrict the possisbility to aggragate with every function using a dotted line and explaining

# Star schema
We can also provide a schema in form of star, like this one:
![[Pasted image 20250317133103.png|600|center]]

We will have a:
- **Fact table:** contains tuples at the choosen level of aggregations;
- **Dimension tables:** are completely denormalized;

**Denormalization** multiply the space for the table and increases the space that you have to use for the tables, but you will have to do few joins.
To get the multidimensional view of the data can be given by denormalized datas by just joining every dimesinon.
EX:
Select *
from SALES s join STORE st on s.keys = st.keys
join PRODUCT p on p.keyp = skeyp
join DATE d on s.keyd = d.keyd

### Snowflake schema
![[Pasted image 20250317141039.png]]

In this case, datas are not completely denormalized.

### Logical design
Main steps in DW logical design are:
1. Choosing the logical schema to adopt (e.g., star/snowflake schema);
2. Translating fact schemata into logical schemata;
3. Materializing views;
4. Realizing some forms of optimization;

Exercise:
![[Pasted image 20250317143745.png]]

1) DFM schema:
Exercise on paper.

<<<<<<< HEAD

---

# NO SQL

Relational database schema are static, but data changes continuously and increase or decrease. For this reason, since relational database are not **flexible**, No SQL are concretely used in real life.

## Graph database
We can have database in form of graph.
This kind of graphs are used for example in social network database.

Litterally a graph, with nodes, edges, and properties to represent and store data.
Can be used for example to represent:
- Social networks;
- Maps with streets and places on the globe;
- ecc...

The advantage is for example to create 2 different kind of relation for the same object with 2 different entities and with different characteristics. With relational database, I should've used different tables, here I just need to create 2 different edges wich is completely natural in a graph approach.

Practical example:
![[Pasted image 20250324134608.png]]

Query needed to search for Alice friends in a relational database.
In a graph, I just need to follow a path, which is much easier.

### Implementations
We can implement a graph in Adjacency list.
Each node has a list of neighbors.

### Queries
How to query a graph db ?
We will have labelled edges, and we will use regex.
Regex example:
L = (a | (bc) +)?
L = {$\epsilon$,a,bc,bcbc,bcbcbc,...}
Why are we talking about regex?
Because nodes are labelled with letter in alphabtes, so **each path in a graph db is defined by a regex**.

## Neo4j
Graph database representation.

1. Node: (node);
2. Edge: -[edge]->
3. Labels: (x:Director) where:
	- x is the name;
	- Director is the label; 
4. Properties: properties of a node are expressed as: $\{p_1,p_2,...,p_n\}$;

After the nodes creation that happens as described before, if you want to modify the schema afterwards you will have to match every node with and identifier.
EX:
match (m ,{name: 'Lenzerini'}),(dm {name: 'Data Management'})
CREATE (m) -[Teaches]-> (dm)

Queries:
...

---
# RDF
New NoSQL database.
Resource Description Framework is considered a  data model for the web.
Basically is a graph database.
Every entry in this database is a triple, subject, predicate, object.
Every edge in a graph database, at the end, is actually a triple.
We also have BLANK NODES as models of triples.

We have index, and this index must be updated at every operation, but queries are much faster.
RDF is just a particular type of relational database.
Naive approach:
Store all the triples in  table (vertical representation)

Horizontal
We can have an horizontal representation in which we group for example all the subject of the tuple (TRIPLES) and create a single entry in the table.
Pros:

Cons:
- There will be a lot of nulls;
- I might have different tuples with same subject and same object

Query lenguage for RDS:
**SPARQL**

---
## Knowledge graphs

A lot of request for machine learning engeneers.
Difference between data base and knowledge base.

#### Knowledge base
A database satisfies the costraints if all the conditions imposed in the foreign key constraints are respected.

A knowledge graph can deduce if something is missing in order to satisfy a constraint, and add it using also null values.

We define an RDFS (RDF schema) in the following terms:

#### Alphabet
A set of symbols. Some of theme are already defined in RDF.
We also have some class in RDF, for example:
rdf:Resource is a class of everything;
Class: is a class;
type: used to assert that a resource is an insatance of a class;
subClassOf: triv
Property: is a class whos istances are the properties of the class;
example of properties:
- domain is used to assert the class of the first component;
- range =================  of the second component;
- subPropertyOf;
 So we basically have classes and properites.

#### Syntax
X rdf:type Y
The same symbol could be used as a property and as an individual;

Schema informations and data informations are mixed.
For example:
	  /------------------      **PERSON**      --------------------\
	 /                                                                                           \
	/                                                                                              \
 **Student**      <--rdf:domain--  has supervisor    --rdf:range->  **Researcher** 
   ^                                                                                                      ^
   |                                                                                                       |
**Frank**                           ----   has supervisor   --->                     **Jean**

This cannot be represented as a database because Frank is a stuend but there is no person wich name is frank, even tho each student is a person.

Closed world assumption: everything that is not in the database is false.
A database follows the closed world assumption.
In Graph knowledge there is NO closed world assumption.
We can also answare I don't know in the knowledge graph, so this modifies the closed world assumptions. In this case, the answare NO should include negative informations in the database.

#### Semantics
Based on the notion of **interpretation**.
Interpretations are composed of:
- Domain of the interpretations;
- The interpretation itself;
Extension is an interpretation of a generic graph.
There could be multiple interpretations of the same graph.
In the interpretation of a predicate, we have to formally define it.
EX:
rdf:domain = cock
I is "good" if elem are coherent with the knowledge

---
3 Most famous no SQL database:

key value;
graph database;
document based;

### Key value
We can access values based on their keys.
It is like a dictionary.
Values can represent different attributes, and are blocks of informations. This values doesn't have to keep a fixed schema. Values are single objects and can be modeled in anyway.
We cannot access only a part of the value, we must take it all.
### Document-based
Whenever I talk about documents, I refer to aggregates.
A document is a structure of key-value pairs, where the values are as complex as you want.
Documents are usually represented trought the JSON format.
We can define transformations for JSON objects.

#### Differences:
With key value you can't access part of the value, with document based you can access single parts of the value since is a JSON file, accessing only a part of the whole value content.

# MongoDB
Document-based data base.
Open source, No SQL, DataBase Management System, relies on BSON (Binary JSON).
Same structure of JSON, with specific data types.

To create a collection we will have a validator, a list of required properties.
We can insert consrtraints on the data that are inserted in our schema.

dbcreatecollection{

}


---

	  /------------------      **PERSON**      --------------------\
	 /                                                                                           \
	/                                                                                              \
 **Student**      <--rdf:domain--  has supervisor    --rdf:range->  **Researcher** 
   ^                                                                                                      ^
   |                                                                                                       |
**Frank**                           ----   has supervisor   --->                     **Jean**

Can this graph be regarder as a database ?

We know what is a graph database, but now we ask: What is a Knowledge Graph ?
Remember that the closed world assumption is not valid in a graph knowledge base.
Delta I is the set of all URIs.
The interpretation is not necessarly coherent with the graph, but it must be coherent with the alphabet. That's not the same if we talk about **models**.
We call **Models** of the graph a good interpretation, an interpretation that respects all the triples of the graph.
Blank node in a graph database allows us to choose between different interpretations, unlike the Null value in the regular database.

---
# Buffer

The **database buffer** (also called simply buffer or buffer pool) is a
non-persistent main memory space used to cache database pages.

The **buffer manager** is responsible of the transfer of the pages from
the secondary storage to the buffer pool, and back from the buffer
pool to the secondary storage

![[Pasted image 20250424101628.png]]
This is the buffer architecture.

### Operations

The buffer manager uses the following **primitive operations**:
- Fix: load a page;
- Unfix: releases a page;
- Use: registers the use of a page in the buffer;
- Force: synchronous transfer to secondary storage;
- Flush: asynchronous transfer to secondary storage;

#### Fix
The fix operation **load** a specific secondary storage page into the buffer.
The buffer manager manages this operations using 2 counters:
- **pin-count(F)**: how many transactions are using the page contained in F (initially, set to 0).
- **dirty(F):** a bit whose value indicates whether the content of the frame F has been modified (true) or not (false) from the last load (initially, set to false).
So the buffer manager will have to load a page in a Frame.
If the page is already in a frame, he will just increase his pin-counter.
If the page is not in a frame, he will have to find a free frame, or replace a page in a frame with the new one by means of a  **replacing policy**.

#### Unfix:
Transaction T certifies that it does not need the content of a
specific frame anymore
The pin-counter of that frame is decremented

#### Use:
The transaction modifies the content of a frame
The dirty bit of that frame is set to true


#### Force: 
Synchronous (i.e., the transactions waits for the successful
completion of the operation) transfer to secondary storage of the
page contained in a frame


#### Flush: 
Asynchronous (i.e., executed when the buffer manager is
not busy) transfer to secondary storage of the pages used by a
transaction

But, what is a transaction ?
For example, in database we did:
begin transaction:
...
end transaction.
Transactions are operations defined in brackets that must be treated as **atomic operations**.

There are 4 basic properties that we want to achieve: **ACID**
1. **Atomicity**: for each transaction execution, either all or
none of its actions have their effect
2. **Consistency**: each transaction execution brings the
database to a correct state (as state where no integrity
constraint is violated)
3. **Isolation**: each transaction execution is independent of
any other concurrent transaction executions
4. **Durability**: if a transaction execution succeeds, then its
effects are registered permanently in the database

We have to be sure that even if a transaction satisfies the ACID properties, also the other concurrent executions behaves correctly.

We have to schedule transaction in a correct order.

A schedule S is serializable if there exists a serial schedule which is **equivalent** to S.
equivalent = $\forall input$ they produce the same output.

#### Scheduler

A schedule represents the sequence of actions of transactions presented to the data
manager. The **scheduler** is the part of the transaction manager that is responsible of
managing the schedule.

#### Class of schedules
We have different class of schedules:

All
schedules
							
Serializable
schedules
							
Serial
schedules

##### Reads-from
In a schedule S, we say that ri(x) READS-FROM wj(x) if wj(x) preceeds ri(x) in S, and there is no action of type wk(x) between wj(x) and ri(x).
##### Final-write
In a schedule S, we say that wi(x) is a FINAL-WRITE if wi(x) is the last write action on x in S. 

####  view-equivalence
Let S1 and S2 be two (total) schedules on the same transactions. 
Then S1 is view-equivalent to S2 if S1 and S2 have the same READS-FROM relation, and the same FINAL-WRITE set.

#### view-serializability
A (total) schedule S on {T1,…,Tn} is view-serializable if there exists a serial schedule S’ on {T1,…,Tn} that is view-equivalent to S.
Checking a schedule for view-serializability is NP Complete.
So in practice, it is not possible to use it.

#### serializability
Something

A schedule can be serializable but not view-serializable;

---
## **EXERCISES**
#exercises

• Consider the schedules:
1. w0(x) r2(x) r1(x) w2(x) w2(z)
2. w1(y) r2(x) w2(x) r1(x) w2(z)
3. w1(x) r2(x) w2(y) r1(y)
4. w0(x) r1(x) w1(x) w2(z) w1(z)
and tell which of them are view-serializable
• Consider the following schedules, verify that they are not view-
serializable, and tell which anomalies they suffer from
5. r1(x) w2(x) r1(x)
6. r1(x) r2(x) w1(x) w2(x)
7. w1(x) w2(y) w1(y) w2(x)

Exercise 1a
• Consider the schedules:
8. w0(x) r2(x) r1(x) w2(x) w2(z)
9. w1(y) r2(x) w2(x) r1(x) w2(z)
10. w1(x) r2(x) w2(y) r1(y)
11. w0(x) r1(x) w1(x) w2(z) w1(z)
and tell which of them are view-serializable

• Consider the following schedules, verify that they are not view-
serializable, and tell which anomalies they suffer from
12. r1(x) w2(x) r1(x) -- unrepeatable read
13. r1(x) r2(x) w1(x) w2(x) -- lost update
14. w1(x) w2(y) w1(y) w2(x) -- ghost update

Exercise 1b
Consider the following schedule
S = r1(x) w3(x) w3(z) w2(x) w2(y) r4(x) w4(z) w1(y)
and tell whether S is view-serializable or not, explaining the answer in
detail.

---

### Conflicts and commutations
We consider arbitrary actions.
There are actions that are confilcting.
Based on conf(S), we can define the **commutativity rule** for a sequence S as follows: if p,q are adiacent actions in S belonging to different transactions, and they are such that <p,q> is not in conf(S), then the sequence p,q can be replaced by the sequence q,p (in other words, p and q can be swapped).

#### Conflict-equivalence
A sequence is conflict equivalent to another if you can produce it by swapping non-conflicting actions between each other.

#### Conflict serializable
A sequence is conflict serializable if it is conflict equivalent with another sequence.

To better visualize this conflicting sequence, we can draw a precedence graph, and the build an alogotihm on the graph.
Every edge is a constraint on the set of the conflict-equivalent possible sequence.
So we need to search another graph with the same topological order.
If we find another graph with the same topological order, we have found a conflict-equivalent. But a graph can't have a topological order if it has cycles.
So we can say that if a graph has a cycle it is not conflict-serializable.

---
## **EXERCISE**
#exercises 

Exercise 4
Check whether the following schedule is conflict-serializable
w1(x) r2(x) w1(z) r2(z) r3(x) r4(z) w4(z) w2(x)

---
Trying to prove something:

$READFROM (S) \neq READFROM(S')$
Supposing we have a pair $(w_j(x), r_i(x))$ in the relation for S but not in the relation for S'.
Now, if S' has not the same sequence, what happens is:
- 