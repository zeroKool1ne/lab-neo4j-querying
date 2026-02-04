![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# Lab | Neo4j: Querying the Movies Dataset

### **Setup**

1.  **Load the dataset** (if not preloaded):

    -   Ensure you have `Movie`, `Person`, `Genre`, and relationships like `ACTED_IN`, `DIRECTED`, etc.
    -   Use `:play movies` in Neo4j Browser to load the sample dataset if needed.

## Exercises

### 1. Basic Node Retrieval

**Task**: List all movies released in the year **1999**. <br>
**Goal**: Practice filtering node properties.

MATCH (m:Movie)
WHERE m.released = 1999
RETURN m

### 2. Find a Person by Name

**Task**: Retrieve details for the person named **Keanu Reeves**. <br>
**Goal**: Query nodes by exact property match.

MATCH (p:Person {name: 'Keanu Reeves'})
RETURN p

### 3. Find Actors in a Movie

**Task**: List all actors who acted in **The Matrix**. <br>
**Goal**: Traverse relationships from a movie to actors.

MATCH (a:Person)-[:ACTED_IN]->(m:Movie {title: 'The Matrix'})
RETURN a.name

### 4. Directors of a Movie

**Task**: Find the director(s) of **The Matrix Revolutions**. <br>
**Goal**: Filter relationships by type (`DIRECTED`).

MATCH (d:Person)-[:DIRECTED]->(m:Movie {title: 'The Matrix Revolutions'})
RETURN d.name

### 5. Movies by Genre and Year

**Task**: Find all **Action** movies released after **2000**. <br>
**Goal**: Combine relationship traversal and property filtering.

MATCH (m:Movie)-[:IN_GENRE]->(g:Genre {name: 'Action'})
WHERE m.released > 2000
RETURN m.title, m.released

### 6. Co-Actors of an Actor

**Task**: Find all actors who worked with **Tom Hanks** in any movie. <br>
**Goal**: Use variable-length patterns and deduplication (`DISTINCT`).

MATCH (tom:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(coactor:Person)
WHERE tom <> coactor
RETURN DISTINCT coactor.name

### 7. Movies with Multiple Relationships

**Task**: Find all people who either acted in or directed **Cloud Atlas**. <br>
**Goal**: Filter relationship types dynamically.

MATCH (p:Person)-[r:ACTED_IN|DIRECTED]->(m:Movie {title: 'Cloud Atlas'})
RETURN p.name, type(r)

### 8. Shortest Path Between Actors

**Task**: Find the shortest path between **Tom Hanks** and **Meg Ryan** through co-acting. <br>
**Goal**: Use `shortestPath` and variable-length relationships.

MATCH path = shortestPath(
  (tom:Person {name: 'Tom Hanks'})-[:ACTED_IN*]-(meg:Person {name: 'Meg Ryan'})
)
RETURN path

### 9. Aggregation with HAVING

**Task**: List actors who have acted in **at least 3 movies**. <br>
**Goal**: Use `WITH` and `HAVING`-like filtering.

MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, count(m) AS movieCount
WHERE movieCount >= 3
RETURN a.name, movieCount
ORDER BY movieCount DESC

### 10. Complex Graph Pattern (Advanced)

**Task**: Find all directors who also acted in their own movies. <br>
**Goal**: Combine multiple relationships on the same node.

MATCH (p:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(p)
RETURN p.name, m.title

<!-- ### Bonus Challenge

Create a **recommendation query** that suggests movies based on shared genres and frequent collaborators of a user’s favorite actor (e.g., **Keanu Reeves**). -->

**Tips:**

-   Use `PROFILE` to analyze query performance.
-   Visualize results in Neo4j Browser for graph patterns.
-   Experiment with `OPTIONAL MATCH` for partial matches.
