# BM25 Retriever and Hybrid Search in RAG — Detailed Notes 

# Table of Contents

Introduction
What is BM25 Retriever?
Why BM25 is Important
Working of BM25
BM25 and TF-IDF Relationship
TF (Term Frequency)
IDF (Inverse Document Frequency)
BM25 Formula
How BM25 Scores Documents
Example of BM25 Retrieval
Difference Between Embedder and Vectorizer
Embeddings vs Keyword Search
Advantages and Disadvantages of BM25
Problem with BM25 when Keywords Don’t Match
What is Hybrid Search?
Why Hybrid Search is Needed
What is Ensemble Retriever?
Weight Assignment in Ensemble Retrieval
BM25 + Vector Retrieval Architecture
LangChain BM25 Retriever Implementation
LangChain Vector Retriever Implementation
Hybrid Search using Ensemble Retriever
LangGraph Flow for Hybrid Retrieval
Real-World Enterprise Examples
Interview Questions & Answers
Key Takeaways

# 1. Introduction

In RAG systems,

retrieval quality is extremely important.

Different retrieval methods solve different problems.

Two major retrieval approaches are:

Retrieval Type
Keyword-based retrieval
Semantic/vector retrieval
Keyword Retrieval

Uses:

Exact keyword matching
Semantic Retrieval

Uses:

Embeddings and vector similarity
Important Insight

Neither method is perfect alone.

So modern RAG systems often combine both.

# 2. What is BM25 Retriever?
Full Form

BM25 = Best Match 25

## Definition

BM25 is a keyword-based ranking algorithm used in information retrieval systems to rank documents based on how well they match query keywords.

Simple Definition

BM25 = Advanced keyword matching retriever.

Main Goal

Find documents containing:

Most relevant keywords

from user query.

Important Observation

### BM25 does NOT understand semantic meaning.

It only focuses on:

Keywords
Example

Query:

AI in hospitals

BM25 searches documents containing:

AI
hospitals
# 3. Why BM25 is Important

Before embeddings existed,

most search engines used:

Keyword matching

BM25 became one of the BEST ranking algorithms.

### Why?

Because simple keyword matching was weak.

BM25 improved ranking using:

Term frequency
Document rarity
Document length normalization

# 4. Working of BM25

MOST IMPORTANT CONCEPT

BM25 is based on:

TF-IDF concepts

with additional optimizations.

Core Idea

A document becomes important if:

✅ Query keywords appear frequently
✅ Keywords are rare across dataset
✅ Document is not unnecessarily long

Workflow

Query
   ↓
Tokenize Keywords
   ↓
Find Matching Documents
   ↓
Calculate BM25 Scores
   ↓
Rank Documents

# 5. BM25 and TF-IDF Relationship

BM25 is an improved version of:

TF-IDF
TF-IDF Meaning
Term	Meaning
TF	Term Frequency
IDF	Inverse Document Frequency
BM25 Enhances TF-IDF By

✅ Better scoring
✅ Length normalization
✅ Saturation handling
✅ Improved ranking quality

6. TF (Term Frequency)
Definition

TF measures:

How many times a keyword appears in a document.
Example

Document:

AI helps hospitals using AI systems

Keyword:

AI

TF:

2
Important Insight

More occurrences generally indicate:

Higher relevance.

## 7. IDF (Inverse Document Frequency)
Definition

IDF measures:

How rare or unique a term is across all documents.
Example

Common word:

the

Appears everywhere.

Low importance.

Rare word
transformer

Appears less frequently.

Higher importance.

Important Idea

Rare words are often:

More informative.

## 8. BM25 Formula
BM25 Formula

BM25(D,Q)=∑IDF(qi)⋅f(qi,D)+k1(1−b+b⋅avgdl∣D∣)f(qi,D)(k1+1)

Important Parameters
Symbol	Meaning
f(qi,D)	Frequency of query term in document
Davgdl	Average document length 
k1 Term frequency scaling
b	Length normalization
Simple Meaning

BM25 balances:

keyword frequency
keyword importance
document length

## 9. How BM25 Scores Documents
    
Example Query
AI in healthcare
Documents
Document
AI diagnosis systems
Football world cup
AI hospitals monitoring
BM25 Ranking
Document	Score
AI hospitals monitoring	High
AI diagnosis systems	Medium
Football world cup	Very low
Why?

Because keyword overlap matters.

### 1.  Example of BM25 Retrieval
Query
transformer architecture
Document 1
Transformer architecture uses attention mechanisms

High BM25 score.

Document 2
Deep learning NLP models

Lower score.

Even if semantically related.

Important Observation

BM25 depends on:

Exact keywords.

# 11. Difference Between Embedder and Vectorizer

MOST IMPORTANT INTERVIEW QUESTION
Vectorizer

Used in traditional NLP.

Converts text into:

Sparse keyword vectors

using methods like:

Bag of Words
TF-IDF

Embedder

Uses deep learning models.

Converts text into:

Dense semantic vectors

Comparison Table

Feature	Vectorizer	Embedder
Focus	Keywords	Meaning
Output	Sparse vectors	Dense vectors
Semantic Understanding	Weak	Strong
Context Awareness	No	Yes
Example	TF-IDF	OpenAI embeddings
Example
Vectorizer
car
automobile

treated differently.

Embedder

Understands:

car ≈ automobile 

#12. Embeddings vs Keyword Search

Feature	                    BM25	        Embeddings
Exact Keywords	            Excellent	    Moderate
Semantic Search	            Weak	        Excellent
Synonym Understanding	    Poor	        Strong
Speed	                    Fast	        Moderate
Interpretability	        Easy	        Hard
Rare Keywords	            Strong	        Sometimes weaker 

13. Advantages and Disadvantages of BM25

Advantages
1. Very Fast

Efficient keyword retrieval.

2. Interpretable

Easy to understand rankings.

3. Excellent for Exact Keywords

Good for:

product IDs
technical keywords
exact terms

4. No Embedding Model Needed

No GPU or embedding cost.

5. Lightweight

Lower infrastructure requirement.

Disadvantages

1. No Semantic Understanding

Cannot understand meaning.

2. Poor Synonym Handling
car ≠ automobile

3. Keyword Dependency

Requires exact keyword overlap.

4. Weak Context Awareness

Does not understand relationships.

# 14. Problem with BM25 when Keywords Don’t Match

MOST IMPORTANT LIMITATION

Suppose query:

automobile industry
Document
car manufacturing companies
Problem

No exact keyword overlap.

BM25 may fail.

Why?

Because BM25 depends on:

Keyword matching

not semantic meaning.

Embedding-Based Retrieval Solves This

Embeddings understand:

automobile ≈ car

# 15. What is Hybrid Search?

Definition

Hybrid search combines:

Keyword retrieval + Semantic retrieval

to improve overall retrieval quality.

Simple Definition

Hybrid Search = BM25 + Vector Search

Why Important?

Because:

BM25 handles exact keywords
Embeddings handle semantic meaning

Together:

✅ Better retrieval quality

Hybrid Search Architecture
User Query
      ↓
 ┌───────────────┐
 │ BM25 Search   │
 └───────────────┘
      ↓

 ┌───────────────┐
 │ Vector Search │
 └───────────────┘
      ↓

Combine Results
      ↓
Final Ranking

# 16. Why Hybrid Search is Needed

BM25 Alone Problem

No semantic understanding.

Embedding Alone Problem

Sometimes exact keywords are important.

Example

Query:

Error code 504

Keyword search may perform better.

Hybrid Search Combines Both Strengths

✅ Exact matching
✅ Semantic understanding

#  17. What is Ensemble Retriever?

Definition

Ensemble Retriever combines multiple retrievers and merges their results using weighted scoring.

Simple Definition

Ensemble Retriever = Multiple retrievers working together.

Example

Combine:

BM25 Retriever
Vector Retriever

Workflow
Query
   ↓
BM25 Retriever
   ↓
Vector Retriever
   ↓
Weighted Combination
   ↓
Final Documents

## 1.  Weight Assignment in Ensemble Retrieval
MOST IMPORTANT HYBRID SEARCH CONCEPT

Each retriever gets:

Weight importance
Example
Retriever	Weight
BM25	0.4
Vector	0.6
Meaning

Vector retrieval contributes more.

Final Score
Final Score =
0.4(BM25 Score)
+
0.6(Vector Score)
Why Useful?

Different systems need different balance.

Example

Technical support chatbot:

May prefer:

Higher BM25 weight

because exact terms matter.

Research chatbot

May prefer:

Higher vector weight

for semantic understanding.

# 19. BM25 + Vector Retrieval Architecture

User Query
      ↓
 ┌─────────────┐
 │ BM25 Search │
 └─────────────┘
      ↓
 ┌─────────────┐
 │ Vector DB   │
 └─────────────┘
      ↓
Ensemble Retriever
      ↓
Weighted Ranking
      ↓
Final Retrieval

# 20. LangChain BM25 Retriever Implementation

## Installation

pip install rank_bm25
pip install langchain-community

## Imports
from langchain_community.retrievers import BM25Retriever
## Documents
documents = [
    \"AI helps healthcare\",
    \"Machine learning improves hospitals\",
    \"Football world cup updates\"
]
## Create BM25 Retriever
bm25_retriever = BM25Retriever.from_texts(
    documents
)
Configure k
bm25_retriever.k = 2
Query
results = bm25_retriever.invoke(
    \"AI in hospitals\"
)

for doc in results:
    print(doc.page_content)

Internal Working
Query
   ↓
Keyword Matching
   ↓
BM25 Scoring
   ↓
Top-k Documents

# 21. LangChain Vector Retriever Implementation

from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings
## Embedding Model
embedding_model = OpenAIEmbeddings(
    model=\"text-embedding-3-small\"
)
## Create Vector Store
vector_store = Chroma.from_texts(
    documents,
    embedding_model
)
## Vector Retriever
vector_retriever = vector_store.as_retriever(
    search_kwargs={\"k\": 2}
)
## Query
results = vector_retriever.invoke(
    \"AI in hospitals\"
)
# 22. Hybrid Search using Ensemble Retriever
## Imports
from langchain.retrievers import EnsembleRetriever
## Create Ensemble Retriever
ensemble_retriever = EnsembleRetriever(
    retrievers=[
        bm25_retriever,
        vector_retriever
    ],
    weights=[0.4, 0.6]
)
## Query
results = ensemble_retriever.invoke(
    \"AI in hospitals\"
)

for doc in results:
    print(doc.page_content)
## What Happens Internally?
BM25 Results
      ↓
Vector Results
      ↓
Weighted Combination
      ↓
Final Ranking
# 23. LangGraph Flow for Hybrid Retrieval
from langgraph.graph import StateGraph
Workflow Concept
User Query
      ↓
Hybrid Retriever Node
      ↓
Retrieved Context
      ↓
LLM Node
      ↓
Final Response
Simple Retrieval Node Example
def retrieve(state):
    query = state[\"query\"]

    docs = ensemble_retriever.invoke(query)

    return {
        \"documents\": docs
    }
Why LangGraph Useful?

Because retrieval becomes:

✅ Modular
✅ Stateful
✅ Multi-step capable

# 24. Real-World Enterprise Examples
Example 1 — Technical Support Chatbot

Uses:

BM25 for error codes
Embeddings for semantic understanding
Example 2 — Research Assistant

Uses hybrid retrieval for:

Exact paper names
Semantic paper concepts
Example 3 — Enterprise Search

Uses:

Keyword retrieval
Semantic retrieval

for accurate internal search.

Example 4 — Legal AI Systems

Combines:

Exact clause retrieval
Semantic legal reasoning
# 25. Interview Questions & Answers
Q1. What is BM25?

BM25 is a keyword-based ranking algorithm used for document retrieval.

Q2. Is BM25 semantic search?

No. BM25 focuses on keyword matching.

Q3. How is BM25 related to TF-IDF?

BM25 is an improved ranking version of TF-IDF.

Q4. Difference between vectorizer and embedder?

Vectorizer creates sparse keyword vectors.
Embedder creates dense semantic vectors.

Q5. Why do we need hybrid search?

Because BM25 handles exact keywords while embeddings handle semantic meaning.

Q6. What is Ensemble Retriever?

It combines multiple retrievers using weighted ranking.

Q7. What happens when keywords do not match in BM25?

BM25 retrieval quality drops because it depends on exact keyword overlap.

Q8. Why is hybrid search powerful?

Because it combines exact keyword matching and semantic understanding.

26. Key Takeaways
Important Concepts

✅ BM25 is keyword-focused retrieval

✅ BM25 is based on TF-IDF principles

✅ BM25 does not understand semantic meaning

✅ Embeddings provide semantic understanding

✅ Hybrid retrieval combines both strengths

✅ Ensemble retriever merges multiple retrievers

✅ Weight assignment controls retrieval importance

✅ Hybrid retrieval is widely used in production RAG systems

Final Summary

BM25 (Best Match 25) is one of the MOST IMPORTANT traditional retrieval algorithms focused on:

Keyword-based document retrieval

using TF-IDF-inspired ranking mechanisms.

However, BM25 struggles with:

❌ Semantic understanding
❌ Synonyms
❌ Contextual meaning

Modern RAG systems therefore use:

Hybrid Search

which combines:

BM25 keyword retrieval
Embedding-based semantic retrieval

through:

Ensemble Retrievers

to achieve:

✅ Better accuracy
✅ Better coverage
✅ Better semantic understanding