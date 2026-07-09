# Graph RAG — How Does Graph RAG Work?
Table of Contents
Introduction
Overall Working of Graph RAG
Why Graph Database is Used
Graph Database vs Vector Database
How Data is Stored in Graph Database
Persisting Entities and Relationships
Why Embeddings are Still Stored in Graph RAG
Retrieval Process in Graph Database
Graph Databases Used in Graph RAG
Querying Graph Database using Cypher
Complete Retrieval Flow
LangChain + Neo4j Implementation
Advantages
Interview Questions
Key Takeaways
# 1. Introduction

In the previous notes, we learned that Graph RAG is built on the foundation of a Knowledge Graph, where information is stored as entities (nodes) and relationships (edges).

However, one important question remains:

How does Graph RAG actually work behind the scenes?

To answer this, we need to understand:

How data is stored in a Graph Database.
How Graph Databases retrieve information.
How Graph RAG converts a user's natural language question into a graph query.
How the retrieved graph information is converted back into context for the LLM.
# 2. Overall Working of Graph RAG

The complete Graph RAG pipeline looks like this:

                    Raw Documents
                          │
                          ▼
          Entity & Relationship Extraction
                          │
                          ▼
                 Knowledge Graph Creation
                          │
                          ▼
                 Graph Database (Neo4j)
                          │
               (Nodes + Edges + Embeddings)
                          │
                          ▼
                   User Natural Query
                          │
                          ▼
               LLM generates Cypher Query
                          │
                          ▼
                 Execute on Graph Database
                          │
                          ▼
              Graph Results (Structured Data)
                          │
                          ▼
         Convert Graph Results into Natural Language
                          │
                          ▼
                    Context for LLM
                          │
                          ▼
                    Final Generated Answer

Notice that unlike Traditional RAG, the retrieval is performed by traversing relationships in the graph instead of only performing vector similarity search.

# 3. Why is a Graph Database Used?

A Graph Database is specifically designed to store and retrieve connected data efficiently.

Unlike a relational database, which stores data in tables, or a vector database, which stores embeddings, a Graph Database stores:

Entities
Relationships
Properties

This makes it extremely efficient for answering relationship-based questions.

Example

Consider the following facts:

Elon Musk is CEO of Tesla.
Tesla acquired Maxwell.
Maxwell develops Battery Technology.

A Graph Database stores this as:

Elon Musk
      │
   CEO_OF
      │
    Tesla
      │
  ACQUIRED
      │
   Maxwell
      │
 DEVELOPS
      │
Battery Technology

When the user asks:

How is Elon Musk connected to Battery Technology?

The Graph Database simply traverses the graph.

# 4. Graph Database vs Vector Database

Graph Databases and Vector Databases are conceptually similar because both are designed for fast retrieval.

However, what they index is different.

Vector Database	Graph Database
Stores vectors	Stores nodes and relationships
Searches semantic similarity	Traverses graph relationships
Foundation: Embeddings	Foundation: Knowledge Graph
Uses ANN Indexes (HNSW, IVF)	Uses Graph Traversal + Graph Indexes
Examples: ChromaDB, FAISS, Pinecone	Neo4j, Memgraph, Amazon Neptune
Similarity

Both databases:

Store processed knowledge
Build indexes
Retrieve information efficiently
Avoid scanning the entire dataset
# 5. How Data is Stored Inside a Graph Database

Unlike vector databases, a Graph Database stores each entity as a Node.

Each node contains:

Properties
Metadata
Optional Embedding
Example Node
Node : Elon Musk

name       : "Elon Musk"

type       : "Person"

source     : "chunk_42"

embedding  : [0.20, 0.31, 0.45, ...]

Similarly,

Node : Tesla

name       : "Tesla"

type       : "Company"

source     : "chunk_43"

embedding  : [0.45, 0.87, ...]

Relationship

(:Person)-[:CEO_OF]->(:Company)

Visualization

+----------------------+
| Node : Elon Musk     |
|----------------------|
| name      : Elon Musk|
| type      : Person   |
| source    : chunk_42 |
| embedding : [...]    |
+----------------------+

             │
          CEO_OF
             │

+----------------------+
| Node : Tesla         |
|----------------------|
| name      : Tesla    |
| type      : Company  |
| source    : chunk_43 |
| embedding : [...]    |
+----------------------+
# 6. Persisting Entities and Relationships

When documents are processed, Graph RAG performs:

Step 1

Extract entities.

Example:

Elon Musk

Tesla

Maxwell
Step 2

Extract relationships.

CEO_OF

ACQUIRED

DEVELOPS
Step 3

Persist inside Graph Database.

Elon Musk
      │
   CEO_OF
      │
    Tesla
      │
 ACQUIRED
      │
 Maxwell

This persistent graph becomes the Knowledge Base.

# 7. Why Are Embeddings Also Stored?

A common interview question is:

If Graph RAG is based on relationships, why do we still store embeddings?

Answer

Embeddings help with:

Semantic search
Entity matching
Hybrid retrieval
Example

User asks:

Owner of Tesla

But the graph stores:

CEO_OF

Using embeddings, the system understands that:

Owner ≈ CEO (semantically related)

This improves retrieval.

Hybrid Graph Retrieval

Many modern Graph RAG systems combine:

Graph Traversal

+

Embedding Similarity

This is often called Hybrid Graph Retrieval.

# 8. How Retrieval Works in a Graph Database

Suppose the user asks:

How is Elon Musk connected to Battery Technology?
Step 1

Identify starting entity.

Elon Musk
Step 2

Traverse graph.

Elon Musk

↓

Tesla

↓

Maxwell

↓

Battery Technology
Step 3

Collect connected nodes.

Step 4

Return structured graph result.

Unlike Vector RAG, Graph RAG does not retrieve isolated chunks.

It retrieves:

Connected Knowledge
Retrieval Workflow
User Query
      │
      ▼
Entity Identification
      │
      ▼
Graph Traversal
      │
      ▼
Connected Nodes Retrieved
      │
      ▼
Context Construction
      │
      ▼
LLM
# 9. Popular Graph Databases

Just like we have vector databases such as:

ChromaDB
FAISS
Pinecone
Milvus

Graph RAG uses graph databases.

Popular ones are:

Graph Database	Description
Neo4j	Most popular graph database for Graph RAG
Memgraph	High-performance graph database
Amazon Neptune	AWS managed graph database
TigerGraph	Enterprise graph analytics
ArangoDB	Multi-model database supporting graphs

Among these,

Neo4j is the most commonly used with LangChain.

# 10. Interacting with a Graph Database using Cypher

Unlike SQL databases that use SQL,

Neo4j uses:

Cypher Query Language

Cypher is used to:

Find nodes
Traverse relationships
Filter graph
Return results
Example Graph
Elon Musk
      │
   CEO_OF
      │
    Tesla
Cypher Query
MATCH (p:Person)-[:CEO_OF]->(c:Company)
WHERE p.name = "Elon Musk"
RETURN c.name

Meaning:

Find Person

↓

Follow CEO_OF relationship

↓

Return Company Name

Visualization

Natural Question

↓

Who is Elon Musk CEO of?

↓

LLM

↓

Cypher Query

↓

MATCH (p:Person)-[:CEO_OF]->(c:Company)

↓

Neo4j Graph Database

↓

Tesla

↓

Context

↓

LLM

↓

Final Answer
# 11. How Cypher Results Become Context

Graph Database returns:

Person = Elon Musk

Relationship = CEO_OF

Company = Tesla

This is structured data.

Before passing it to the LLM, it is converted into natural language.

Example:

Elon Musk is the CEO of Tesla.

This becomes the retrieval context.

Complete Flow

Natural Language Query

↓

LLM

↓

Cypher Query

↓

Graph Database

↓

Structured Graph Result

↓

Natural Language Context

↓

LLM

↓

Answer
# 12. LangChain + Neo4j Example
Step 1: Connect to Neo4j
from langchain_community.graphs import Neo4jGraph

graph = Neo4jGraph(
    url="bolt://localhost:7687",
    username="neo4j",
    password="password"
)
Step 2: Add Data
graph.query("""
MERGE (p:Person {name:'Elon Musk'})
MERGE (c:Company {name:'Tesla'})
MERGE (p)-[:CEO_OF]->(c)
""")
Step 3: Query Graph
result = graph.query("""
MATCH (p:Person)-[:CEO_OF]->(c:Company)
RETURN p.name,c.name
""")

print(result)
Step 4: Natural Language Question Answering
from langchain.chains import GraphCypherQAChain
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4.1-mini")

chain = GraphCypherQAChain.from_llm(
    llm=llm,
    graph=graph,
    verbose=True
)

response = chain.invoke({
    "query":"Who is the CEO of Tesla?"
})

print(response["result"])
Internal Workflow of GraphCypherQAChain
User Query
      │
      ▼
LLM understands the question
      │
      ▼
Generates Cypher Query
      │
      ▼
Executes Cypher on Neo4j
      │
      ▼
Receives Graph Results
      │
      ▼
Converts Results to Context
      │
      ▼
LLM Generates Final Answer
# 13. Advantages
Excellent for relationship-based retrieval.
Supports multi-hop reasoning.
Stores both entities and relationships.
Can combine graph traversal with embedding search.
Highly explainable retrieval process.
# 14. Interview Questions
Q1. Why do we need a Graph Database in Graph RAG?

To efficiently store entities and their relationships, enabling relationship-based retrieval instead of only semantic similarity search.

Q2. Why are embeddings stored in Graph RAG?

Embeddings enable semantic matching and hybrid retrieval alongside graph traversal.

Q3. Which graph database is most commonly used with LangChain?

Neo4j.

Q4. Which query language is used by Neo4j?

Cypher Query Language.

Q5. How does Graph RAG retrieve information?

By identifying entities, traversing relationships (hops) in the graph, retrieving connected nodes, and converting the graph results into natural language context for the LLM.

# 15. Key Takeaways
Graph RAG stores entities as nodes and relationships as edges in a Graph Database.
Modern Graph RAG systems often store embeddings alongside graph data to support hybrid retrieval.
Neo4j is the most widely used graph database, and Cypher is its query language.
Retrieval is performed through graph traversal, not just vector similarity.
The structured graph results are converted into natural language context before being passed to the LLM, enabling accurate, explainable, and relationship-aware responses.