# A guide for Neo4j & Cypher
A basic cheat sheet for Cypher query language and a small family tree demo for graph database Neo4j.  
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
