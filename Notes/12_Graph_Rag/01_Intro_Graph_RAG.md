# Graph RAG (Graph Retrieval-Augmented Generation) — Detailed Notes
Table of Contents
Introduction
Why Traditional RAG is Not Enough
Demerits of Traditional RAG (and Advanced RAGs Covered So Far)
Why We Need Graph RAG
What is Graph RAG?
Foundation of Traditional RAG vs Graph RAG
Graph Fundamentals
Nodes
Edges
Knowledge Graph
Knowledge Graph Example
What are Hops?
Multi-Hop Reasoning
Graph RAG Architecture
LangChain Implementation (Neo4j)
Advantages and Disadvantages
Interview Questions
Key Takeaways
# 1. Introduction

So far we have learned many advanced RAG techniques:

Traditional RAG
CRAG
Self-RAG
Agentic RAG
Parent Retriever
Multi Query Retriever
Rank Fusion
HYDE
Re-ranking

All these techniques improve retrieval quality.

However, they still have one major limitation:

They retrieve documents (or chunks), but they do not explicitly understand the relationships between entities contained in those chunks.

This limitation led to the development of Graph RAG.

# 2. Why Traditional RAG is Not Enough?

Traditional RAG is built on:

Document
      ↓
Chunking
      ↓
Embedding
      ↓
Vector Database
      ↓
Similarity Search

The retriever finds chunks that are semantically similar to the query.

Example

Suppose our documents contain:

Chunk 1
Elon Musk is the CEO of Tesla.
Chunk 2
Tesla acquired Maxwell Technologies.
Chunk 3
Maxwell Technologies develops battery technology.

User asks:

How is Elon Musk related to battery technology?

Traditional RAG retrieves chunks independently.

It may retrieve:

Chunk 1
Chunk 3

but it does not explicitly know that:

Elon Musk
    ↓
CEO of Tesla
    ↓
Tesla acquired Maxwell
    ↓
Maxwell develops batteries

This relationship must be inferred by the LLM.

Problem

Traditional RAG stores:

Semantic Similarity

but not:

Entity Relationships
# 3. Demerits of Traditional RAG (and Advanced RAGs Covered So Far)

Even after all advanced RAG techniques, one limitation still remains.

## 1. No Explicit Relationship Between Chunks

Chunks are stored independently.

Example:

Chunk A:
Tesla acquired Maxwell

Chunk B:
Maxwell develops batteries

Chunk C:
Battery improves EV range

Traditional RAG does not explicitly connect these chunks.

## 2. Difficult Multi-Hop Reasoning

Question:

How does Elon Musk indirectly contribute to battery innovation?

Requires multiple logical connections.

Traditional RAG depends on the LLM to discover these connections.

## 3. Relationships are Hidden Inside Text

Relationships such as:

CEO of
Works at
Located in
Owns
Acquired
Parent company

are embedded in natural language.

The retriever cannot search directly using these relationships.

## 4. Retrieval is Based Only on Semantic Similarity

Retriever searches for:

Similar Meaning

It does not search for:

Connected Entities
## 5. Long Chains of Knowledge Become Difficult

Example:

Person
 ↓
Company
 ↓
Acquisition
 ↓
Technology
 ↓
Research

Traditional RAG struggles when answers require multiple connected facts.

Summary

Traditional RAG answers:

"Which chunks are similar?"

Graph RAG answers:

"How are these entities connected?"

# 4. Why We Need Graph RAG?

Graph RAG was introduced to preserve and retrieve:

Relationships

between entities.

Instead of retrieving isolated chunks, Graph RAG retrieves:

Connected Knowledge

Example

Question:

Who founded the company that acquired DeepMind?

Traditional RAG retrieves separate chunks.

Graph RAG traverses relationships:

DeepMind
     ↑
Acquired By
     ↑
Google
     ↑
Founded By
Larry Page
Sergey Brin

The answer becomes much easier to retrieve.

# 5. What is Graph RAG?
Definition

Graph RAG is a Retrieval-Augmented Generation technique that uses a Knowledge Graph instead of relying only on vector embeddings. It retrieves information by traversing relationships between entities (nodes) connected through edges.

Simple Definition
Graph RAG =
Knowledge Graph
+
LLM
+
Retriever
Traditional RAG

Foundation:

Embeddings
Graph RAG

Foundation:

Knowledge Graph
# 6. Foundation of Traditional RAG vs Graph RAG
Traditional RAG
Document
      ↓
Chunk
      ↓
Embedding
      ↓
Vector Database

Everything depends on:

Semantic Similarity
Graph RAG
Document
      ↓
Entity Extraction
      ↓
Relationship Extraction
      ↓
Knowledge Graph

Everything depends on:

Entity Relationships
Comparison
Traditional RAG	Graph RAG
Embeddings	Knowledge Graph
Similarity Search	Graph Traversal
Chunks	Entities
Semantic Matching	Relationship Matching
Vector Database	Graph Database
# 7. Foundation of Graph RAG

Graph RAG is built using:

Nodes

and

Edges
Nodes

Nodes represent:

Entities

Examples:

Person
Company
City
Product
Technology

Example

Elon Musk
Tesla
Maxwell
Battery

Each is a node.

Edges

Edges represent:

Relationships

Examples:

CEO_OF

FOUNDED

LOCATED_IN

ACQUIRED

WORKS_FOR

Example Graph

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
Real-Life Example

Suppose we have:

Sundar Pichai is CEO of Google.

Google acquired DeepMind.

DeepMind developed AlphaGo.

Knowledge Graph:

Sundar Pichai
      │
   CEO_OF
      │
Google
      │
ACQUIRED
      │
DeepMind
      │
DEVELOPED
      │
AlphaGo

Now ask:

Who is indirectly connected with AlphaGo?

Graph traversal easily reaches:

Sundar Pichai
# 8. What is a Knowledge Graph?
Definition

A Knowledge Graph is a structured representation of information where entities are represented as nodes and the relationships between them are represented as edges.

Unlike documents, a Knowledge Graph stores:

Facts + Relationships

Example

Sentence:

Tesla acquired Maxwell Technologies.

Knowledge Graph:

Tesla
   │
ACQUIRED
   │
Maxwell Technologies

Another sentence:

Maxwell develops battery technology.

Graph becomes:

Tesla
   │
ACQUIRED
   │
Maxwell
   │
DEVELOPS
   │
Battery Technology
Knowledge Graph Architecture
                Knowledge Graph

       Tesla -------- ACQUIRED -------- Maxwell
          |                                |
       CEO_OF                         DEVELOPS
          |                                |
     Elon Musk                  Battery Technology
# 9. Why is a Knowledge Graph Powerful?

Suppose user asks:

How is Elon Musk related to battery technology?

Graph traversal follows:

Elon Musk
     ↓
CEO_OF
     ↓
Tesla
     ↓
ACQUIRED
     ↓
Maxwell
     ↓
DEVELOPS
     ↓
Battery Technology

This reasoning is difficult with plain vector search.

# 10. What are Hops?

A hop is one movement from one node to another through an edge.

Example

Elon Musk
     │
CEO_OF
     │
Tesla

Moving from:

Elon Musk → Tesla

is:

## 1 Hop

Now:

Elon Musk
      ↓
Tesla
      ↓
Maxwell

Moving from:

Elon Musk → Tesla → Maxwell

is:

## 2 Hops

Another example

Elon Musk
      ↓
Tesla
      ↓
Maxwell
      ↓
Battery Technology

Moving from:

Elon Musk

to

Battery Technology

requires:

## 3 Hops
Why are Hops Important?

Many real-world questions require multiple hops.

Example:

Who is the CEO of the company that acquired Maxwell Technologies?

Traversal:

Maxwell
      ↑
ACQUIRED
      ↑
Tesla
      ↑
CEO_OF
      ↑
Elon Musk

The graph retrieves the answer by following relationships.

# 11. Multi-Hop Reasoning

Graph RAG naturally supports:

Multi-Hop Reasoning

Example

Question:

Which technology is indirectly connected with Elon Musk?

Graph:

Elon Musk
      ↓
Tesla
      ↓
Maxwell
      ↓
Battery Technology

Instead of searching for one document, Graph RAG traverses connected nodes.

# 12. Graph RAG Architecture
                User Query
                     │
                     ▼
          Entity Identification
                     │
                     ▼
          Knowledge Graph Search
                     │
          (Graph Traversal / Hops)
                     │
                     ▼
          Connected Entities Retrieved
                     │
                     ▼
          Context Construction
                     │
                     ▼
                   LLM
                     │
                     ▼
                Final Answer
# 13. LangChain Implementation (Neo4j Example)

Note: Neo4j is one of the most widely used graph databases with LangChain.

Install
pip install langchain
pip install langchain-community
pip install langchain-openai
pip install neo4j
Connect to Neo4j
from langchain_community.graphs import Neo4jGraph

graph = Neo4jGraph(
    url="bolt://localhost:7687",
    username="neo4j",
    password="password"
)
Add Graph Data
graph.query("""
MERGE (p:Person {name:'Elon Musk'})
MERGE (c:Company {name:'Tesla'})
MERGE (p)-[:CEO_OF]->(c)
""")
Query the Graph
result = graph.query("""
MATCH (p:Person)-[:CEO_OF]->(c:Company)
RETURN p.name, c.name
""")

print(result)
Using LangChain GraphCypherQAChain
from langchain.chains import GraphCypherQAChain
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4.1-mini")

chain = GraphCypherQAChain.from_llm(
    llm=llm,
    graph=graph,
    verbose=True
)

response = chain.invoke({
    "query": "Who is the CEO of Tesla?"
})

print(response["result"])

This chain automatically:

Converts the natural language question into a Cypher query.
Executes the query on Neo4j.
Uses the retrieved graph data to generate the final answer.
14. Advantages

✅ Preserves relationships between entities.

✅ Excellent for multi-hop reasoning.

✅ Better factual consistency.

✅ Easier to answer relationship-based questions.

✅ Supports explainable retrieval through graph traversal.

# 15. Disadvantages

❌ More complex to build than vector-based RAG.

❌ Requires entity and relationship extraction.

❌ Graph databases require additional infrastructure (e.g., Neo4j).

❌ Graph construction and maintenance can be time-consuming.

# 16. Interview Questions
Q1. Why do we need Graph RAG?

Because traditional RAG retrieves semantically similar chunks but does not explicitly model relationships between entities.

Q2. What is the foundation of Traditional RAG?

Vector embeddings and similarity search.

Q3. What is the foundation of Graph RAG?

Knowledge Graphs consisting of nodes and edges.

Q4. What are nodes in Graph RAG?

Nodes represent entities such as people, companies, products, or locations.

Q5. What are edges?

Edges represent relationships between entities, such as CEO_OF, WORKS_FOR, LOCATED_IN, or ACQUIRED.

Q6. What is a hop?

A hop is one traversal from one node to another through an edge in a graph.

Q7. Why is Graph RAG better for relationship-based questions?

Because it retrieves information by following explicit relationships between entities instead of relying only on semantic similarity.

# 17. Key Takeaways
Traditional RAG is built on vector embeddings and retrieves based on semantic similarity.
Graph RAG is built on Knowledge Graphs and retrieves based on relationships.
Nodes represent entities, while edges represent relationships.
Knowledge Graphs make relationships explicit, enabling powerful multi-hop reasoning.
Hops allow Graph RAG to traverse connected entities and answer complex questions that span multiple pieces of knowledge.
Graph RAG is particularly valuable in domains such as enterprise knowledge bases, biomedical research, finance, legal systems, and recommendation engines where understanding relationships is as important as understanding individual documents.