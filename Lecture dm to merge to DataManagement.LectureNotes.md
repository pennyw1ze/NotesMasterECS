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