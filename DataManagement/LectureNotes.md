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
I is "good" if elem are coherent with the knowledge.
