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