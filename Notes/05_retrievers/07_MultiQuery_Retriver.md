# Multi Query Retriever in RAG — Detailed Notes
Table of Contents
Introduction
What is Multi Query Retriever?
Why We Need Multi Query Retriever
Problem with Single Query Retrieval
Query Perspective Problem
How Multi Query Retriever Solves This Problem
How Retriever Reframes Query into Multiple Perspectives
Multi Query Retrieval Workflow
Product Review Example
Real-World Examples
Multi Query Architecture
Query Reformulation Process
How LLM Generates Multiple Queries
Retrieval Flow Internally
Advantages and Disadvantages
LangChain Multi Query Retriever
Full LangChain Code Implementation
LangGraph Workflow
Enterprise Use Cases
Interview Questions & Answers
Key Takeaways
# 1. Introduction

Traditional RAG retrieval usually works like this:

One User Query
        ↓
One Embedding Search
        ↓
Retrieved Documents
Problem

One query may not represent:

All semantic meanings

of user intent.

Example

Query:

How AI helps hospitals?
But documents may contain:
medical diagnosis
patient monitoring
clinical AI systems
healthcare automation
Problem

Single query may miss:

❌ Relevant perspectives
❌ Alternative terminology
❌ Domain-specific wording

Solution
Multi Query Retriever
# 2. What is Multi Query Retriever?
# Definition

Multi Query Retriever is a retriever that uses an LLM to generate multiple reformulated versions of a user query and retrieves documents using all generated queries.

Simple Definition

Instead of searching with:

One query

it searches using:

Multiple semantic variations of the query.
Important Insight

Different query formulations retrieve:

Different relevant documents.
Goal

Increase:

✅ Retrieval coverage
✅ Recall
✅ Semantic diversity

# 3. Why We Need Multi Query Retriever
MOST IMPORTANT INTERVIEW QUESTION
Problem with Single Query Retrieval

One query embedding captures only:

One semantic representation.
But Human Language Has Multiple Expressions

Example:

AI in healthcare

can also mean:

machine learning in medicine
clinical AI systems
medical automation
hospital intelligence systems
Single Query May Miss Documents

Because document wording may differ.

Multi Query Retriever Solves This

By generating:

Multiple semantically related queries

using LLM reasoning.

Result

Better retrieval coverage.

# 4. Problem with Single Query Retrieval

Suppose user asks:

How transformers improved NLP?
Documents May Contain
attention mechanisms
language modeling improvements
BERT architectures
sequence-to-sequence learning
Problem

Original query may retrieve only:

transformer-related chunks

and miss:

❌ BERT
❌ attention-specific chunks
❌ language modeling sections

Why?

Because semantic phrasing differs.

# 5. Query Perspective Problem
MOST IMPORTANT CONCEPT

One user query usually represents:

Only one perspective

of the information need.

Example

Query:

Best laptops for gaming
Related Semantic Perspectives
high GPU laptops
RTX gaming laptops
high-performance laptops
gaming notebooks
Single Retrieval May Miss Some Documents
Multi Query Retriever Expands Perspectives

Automatically.

# 6. How Multi Query Retriever Solves This Problem
Core Idea

Use an LLM to:

Reframe query into multiple semantic variations.
Workflow
Original Query
       ↓
LLM Generates Multiple Queries
       ↓
Multiple Retrieval Searches
       ↓
Combine Results
       ↓
Unique Relevant Documents
Benefit

Improves:

✅ Recall
✅ Coverage
✅ Semantic diversity

# 7. How Retriever Reframes Query into Multiple Perspectives
MOST IMPORTANT TOPIC

LLM understands:

semantics
synonyms
domain terminology
alternative phrasing
Example

Original Query:

How AI helps hospitals?
LLM Reformulates Into
1. Machine learning applications in healthcare
2. Clinical AI systems
3. AI diagnosis in hospitals
4. Healthcare automation using AI
5. AI-based patient monitoring
Important Insight

Each query explores:

Different semantic angle

of same topic.

Result

Retriever finds:

More comprehensive documents.
# 8. Multi Query Retrieval Workflow
User Query
      ↓
LLM Query Generator
      ↓
Multiple Reformulated Queries
      ↓
Embedding Search for Each Query
      ↓
Combine Retrieved Documents
      ↓
Remove Duplicate Documents
      ↓
Final Retrieval
Important Observation

Multi-query retrieval improves:

Recall

rather than precision.

# 9. Product Review Example
MOST IMPORTANT PRACTICAL EXAMPLE

Suppose we build:

Product Review Chatbot
User Query
Best laptops for gaming
Documents May Contain
RTX laptops
High GPU systems
Gaming notebooks
High-performance computers
Problem

Original query may not retrieve all documents.

Multi Query Retriever Generates
1. RTX gaming laptops
2. High GPU laptops
3. Gaming notebooks
4. High-performance laptops
Result

More relevant product documents retrieved.

Why Useful in Product Reviews?

Because users use:

Different terminology

than product descriptions.

# 10. Real-World Examples
Example 1 — Research Paper Search

Query:

How transformers improved NLP?

Generated Queries:

attention mechanisms
BERT models
language modeling
sequence learning
Example 2 — Medical AI Search

Query:

AI in hospitals

Generated Queries:

clinical AI
patient monitoring
medical automation
Example 3 — E-Commerce Search

Query:

Budget gaming laptops

Generated Queries:

RTX laptops
high-performance notebooks
gaming computers
# 11. Multi Query Architecture
User Query
      ↓
LLM Query Rewriter
      ↓

 ┌────────────────────┐
 │ Query 1            │
 │ Query 2            │
 │ Query 3            │
 │ Query 4            │
 └────────────────────┘

      ↓

Multiple Retrieval Searches

      ↓

Combine Results
      ↓
Deduplicate Documents
      ↓
Final Retrieval
# 12. Query Reformulation Process
MOST IMPORTANT INTERNAL PROCESS

LLM analyzes:

semantics
intent
domain language
alternative expressions
Then Generates
Multiple semantically related queries.
Example

Query:

Cloud security
Reformulated Queries
cloud infrastructure security
cybersecurity in cloud systems
AWS security architecture
secure cloud deployment
Important Observation

LLM performs:

Semantic expansion

of query intent.

# 13. How LLM Generates Multiple Queries
Workflow
Original Query
       ↓
LLM Prompting
       ↓
Semantic Expansion
       ↓
Alternative Queries
Why LLM is Powerful?

Because LLM understands:

✅ Synonyms
✅ Domain-specific terminology
✅ Related concepts
✅ Contextual meaning

# 14. Retrieval Flow Internally
User Query
      ↓
LLM Generates Queries
      ↓
Embedding Search for Each Query
      ↓
Retrieve Documents
      ↓
Merge Results
      ↓
Remove Duplicates
      ↓
Final Retrieved Context
Important Insight

Each query retrieves:

Different semantic neighborhood

inside embedding space.

# 15. Advantages and Disadvantages
## Advantages
1. Better Retrieval Recall

Finds more relevant documents.

2. Better Semantic Coverage

Covers multiple perspectives.

3. Reduces Query Ambiguity

Explores alternative meanings.

4. Excellent for Research Systems

Useful for broad exploratory queries.

5. Improves Long-Tail Retrieval

Captures rare terminology.

6. Better Enterprise Search

Improves knowledge discovery.

## Disadvantages
1. Higher Latency

Multiple retrieval searches required.

2. More Token Cost

LLM generates multiple queries.

3. More Retrieved Noise

May retrieve unnecessary documents.

4. Higher Computational Cost

Multiple embedding searches.

5. Deduplication Required

Duplicate documents may appear.

# 16. LangChain Multi Query Retriever
## Installation
pip install langchain
pip install langchain-openai
pip install chromadb
## Imports
from langchain_openai import OpenAIEmbeddings
from langchain_openai import ChatOpenAI
from langchain_chroma import Chroma
Multi Query Retriever Import
from langchain.retrievers.multi_query import (
    MultiQueryRetriever
)
## 17. Full LangChain Code Implementation
Sample Documents
texts = [
    \"AI diagnosis systems in hospitals\",
    \"Machine learning for healthcare\",
    \"Clinical AI systems improve patient care\",
    \"Healthcare automation using deep learning\",
    \"Football world cup updates\"
]
Embedding Model
embedding_model = OpenAIEmbeddings(
    model=\"text-embedding-3-small\"
)
## Create Vector Store
vector_store = Chroma.from_texts(
    texts,
    embedding_model
)
## Base Retriever
retriever = vector_store.as_retriever()
LLM
llm = ChatOpenAI(
    model=\"gpt-4.1-mini\"
)
## Create Multi Query Retriever
multi_query_retriever = (
    MultiQueryRetriever.from_llm(
        retriever=retriever,
        llm=llm
    )
)
## Query
docs = multi_query_retriever.invoke(
    \"How AI helps hospitals?\"
)

for doc in docs:
    print(doc.page_content)
What Happens Internally?
Original Query
       ↓
LLM creates multiple query variations
       ↓
Retriever searches for each query
       ↓
Results merged together
# 18. LangGraph Workflow
## Import
from langgraph.graph import StateGraph
## Retrieval Node
def retrieve(state):

    query = state[\"query\"]

    docs = multi_query_retriever.invoke(query)

    return {
        \"documents\": docs
    }
## LangGraph Architecture
User Query
      ↓
Multi Query Retriever Node
      ↓
Expanded Retrieval
      ↓
LLM Generation Node
      ↓
Final Response
Why LangGraph Useful?

Because retrieval becomes:

✅ Stateful
✅ Modular
✅ Multi-step capable

# 19. Enterprise Use Cases
Example 1 — Research Assistant

Retrieves papers from multiple semantic perspectives.

Example 2 — Enterprise Knowledge Search

Handles varied employee terminology.

Example 3 — Medical RAG

Retrieves clinical knowledge using multiple medical expressions.

Example 4 — E-Commerce Search

Captures different product naming styles.

Example 5 — Legal AI

Retrieves legal clauses using varied legal terminology.

# 20. Interview Questions & Answers
Q1. What is Multi Query Retriever?

It uses an LLM to generate multiple reformulated queries for retrieval.

Q2. Why do we need Multi Query Retriever?

Because one query may not capture all semantic variations of user intent.

Q3. What problem does it solve?

Improves retrieval recall and semantic coverage.

Q4. How does LLM help?

LLM generates semantically related query variations.

Q5. What is the main advantage?

Better retrieval coverage.

Q6. What is the main disadvantage?

Higher latency and computational cost.

Q7. Why is it useful in product recommendation systems?

Because product terminology may differ from user wording.

Q8. Difference between Self Query Retriever and Multi Query Retriever?

Self Query Retriever focuses on:

semantic + metadata filtering

Multi Query Retriever focuses on:

multiple semantic query reformulations
# 21. Key Takeaways
Important Concepts

✅ Multi Query Retriever generates multiple semantic query variations

✅ Improves retrieval recall and coverage

✅ LLM reframes queries into different semantic perspectives

✅ Excellent for research and enterprise search

✅ Helps retrieve documents using varied terminology

✅ Uses multiple embedding searches internally

✅ Commonly used in advanced production RAG systems

Final Summary

Multi Query Retriever is one of the MOST IMPORTANT advanced retrieval techniques in modern RAG systems.

Instead of relying on:

a single semantic query

it uses an LLM to generate:

multiple semantically related query variations

which helps retrieve:

✅ More diverse documents
✅ Better semantic coverage
✅ Better recall
✅ Better knowledge discovery

This makes Multi Query Retriever highly useful for:

Research assistants
Enterprise knowledge systems
Product recommendation chatbots
Medical AI systems
Large-scale semantic search platforms

where information may be expressed using many different terminologies and perspectives.