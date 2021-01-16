# A guide for Neo4j & Cypher
A basic cheat sheet for Cypher query language and a small family tree demo for graph database Neo4j.  

## Installation

### Local usage
For local setup, please refer [Neo4j's official download page](https://neo4j.com/download/).  
Neo4j Server or Neo4j  Desktop can be downloaded. Neo4j Desktop offers a user interface to manage operations. User can also create multiple databases. But Neo4j Server does not offer a user interface. It can be controlled via terminal and it does not have multiple database option in community edition.

### Cloud usage
For cloud setup, [Sandbox Neo4j](https://sandbox.neo4j.com/) can be used. It offers cloud solution for Neo4j online for 3 days long if you just register.

## Cypher Manual
Reference for descriptions and examples can be found at [The Neo4j Cypher Manual v4.2](https://neo4j.com/docs/cypher-manual/current/).

### Clauses
This section contains information on all the clauses in the Cypher query language.

#### Reading clauses
These comprise clauses that read data from the database.

Clause | Description
------ | -----------
MATCH          | Specify the patterns to search for in the database.
OPTIONAL MATCH | Specify the patterns to search for in the database while using *nulls* for missing parts of the pattern.

1.  Get all nodes
```
MATCH (n)
RETURN n
```
> Returns all the nodes in the database.  

2. Get all nodes with a label
```
MATCH (movie:Movie)
RETURN movie.title
```
> Returns all the movies in the database.  

3. Related nodes
```
MATCH (director { name: 'Oliver Stone' })--(movie)
RETURN movie.title
```
> Returns all the movies directed by 'Oliver Stone'.  

4. Outgoing relationships  
```
MATCH (:Person { name: 'Oliver Stone' })-->(movie)
RETURN movie.title
```
> Returns any nodes connected with the Person 'Oliver' by an outgoing relationship.  

5. Match on relationship type  
```
MATCH (wallstreet:Movie { title: 'Wall Street' })<-[:ACTED_IN]-(actor)
RETURN actor.name
```
> Returns all actors that ACTED_IN 'Wall Street'.  

#### Projecting clauses
These comprise clauses that define which expressions to return in the result set. The returned expressions may all be aliased using *AS*.

Clause | Description
------ | -----------
RETURN …​ [AS] | Defines what to include in the query result set.
WITH …​ [AS]   | Allows query parts to be chained together, piping the results from one to be used as starting points or criteria in the next.
UNWIND …​ [AS] | Expands a list into a sequence of rows.

1. Return nodes  
```
MATCH (n { name: 'B' })
RETURN n
```

2. Return relationship  
```
MATCH (n { name: 'A' })-[r:KNOWS]->(c)
RETURN r
```

3. Return propery
```
MATCH (n { name: 'A' })
RETURN n.name
```

#### Reading sub-clauses
These comprise sub-clauses that must operate as part of reading clauses.

Sub-clause | Description
---------- | -----------
WHERE                                  | Adds constraints to the patterns in a *MATCH* or *OPTIONAL MATCH* clause or filters the results of a *WITH* clause.
WHERE EXISTS {…​}                       | An existential sub-query used to filter the results of a *MATCH*, *OPTIONAL MATCH* or *WITH* clause.
ORDER BY [ASC[ENDING] or DESC[ENDING]] | A sub-clause following *RETURN* or *WITH*, specifying that the output should be sorted in either ascending (the default) or descending order.
SKIP                                   | Defines from which row to start including the rows in the output.
LIMIT                                  | Constrains the number of rows in the output.

1. Filter on node label
```
MATCH (n)
WHERE n:Swedish
RETURN n.name, n.age
```
> The name and age for the node which has 'Swedish' label will be returned.  

2. Filter on node property
```
MATCH (n:Person)
WHERE n.age < 30
RETURN n.name, n.age
```
> The name and age values for the node whose age is less than 30 are returned.  

3. Filter on relationship property
```
MATCH (n:Person)-[k:KNOWS]->(f)
WHERE k.since < 2000
RETURN f.name, f.age, f.email
```
> The name, age and email values for the node are returned because node has known other node since before 2000.

#### Reading hints
These comprise clauses used to specify planner hints when tuning a query. More details regarding the usage of these — and query tuning in general — can be found in [Planner hints and the USING keyword](https://neo4j.com/docs/cypher-manual/current/query-tuning/using/#query-using).

Hints | Description
----- | -----------
USING INDEX      | Index hints are used to specify which index, if any, the planner should use as a starting point.
USING INDEX SEEK | Index seek hint instructs the planner to use an index seek for this clause.
USING SCAN       | Scan hints are used to force the planner to do a label scan (followed by a filtering operation) instead of using an index.
USING JOIN       | Join hints are used to enforce a join operation at specified points.

#### Writing clauses
These comprise clauses that write the data to the database.

Clause | Description
------ | -----------
CREATE        | Create nodes and relationships.
DELETE        | Delete nodes, relationships or paths. Any node to be deleted must also have all associated relationships explicitly deleted.
DETACH DELETE | Delete a node or set of nodes. All associated relationships will automatically be deleted.
SET           | Update labels on nodes and properties on nodes and relationships.
REMOVE        | Remove properties and labels from nodes and relationships.
FOREACH       | Update data within a list, whether components of a path, or the result of aggregation.

1. Create node and add labels and properties
```
CREATE (n:Person { name: 'Andy', title: 'Developer' })
```

2. Create relationship between two nodes
```
MATCH (a:Person),(b:Person)
WHERE a.name = 'A' AND b.name = 'B'
CREATE (a)-[r:RELTYPE]->(b)
RETURN type(r)
```

3. Create a full path
```
CREATE p =(andy { name:'Andy' })-[:WORKS_AT]->(neo)<-[:WORKS_AT]-(michael { name: 'Michael' })
RETURN p
```
> This query creates three nodes and two relationships in one go, assigns it to a path variable, and returns it.  

4. Delete a single node
```
MATCH (n:Person { name: 'UNKNOWN' })
DELETE n
```

5. Delete all nodes and relationship
```
MATCH (n)
DETACH DELETE n
```

6. Delete a node with all its relationships
```
MATCH (n { name: 'Andy' })
DETACH DELETE n
```

7. Delete relationships only
```
MATCH (n { name: 'Andy' })-[r:KNOWS]->()
DELETE r
```

8. Set or update a property
```
MATCH (n { name: 'Andy' })
SET n.surname = 'Taylor'
RETURN n.name, n.surname
```


#### Reading/Writing clauses
These comprise clauses that both read data from and write data to the database.

Clause | Description
------ | -----------
MERGE   | Ensures that a pattern exists in the graph. Either the pattern already exists, or it needs to be created.
--- ON CREATE | Used in conjunction with *MERGE*, this write sub-clause specifies the actions to take if the pattern needs to be created.
--- ON MATCH | Used in conjunction with *MERGE*, this write sub-clause specifies the actions to take if the pattern already exists.
CALL […​YIELD] |Invokes a procedure deployed in the database and return any results.

#### Set operations

Clause | Description
------ | -----------
UNION     | Combines the result of multiple queries into a single result set. Duplicates are removed.
UNION ALL | Combines the result of multiple queries into a single result set. Duplicates are retained.

#### Subquery clauses
Clause | Description
------ | -----------
CALL { …​ } | Evaluates a subquery, typically used for post-union processing or aggregations.


#### Multiple graphs
Clause | Description
------ | -----------
USE | Determines which graph a query, or query part, is executed against.

#### Importing Data
Clause | Description
------ | -----------
LOAD CSV                  | Use when importing data from CSV files.
--- USING PERIODIC COMMIT | This query hint may be used to prevent an out-of-memory error from occurring when importing large amounts of data using *LOAD CSV*.

#### Administration clauses
These comprise clauses used to manage databases, schema and security; further details can found in [Administration](https://neo4j.com/docs/cypher-manual/current/administration/#administration).

Clause | Description
------ | -----------
CREATE or DROP or START or STOP DATABASE | Create, drop, start or stop a database.
CREATE or DROP INDEX                     | Create or drop an index on all nodes with a particular label and property.
CREATE or DROP CONSTRAINT                | Create or drop a constraint pertaining to either a node label or relationship type, and a property.
Users roles privileges                   | Manage users, roles and privileges for database, graph and sub-graph access control.




## Family Tree Demo
This family tree shown below is completely imaginary.  
![Imaginary family tree](https://github.com/akngmskvk/guide-for-cypher-and-neo4j/blob/main/images/img1-imaginary-family-tree.png)  

##### Create an index or a constraint with unique name:  

```
CREATE INDEX ON :Person(FullName)
```
or
```
CREATE CONSTRAINT ON (p:Person) ASSERT p.FullName IS UNIQUE
```

##### Create family nodes and relationships between nodes:
```
CREATE (p1:Person{FullName: "Ali Demirel", Gender: "Male"})
CREATE (p2:Person{FullName: "Feriha Demirel", Gender: "Female"})
CREATE (p3:Person{FullName: "Ahmet Günce", Gender: "Male"})
CREATE (p4:Person{FullName: "Fatma Günce", Gender: "Female"})
CREATE (p5:Person{FullName: "Mehmet Demirel", Gender: "Male"})
CREATE (p6:Person{FullName: "Ayşe Demirel", Gender: "Female"})
CREATE (p7:Person{FullName: "Nilüfer Kılıç", Gender: "Female"})
CREATE (p8:Person{FullName: "Mustafa Kılıç", Gender: "Male"})
CREATE (p9:Person{FullName: "Osman Demirel", Gender: "Male"})
CREATE (p10:Person{FullName: "Didem Demirel", Gender: "Female"})
CREATE (p11:Person{FullName: "Fatoş Kılıç", Gender: "Female"})

CREATE (p5)-[:HAS_FATHER]->(p1)
CREATE (p5)-[:HAS_MOTHER]->(p2)
CREATE (p6)-[:HAS_FATHER]->(p3)
CREATE (p6)-[:HAS_MOTHER]->(p4)
CREATE (p7)-[:HAS_FATHER]->(p3)
CREATE (p7)-[:HAS_MOTHER]->(p4)
CREATE (p9)-[:HAS_FATHER]->(p5)
CREATE (p9)-[:HAS_MOTHER]->(p6)
CREATE (p10)-[:HAS_FATHER]->(p5)
CREATE (p10)-[:HAS_MOTHER]->(p6)
CREATE (p11)-[:HAS_FATHER]->(p8)
CREATE (p11)-[:HAS_MOTHER]->(p7)
```

##### Find Osman Demirel:
```
MATCH (p:Person{FullName: "Osman Demirel"})
RETURN p
```

##### Find Osman Demirel's mother:
```
MATCH (p:Person{FullName: "Osman Demirel"})-[:HAS_MOTHER]->(p2)
RETURN p2
```

##### Find Osman Demirel's grandmother:
```
MATCH (p:Person{FullName: "Osman Demirel"})-[:HAS_MOTHER]->(p2)-[:HAS_MOTHER]->(p3)
RETURN p3
```

##### Find the relation between Osman Demirel and Didem Demirel:
```
MATCH (p1:Person{FullName: "Osman Demirel"})
MATCH (p2:Person{FullName: "Didem Demirel"})
MATCH relation=(p1)-[*]-(p2)
RETURN relation
LIMIT 1
```

SKIP command can be used to see another path and it can be increased by the number of paths:
```
MATCH (p1:Person{FullName: "Osman Demirel"})
MATCH (p2:Person{FullName: "Didem Demirel"})
MATCH relation=(p1)-[*]-(p2)
RETURN relation
SKIP 1
LIMIT 1
```

**shortestPath()** function can be used the get the shortest patch directly:
```
MATCH (p1:Person{FullName: "Osman Demirel"})
MATCH (p2:Person{FullName: "Didem Demirel"})
MATCH relation=shortestPath((p1)-[*]-(p2))
RETURN relation
LIMIT 1
```

##### Find Osman Demirel's sibling with WHERE clause:
```
MATCH (p1:Person{FullName: "Osman Demirel"})
MATCH (p2:Person)
WHERE EXISTS((p1)-[:HAS_MOTHER|HAS_FATHER]->()<-[:HAS_MOTHER|HAS_FATHER]-(p2))
RETURN p2
```

##### Create sibling relationshio between him and his sibling:
```
MATCH (p1:Person{FullName: "Osman Demirel"})
MATCH (p2:Person)
WHERE EXISTS((p1)-[:HAS_MOTHER|HAS_FATHER]->()<-[:HAS_MOTHER|HAS_FATHER]-(p2))
CREATE (p1)-[:HAS_SIBLING]->(p2)
```

##### Update a node:
```
MATCH (p1:Person{FullName: "Mustafa Kılıç"})
SET p1.FullName = "Mustafa Can Kılıç"
```

##### Delete a relationship:
```
MATCH (p1:Person{FullName: "Fatoş Kılıç"})-[relation:HAS_FATHER]->(p2:Person{FullName: "Mustafa Can Kılıç"})
DELETE relation
```

##### Delete a node:
```
MATCH (p1:Person{FullName: "Mustafa Can Kılıç"})
DELETE p1
```
