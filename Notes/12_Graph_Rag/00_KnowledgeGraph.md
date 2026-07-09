# Knowledge Graph (Required to Understand Graph RAG)
# Introduction

Before learning Graph RAG, we must understand one important concept:

Knowledge Graph

Graph RAG is nothing but RAG + Knowledge Graph.

If you understand Knowledge Graph well, Graph RAG becomes very easy.

Why do we need Knowledge Graph?

Let's first understand the problem.

Suppose we have these three documents.

Document 1
Elon Musk is the CEO of Tesla.
Document 2
Tesla acquired Maxwell Technologies.
Document 3
Maxwell Technologies develops Battery Technology.

Now suppose the user asks

How is Elon Musk related to Battery Technology?

How Traditional RAG solves it

Traditional RAG searches documents based on semantic similarity.

It retrieves

Document 1

Elon Musk is CEO of Tesla.

and

Document 3

Battery Technology

But...

it does NOT know that

Elon Musk
      ↓
Tesla
      ↓
Maxwell
      ↓
Battery Technology

are connected.

It only sees three independent chunks.

This is where Knowledge Graph comes into picture.

Instead of storing text only,

we also store

Relationships.

# What is Knowledge Graph?
Definition

A Knowledge Graph is a structured representation of knowledge where

entities are represented as Nodes
relationships are represented as Edges

Unlike traditional databases that only store data, a Knowledge Graph stores both data and the relationships between data.

Simple Definition

A Knowledge Graph is a graph that connects related entities through meaningful relationships.

# Foundation of Knowledge Graph

Knowledge Graph has only three building blocks.

Node

Edge

Properties

Let's understand them.

1. Node

Node represents an Entity.

Entity means

anything that exists.

Examples

Person

Company

Country

Car

Technology

Product

Example

Elon Musk

Tesla

Maxwell

Battery Technology

Each one is a Node.

Graph looks like

(Elon Musk)

(Tesla)

(Maxwell)

(Battery Technology)
2. Edge

Edges represent

Relationship between two Nodes.

Examples

CEO_OF

WORKS_FOR

ACQUIRED

LOCATED_IN

OWNS

DEVELOPS

Example

Elon Musk

CEO_OF

Tesla

Graph

Elon Musk
      |
   CEO_OF
      |
    Tesla

Another example

Tesla

ACQUIRED

Maxwell

Now graph becomes

Elon Musk
      |
   CEO_OF
      |
    Tesla
      |
 ACQUIRED
      |
   Maxwell
# 3. Properties

Each node stores extra information.

Example

Node

Tesla

Properties

name : Tesla

type : Company

country : USA

founded : 2003

Similarly

Node

Elon Musk

Properties

type : Person

Age : 53

Nationality : American
Complete Knowledge Graph
            CEO_OF

Elon Musk ---------- Tesla
                        |
                        |
                   ACQUIRED
                        |
                  Maxwell
                        |
                   DEVELOPS
                        |
               Battery Technology

Notice something.

We are not storing

only documents.

We are storing

relationships.

This is the biggest difference.

# Why do we use Knowledge Graph?

Knowledge Graph helps us answer questions that involve relationships.

Example

Who is connected to Battery Technology?

Instead of searching documents,

Graph simply follows connections.

Battery Technology

↓

Maxwell

↓

Tesla

↓

Elon Musk

Very easy.

How do we draw a Knowledge Graph?

The process is simple.

Step 1

Identify entities.

Sentence

Elon Musk is CEO of Tesla.

Entities

Elon Musk

Tesla
Step 2

Find relationship.

CEO_OF
Step 3

Draw graph.

Elon Musk
      |
   CEO_OF
      |
    Tesla

Another sentence

Tesla acquired Maxwell.

becomes

Tesla
     |
 ACQUIRED
     |
Maxwell

Merge both

Elon Musk
      |
   CEO_OF
      |
    Tesla
      |
 ACQUIRED
      |
   Maxwell

This is Knowledge Graph construction.

# What is a Hop?

One of the most important concepts in Graph RAG.

A Hop means

Moving from one node to another through an edge.

Example

Elon Musk

↓

Tesla

This is

1 Hop

Example

Elon Musk

↓

Tesla

↓

Maxwell

This is

2 Hop

Example

Elon Musk

↓

Tesla

↓

Maxwell

↓

Battery Technology

This is

3 Hop
Why are Hops important?

Suppose the question is

How is Elon Musk related to Battery Technology?

The graph traverses

Elon Musk

↓

Tesla

↓

Maxwell

↓

Battery Technology

This required

3 Hops

This process is called

Multi-Hop Reasoning

Graph RAG is extremely powerful because it naturally supports multi-hop reasoning.

Knowledge Graph vs Vector Database
Vector Database	Knowledge Graph
Stores embeddings	Stores entities and relationships
Searches similar meaning	Searches connected entities
Based on vectors	Based on graphs
Uses cosine similarity	Uses graph traversal

Traditional RAG

Chunk

↓

Embedding

↓

Vector Database

Graph RAG

Chunk

↓

Entity Extraction

↓

Relationship Extraction

↓

Knowledge Graph
# Graph Database

A Knowledge Graph needs a database to store nodes and relationships.

That database is called

Graph Database

Examples

Neo4j

Amazon Neptune

TigerGraph

Memgraph

ArangoDB

Most popular

Neo4j
# Why Neo4j?

Neo4j is optimized for

Nodes
Relationships
Fast Traversal

Instead of searching millions of rows,

Neo4j simply follows connections.

# How do we interact with Graph Database?

SQL Database uses

SQL

Graph Database uses

# Cypher

Cypher is the query language of Neo4j.

Example Graph
Elon Musk
      |
   CEO_OF
      |
    Tesla

Cypher Query

MATCH (p:Person)-[:CEO_OF]->(c:Company)
WHERE p.name = "Elon Musk"
RETURN c.name

Meaning

Find Person

↓

Follow CEO_OF relationship

↓

Return Company

Output

Tesla

Another example

Find all companies.

MATCH (c:Company)
RETURN c.name

Find all CEOs.

MATCH (p:Person)-[:CEO_OF]->(c:Company)
RETURN p.name,c.name

Find companies acquired by Tesla.

MATCH (t:Company)-[:ACQUIRED]->(c:Company)
WHERE t.name="Tesla"
RETURN c.name
How Query Works Inside Graph Database

Suppose user asks

Who is CEO of Tesla?

Flow

Natural Language

↓

LLM

↓

Cypher Query

↓

Neo4j

↓

Graph Traversal

↓

Retrieved Nodes

↓

Natural Language Context

↓

LLM

↓

Answer
# How Knowledge Graph is Used in Graph RAG

Now everything comes together.

Documents

↓

Entity Extraction

↓

Relationship Extraction

↓

Knowledge Graph

↓

Neo4j

↓

User Query

↓

LLM

↓

Cypher Query

↓

Neo4j

↓

Connected Nodes Retrieved

↓

Convert to Context

↓

LLM

↓

Final Answer

Unlike Traditional RAG, Graph RAG does not retrieve isolated chunks. It retrieves connected knowledge by following relationships in the graph, enabling accurate multi-hop reasoning.

Simple LangChain Example
Connect to Neo4j
from langchain_community.graphs import Neo4jGraph

graph = Neo4jGraph(
    url="bolt://localhost:7687",
    username="neo4j",
    password="password"
)
Add Nodes and Relationship
graph.query("""
MERGE (p:Person {name:'Elon Musk'})
MERGE (c:Company {name:'Tesla'})
MERGE (p)-[:CEO_OF]->(c)
""")
Ask Questions Using LangChain
from langchain.chains import GraphCypherQAChain
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4.1-mini")

chain = GraphCypherQAChain.from_llm(
    llm=llm,
    graph=graph,
    verbose=True
)

response = chain.invoke(
    {"query": "Who is the CEO of Tesla?"}
)

print(response["result"])
# Key Takeaways
A Knowledge Graph stores entities (nodes) and relationships (edges) instead of just text.
Traditional RAG retrieves semantically similar chunks, while Graph RAG retrieves connected knowledge by traversing relationships.
A hop is one movement from one node to another through an edge, and multiple hops enable multi-hop reasoning.
Neo4j is the most widely used graph database for Graph RAG, and Cypher is its query language.
In Graph RAG, documents are transformed into a knowledge graph, queried using Cypher, and the retrieved graph information is converted into natural language context before being passed to the LLM.

These concepts are sufficient to understand how Graph RAG works without diving into the full theory of knowledge graphs.