# Graph RAG - Ingestion Pipeline and Retrieval Pipeline

# Table of Contents

1. Introduction
2. Traditional RAG vs Graph RAG
3. Graph RAG Ingestion Pipeline
4. Step 1 - Knowledge Source
5. Step 2 - Document Loading
6. Step 3 - Text Splitting
7. Step 4 - Entity & Relationship Extraction
8. Earlier Approach (NER) vs Modern Approach (LLM)
9. LLM Prompting
10. Cypher Generation
11. Embedding Generation
12. Neo4j Storage
13. Complete Ingestion Architecture
14. Graph RAG Retrieval Pipeline
15. Example Walkthrough
16. Schema Awareness
17. Entry Node Selection
18. Graph Traversal (Breadth First Search)
19. Complete Retrieval Architecture
20. LangChain Implementation
21. Limitations of Graph RAG
22. When to use Graph RAG vs Regular RAG
23. Key Takeaways

---

# 1. Introduction

In Traditional RAG, the ingestion pipeline stores **chunks as embeddings** inside a Vector Database.

Graph RAG extends this idea by storing:

- Entities (Nodes)
- Relationships (Edges)
- Embeddings of Nodes

inside a Graph Database such as **Neo4j**.

The purpose is not only to understand the meaning of the document but also to preserve the relationships between different entities.

---

# Traditional RAG Ingestion

```
Raw Documents
      │
      ▼
Document Loader
      │
      ▼
Text Splitter
      │
      ▼
Embedding Model
      │
      ▼
Vector Database
```

Everything is stored as vectors.

---

# Graph RAG Ingestion

```
Raw Documents
      │
      ▼
Document Loader
      │
      ▼
Text Splitter
      │
      ▼
LLM
(Entity & Relationship Extraction)
      │
      ▼
Cypher Generation
      │
      ▼
Embedding Model
      │
      ▼
Neo4j Graph Database

Stores:

Nodes
Edges
Node Embeddings
```

Unlike Traditional RAG, Graph RAG stores the relationships explicitly.

---

# 2. Graph RAG Ingestion Pipeline

The complete ingestion pipeline consists of the following steps:

```
Knowledge Source
        │
        ▼
Document Loader
        │
        ▼
Text Splitter
        │
        ▼
LLM
(Entity Extraction)

(Relationship Extraction)
        │
        ▼
Cypher Query Generation
        │
        ▼
Embedding Model
        │
        ▼
Neo4j Graph Database
```

---

# Step 1 : Knowledge Source

Everything starts with a knowledge source.

Examples

- PDF
- Word document
- HTML page
- Website
- CSV
- JSON
- SQL Database
- API

Example

```
Tesla annual report.pdf
```

---

# Step 2 : Document Loader

The Document Loader loads the raw document into memory.

Example

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("tesla_report.pdf")

documents = loader.load()
```

Output

```
Document Objects
```

---

# Step 3 : Text Splitter

Large documents cannot directly be processed by the LLM.

Therefore,

we split them into meaningful chunks.

```
Annual Report

↓

Chunk 1

↓

Chunk 2

↓

Chunk 3
```

LangChain Example

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=100
)

chunks = splitter.split_documents(documents)
```

Now each chunk contains one meaningful portion of the document.

---

# Step 4 : Entity and Relationship Extraction

Now comes the most important step.

Question:

How do we convert chunks into a Knowledge Graph?

Earlier,

we used

# Named Entity Recognition (NER)

NER identifies entities like

```
Person

Company

Location

Organization
```

Example

Sentence

```
Elon Musk is CEO of Tesla.
```

NER Output

```
Entity

Elon Musk

Type : Person

Entity

Tesla

Type : Company
```

But...

NER cannot understand complex relationships very well.

---

# Modern Approach : LLM

Today we use an LLM.

Instead of manually defining rules,

we provide a prompt to the LLM.

The LLM identifies

- Nodes
- Relationships
- Properties

automatically.

---

Example Chunk

```
Elon Musk is the CEO of Tesla.

Tesla is headquartered in Austin.

Tesla acquired Maxwell Technologies.
```

LLM Output

Nodes

```
Elon Musk

Tesla

Austin

Maxwell Technologies
```

Relationships

```
CEO_OF

HEADQUARTERED_IN

ACQUIRED
```

Graph

```
Elon Musk

      │
   CEO_OF

      │

    Tesla

  /         \

HQ_IN     ACQUIRED

 |            |

Austin     Maxwell
```

---

# LLM Prompting

The LLM is guided using prompts.

Example Prompt

```
You are an Information Extraction Assistant.

Given the following text,

extract:

1. Entities

2. Relationship

Return the output in Cypher Query format.
```

Input

```
Elon Musk is CEO of Tesla.

Tesla acquired Maxwell.
```

Output

```
MERGE (:Person {name:"Elon Musk"})

MERGE (:Company {name:"Tesla"})

MERGE (:Company {name:"Maxwell"})

MERGE (p)-[:CEO_OF]->(t)

MERGE (t)-[:ACQUIRED]->(m)
```

Notice

The LLM directly generates

Cypher Queries.

---

# Cypher Generation

Cypher is the query language used by Neo4j.

Instead of manually creating Nodes,

the generated Cypher is executed directly.

Example

```cypher
MERGE (p:Person {name:"Elon Musk"})

MERGE (c:Company {name:"Tesla"})

MERGE (p)-[:CEO_OF]->(c)
```

Neo4j stores

```
Person

↓

CEO_OF

↓

Company
```

---

# Embedding Generation

Graph RAG also stores embeddings.

Question

Why?

Because embeddings help us perform

Semantic Search.

Each important node gets an embedding.

Example

```
Node

Tesla

↓

Embedding Model

↓

[0.23,0.45,0.67....]
```

Node stored

```
Node

Tesla

Properties

Name

Type

Embedding
```

---

# Neo4j Storage

Neo4j stores

```
Node

↓

Properties

↓

Embedding

↓

Relationship
```

Example

```
Node

Tesla

Name : Tesla

Type : Company

Embedding

[0.45,0.87....]
```

Relationship

```
Tesla

↓

HEADQUARTERED_IN

↓

Austin
```

---

# Complete Ingestion Architecture

```
Raw Documents
       │
       ▼
Document Loader
       │
       ▼
Text Splitter
       │
       ▼
LLM Prompt
       │
       ▼
Entity Extraction

Relationship Extraction
       │
       ▼
Cypher Query
       │
       ▼
Embedding Model
       │
       ▼
Neo4j

Stores

Nodes

Edges

Embeddings
```

At the end of the ingestion pipeline,

we obtain a complete Knowledge Graph.

---

# Graph RAG Retrieval Pipeline

Now suppose the user asks

```
Which company of Elon Musk is headquartered in Austin?
```

Traditional RAG would search similar chunks.

Graph RAG instead searches the Knowledge Graph.

---

## Retrieval Architecture

```
User Query
      │
      ▼
LLM
      │
      ▼
Entity Extraction
      │
      ▼
Cypher Query Generation
      │
      ▼
Neo4j Retrieval
      │
      ▼
Graph Traversal
      │
      ▼
Retrieved Context
      │
      ▼
LLM
      │
      ▼
Final Answer
```

---

### Step 1 : Graph Schema Awareness

Before generating Cypher,

the LLM is provided with the Graph Schema.

Example Schema

```
Person

Company

City

Relationships

CEO_OF

HEADQUARTERED_IN

ACQUIRED
```

Because the LLM already knows the schema,

it generates correct Cypher queries.

---

### Step 2 : Entity Extraction from Query

User Query

```
Which company of Elon Musk is headquartered in Austin?
```

LLM extracts

Entities

```
Elon Musk

Austin
```

Relationship

```
HEADQUARTERED_IN
```

---

## Step 3 : Entry Node Selection

Instead of directly searching the graph,

Graph RAG first finds the

Entry Node.

How?

Entity

↓

Embedding Model

↓

Embedding Vector

↓

Similarity Search

↓

Nearest Node

```
Elon Musk

↓

Embedding

↓

Similarity Search

↓

Elon Musk Node
```

This becomes the

Point of Origin.

---

### Step 4 : Graph Traversal

Once the Entry Node is found,

the graph traversal starts.

Graph RAG commonly uses

### Breadth First Search (BFS)

because BFS explores all neighboring nodes first, maximizing coverage and helping discover all relevant relationships.

Example Graph

```
           Elon Musk

              │

          CEO_OF

              │

            Tesla

        /             \

Austin             SpaceX
```

Traversal

```
Start

↓

Elon Musk

↓

Tesla

↓

Austin

↓

Return Answer
```

---

### Why Breadth First Search?

Suppose the graph contains

```
Person

↓

Company

↓

City

↓

Country
```

BFS explores

all immediate neighbors before moving deeper.

Advantages

- Better coverage
- Finds nearby relationships first
- Useful for multi-hop reasoning
- Efficient for relationship-based questions

---

# Complete Retrieval Pipeline

```
User Query
      │
      ▼
LLM
      │
      ▼
Graph Schema
      │
      ▼
Entity Extraction
      │
      ▼
Embedding Model
      │
      ▼
Similarity Search
      │
      ▼
Entry Node
      │
      ▼
Breadth First Traversal
      │
      ▼
Relevant Nodes
      │
      ▼
Convert to Context
      │
      ▼
LLM
      │
      ▼
Answer
```

---

## LangChain Example

```python
from langchain_community.graphs import Neo4jGraph
from langchain_openai import ChatOpenAI
from langchain.chains import GraphCypherQAChain

graph = Neo4jGraph(
    url="bolt://localhost:7687",
    username="neo4j",
    password="password"
)

llm = ChatOpenAI(model="gpt-4.1-mini")

chain = GraphCypherQAChain.from_llm(
    llm=llm,
    graph=graph,
    verbose=True
)

response = chain.invoke({
    "query":"Which company of Elon Musk is headquartered in Austin?"
})

print(response["result"])
```

Internally,

LangChain performs

```
Question

↓

Cypher Generation

↓

Neo4j Retrieval

↓

Graph Traversal

↓

Context

↓

LLM

↓

Answer
```

---

# Limitations of Graph RAG

Although Graph RAG is powerful, it has several limitations.

### 1. Expensive Ingestion

Creating the graph requires LLM calls for every chunk.

---

### 2. Time-Consuming

Building the Knowledge Graph is much slower than storing vectors in a Vector Database.

---

### 3. Graph Quality Depends on the LLM

If the LLM extracts incorrect entities or relationships, the Knowledge Graph will also be incorrect.

---

### 4. Multiple Models are Used

Graph RAG usually requires both:

- LLM (for graph construction)
- Embedding Model (for semantic search)

This increases cost.

---

### 5. Works Best for Relationship-Based Queries

If there are no meaningful relationships in the data, Graph RAG provides little advantage over Regular RAG.

---

### 6. Domain-Specific

Graph RAG performs best in domains where entities and relationships are important, such as:

- Healthcare
- Finance
- Enterprise Knowledge Bases
- Research Papers
- Supply Chains

---

# When to Use Graph RAG?

Use Graph RAG when:

- Relationship-based reasoning is required.
- Multi-hop questions are common.
- You need explainable retrieval.
- Your data naturally forms a network of connected entities.

Examples:

- Enterprise knowledge graphs
- Biomedical research
- Fraud detection
- Legal document analysis
- Supply chain analysis

---

# When to Use Regular RAG?

Use Regular RAG when:

- Documents are mostly independent.
- Simple semantic retrieval is sufficient.
- You need faster ingestion.
- Lower infrastructure cost is important.
- Relationships between entities are not the primary focus.

Examples:

- FAQ bots
- Company policy assistants
- Document Q&A
- General chatbots
- Product manuals

---

# Key Takeaways

- **Traditional RAG** stores document chunks as embeddings in a Vector Database, whereas **Graph RAG** stores **entities (nodes), relationships (edges), and node embeddings** inside a Graph Database such as Neo4j.
- During **ingestion**, an LLM extracts entities and relationships from text chunks and generates **Cypher** queries that populate the graph.
- Embeddings are still generated for important nodes to support **semantic search** and entry-node selection.
- During **retrieval**, the LLM uses the graph schema to understand the available node and relationship types, extracts entities from the user's query, finds an **entry node** using embeddings, and performs **Breadth First Search (BFS)** graph traversal to gather connected knowledge.
- Graph RAG is ideal for **relationship-rich, multi-hop reasoning problems**, while **Regular RAG** is simpler, faster, and more suitable for semantic document retrieval where explicit relationships are not essential.