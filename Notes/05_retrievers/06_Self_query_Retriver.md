# Self Query Retriever in RAG — Detailed Notes
Table of Contents
Introduction
What is Self Query Retriever?
Why We Need Self Query Retriever Instead of Vanilla Retriever
Problem with Traditional Semantic Retrieval
What is Query Decomposition in Self Query Retrieval
Structured Query vs Unstructured Query
Metadata Filtering in Self Query Retriever
Product Review Example
How Self Query Retriever Works Internally
Semantic Search + Metadata Filtering
Query Decomposition Flow
Complete Workflow Architecture
How Earlier Systems Worked Without Self Query Retriever
Real-World Use Cases
LangChain Self Query Retriever
Metadata Schema Definition
Creating Vector Store with Metadata
Self Query Retriever Implementation
Query Examples
LangGraph Workflow
Advantages and Disadvantages
Interview Questions & Answers
Key Takeaways
# 1. Introduction

Traditional retrievers mainly focus on:

Semantic similarity search

using embeddings.

Example

Query:

How AI helps healthcare?

Retriever searches:

Semantic meaning

inside embeddings.

But Real-World Queries Are More Complex

Example:

Recommend top 5 laptops under 50k
Problem

Terms like:

under 50k
top 5

may NOT exist inside page content.

Instead they belong to:

Metadata filtering
Solution
Self Query Retriever

# 2. What is Self Query Retriever?
## Definition

Self Query Retriever is an advanced retriever that uses an LLM to convert an unstructured user query into:

Semantic search + structured metadata filtering

before performing retrieval.

Simple Definition

Self Query Retriever intelligently breaks a natural language query into:

✅ Semantic search part
✅ Metadata filtering part

Important Insight

It transforms:

Unstructured query → Structured retrieval query
Example

User Query:

##  Recommend top 5 laptops under 50k
Self Query Retriever Converts Into
Semantic Search:
\"best laptops\"

Metadata Filters:
price < 50000
category = laptop
# 3. Why We Need Self Query Retriever Instead of Vanilla Retriever
MOST IMPORTANT INTERVIEW QUESTION
Vanilla Retriever Only Performs
Semantic similarity search
Problem

Semantic retrieval searches mainly in:

Page content embeddings
But Many Queries Depend on Metadata

Example:

Top phones under 30k
Metadata Information
price
brand
rating
category
date
location

may not exist in document text.

Vanilla Retriever Cannot Handle This Properly

Because it only searches:

Semantic embeddings
Self Query Retriever Solves This

It performs:

✅ Semantic retrieval
+
✅ Metadata filtering

Result

Better and more accurate retrieval.

# 4. Problem with Traditional Semantic Retrieval

Suppose documents contain:

Product	Metadata
HP Laptop	price=60000
Dell Laptop	price=45000
Lenovo Laptop	price=48000
User Query
Recommend laptops under 50k
Problem

The phrase:

under 50k

is NOT semantic meaning.

It is:

Structured filter condition.
Vanilla Similarity Search May Fail

Because embeddings do not naturally enforce:

price < 50000
Self Query Retriever Solves This

By converting query into:

Semantic search + metadata filters
# 5. What is Query Decomposition in Self Query Retrieval
MOST IMPORTANT CONCEPT

Self Query Retriever performs:

Query decomposition
Meaning

Breaking one natural language query into:

Component	Purpose
Semantic part	Embedding search
Filtering part	Metadata filtering
Example

Query:

Recommend top-rated gaming laptops under 70k
Decomposition
Semantic Part
gaming laptops
Filtering Part
price < 70000
rating = high
Important Insight

LLM performs this decomposition automatically.

# 6. Structured Query vs Unstructured Query
Unstructured Query

Human natural language.

Example:

Suggest best mobile phones under 20k with good camera
Structured Query

Machine-readable filters.

Example:

category = mobile
price < 20000
feature = good camera
Self Query Retriever Converts
Unstructured Query
        ↓
Structured Retrieval Query
# 7. Metadata Filtering in Self Query Retriever
MOST IMPORTANT FEATURE

Self Query Retriever searches not only:

Page content

but also:

Metadata
Example Metadata
{
    \"price\": 45000,
    \"category\": \"laptop\",
    \"rating\": 4.5,
    \"brand\": \"Dell\"
}
Important Observation

Metadata often contains:

✅ Structured business information
✅ Numerical filters
✅ Categories
✅ Dates
✅ Ratings

Self Query Retriever Uses These Filters

Automatically.

# 8. Product Review Example 

MOST IMPORTANT PRACTICAL EXAMPLE

Suppose we build:

Product Recommendation Chatbot
User Query
Recommend top 5 laptops under 50k
Document Content
Dell Inspiron laptop with good performance...
Metadata
{
    \"price\": 48000,
    \"category\": \"laptop\",
    \"rating\": 4.4
}
Problem

The condition:

under 50k

is NOT semantic content.

Self Query Retriever Decomposes Query
Semantic Search
laptops
Metadata Filter
price < 50000
Final Retrieval

Only laptops under 50k retrieved.

# 9. How Self Query Retriever Works Internally
Workflow
User Query
      ↓
LLM analyzes query
      ↓
Query decomposition
      ↓
Semantic Part + Metadata Filters
      ↓
Vector Search + Metadata Filtering
      ↓
Final Documents
Important Insight

LLM acts as:

Intelligent query parser.

# 10. Semantic Search + Metadata Filtering
Combined Retrieval

Self Query Retriever performs:

Semantic Similarity Search
+
Metadata Filtering
Example

Query:

Best smartphones under 30k
Semantic Search
smartphones
Metadata Filter
price < 30000
Final Retrieval

Only semantically relevant AND filtered documents returned.

# 11. Query Decomposition Flow
Natural Language Query
          ↓
LLM Parsing
          ↓
 ┌───────────────────────┐
 │ Semantic Search Part  │
 └───────────────────────┘

 ┌───────────────────────┐
 │ Metadata Filters      │
 └───────────────────────┘

          ↓
# 12. Complete Workflow Architecture
User Query
      ↓
LLM Query Parser
      ↓
Structured Query
      ↓

 ┌────────────────────┐
 │ Semantic Retrieval │
 └────────────────────┘

 ┌────────────────────┐
 │ Metadata Filtering │
 └────────────────────┘

      ↓
Combined Results
      ↓
LLM Response
# 13. How Earlier Systems Worked Without Self Query Retriever

Before self-query retrieval,

developers manually wrote:

Metadata filters
Example
filter={
    \"price\": {\"$lt\": 50000}
}
Problem

Manual filter generation required:

❌ Custom logic
❌ Hardcoded parsing
❌ Extra engineering effort

Self Query Retriever Automates This

Using:

LLM-based query understanding
Important Benefit

Natural language queries directly converted into filters.

# 14. Real-World Use Cases
Example 1 — Product Recommendation Systems

Query:

Best gaming laptop under 80k
Example 2 — Movie Recommendation
Sci-fi movies after 2020 with rating above 8
Example 3 — E-Commerce Search
Running shoes under 5k
Example 4 — Enterprise Document Search
HR policies created after 2024
Example 5 — Research Paper Search
Transformer papers after 2022

# 15. LangChain Self Query Retriever

## Installation
pip install langchain
pip install langchain-openai
pip install chromadb
## Imports
from langchain_openai import OpenAIEmbeddings
from langchain_openai import ChatOpenAI
from langchain_chroma import Chroma
Self Query Retriever Import
from langchain.retrievers.self_query.base import (
    SelfQueryRetriever
)
## Metadata Field Description

from langchain.chains.query_constructor.base import (
    AttributeInfo
)
## 16. Metadata Schema Definition
MOST IMPORTANT STEP

We define metadata fields for LLM understanding.

Example
metadata_field_info = [
    AttributeInfo(
        name=\"price\",
        description=\"Price of the laptop\",
        type=\"integer\"
    ),
    AttributeInfo(
        name=\"category\",
        description=\"Type of product\",
        type=\"string\"
    ),
    AttributeInfo(
        name=\"rating\",
        description=\"Product rating\",
        type=\"float\"
    )
]
Why Important?

LLM needs metadata schema to generate filters correctly.

# 17. Creating Vector Store with Metadata
Documents
from langchain_core.documents import Document
Example Documents
documents = [
    Document(
        page_content=\"Dell gaming laptop with RTX graphics\",
        metadata={
            \"price\": 48000,
            \"category\": \"laptop\",
            \"rating\": 4.5
        }
    ),
    Document(
        page_content=\"HP office laptop for productivity\",
        metadata={
            \"price\": 62000,
            \"category\": \"laptop\",
            \"rating\": 4.2
        }
    )
]
## Embedding Model
embedding_model = OpenAIEmbeddings(
    model=\"text-embedding-3-small\"
)
Create Vector Store
vector_store = Chroma.from_documents(
    documents,
    embedding_model
)
## 18. Self Query Retriever Implementation
LLM
llm = ChatOpenAI(
    model=\"gpt-4.1-mini\"
)
Create Retriever
retriever = SelfQueryRetriever.from_llm(
    llm=llm,
    vectorstore=vector_store,
    document_contents=\"Laptop product reviews\",
    metadata_field_info=metadata_field_info
)
Query
docs = retriever.invoke(
    \"Recommend top laptops under 50k\"
)

for doc in docs:
    print(doc.page_content)
What Happens Internally?
LLM parses query
       ↓
Extract filters
       ↓
Perform semantic retrieval
       ↓
Apply metadata filtering
       ↓
Return filtered documents
# 19. Query Examples
Example 1
Best phones under 30k
Generated Filters
category = phone
price < 30000
Example 2
Sci-fi movies after 2021
Generated Filters
genre = sci-fi
year > 2021
Example 3
Top-rated gaming laptops
Generated Filters
category = laptop
rating > 4
# 20. LangGraph Workflow
Import
from langgraph.graph import StateGraph
Retrieval Node
def retrieve(state):

    query = state[\"query\"]

    docs = retriever.invoke(query)

    return {
        \"documents\": docs
    }
LangGraph Architecture
User Query
      ↓
Self Query Retriever Node
      ↓
Retrieved Documents
      ↓
LLM Generation Node
      ↓
Final Response
Why LangGraph Useful?

Because retrieval becomes:

✅ Stateful
✅ Modular
✅ Multi-step capable

# 21. Advantages and Disadvantages
## Advantages
1. Intelligent Metadata Filtering

Automatic filter generation.

2. Natural Language Query Support

Users write normal language queries.

3. Combines Semantic + Structured Search

Very powerful retrieval.

4. Less Manual Engineering

No hardcoded filters needed.

5. Better Business Search Systems

Excellent for e-commerce and enterprise systems.

6. Better User Experience

Users don’t need SQL-like filters.

## Disadvantages
1. Extra LLM Cost

Query parsing requires LLM call.

2. Higher Latency

Additional decomposition step.

3. Metadata Schema Required

Need proper metadata descriptions.

4. LLM Parsing Errors Possible

Wrong filters may be generated.

5. More Complex Retrieval Pipeline

Compared to simple retrievers.

# 22. Interview Questions & Answers
Q1. What is Self Query Retriever?

It uses an LLM to convert natural language queries into semantic search + metadata filters.

Q2. Why do we need Self Query Retriever?

Because many real-world queries contain structured filtering requirements.

Q3. What problem does vanilla retriever have?

It mainly performs semantic retrieval and cannot properly handle metadata filtering.

Q4. What is query decomposition?

Breaking a natural language query into semantic and filtering components.

Q5. Why is metadata important?

Metadata stores structured information like price, rating, category, and dates.

Q6. How does Self Query Retriever use LLM?

LLM parses natural language into structured retrieval filters.

Q7. What is a real-world use case?

Product recommendation systems.

Q8. Why is Self Query Retriever powerful?

Because it combines semantic retrieval with structured metadata filtering.

23. Key Takeaways
Important Concepts

✅ Self Query Retriever performs semantic + metadata retrieval

✅ LLM converts unstructured query into structured filters

✅ Metadata filtering improves retrieval precision

✅ Excellent for e-commerce and enterprise search

✅ Solves limitations of vanilla semantic retrieval

✅ Supports natural language filtering queries

✅ Combines vector search with structured filtering

Final Summary

Self Query Retriever is one of the MOST IMPORTANT advanced retrieval techniques in modern RAG systems.

Instead of relying only on:

semantic similarity search

it intelligently uses an LLM to:

✅ Decompose natural language queries
✅ Generate metadata filters
✅ Combine semantic search with structured retrieval

This enables highly accurate retrieval for real-world systems such as:

Product recommendation engines
Enterprise search systems
Research assistants
E-commerce chatbots
Metadata-heavy AI applications

making Self Query Retriever a critical technique in production-grade RAG pipelines.