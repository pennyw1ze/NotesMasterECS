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