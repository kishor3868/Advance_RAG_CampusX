# Maximum Marginal Relevance (MMR) in RAG — Detailed Notes
# Table of Contents
Introduction to MMR
What is MMR?
Why MMR is Needed
Problem with Vanilla Similarity Search
Relevance vs Diversity
How MMR Avoids Redundant Information
How Diversity Improves Coverage
Real-World Example — Research Paper Chatbot
MMR Retrieval Flow
MMR Formula
Understanding Each Term in Formula
Step-by-Step MMR Calculation Example
k, fetch_k and lambda_mult
Tradeoff Between Relevance and Diversity
MMR Implementation using LangChain
Complete LangChain Example
MMR Workflow Internally
Advantages and Disadvantages of MMR
Enterprise Use Cases
Interview Questions & Answers
Key Takeaways

# 1. Introduction to MMR

In Retrieval-Augmented Generation (RAG),

retrieval quality is EXTREMELY important.

Even if embeddings are good,

sometimes retrieval still returns:

❌ Repeated chunks
❌ Redundant information
❌ Similar context again and again

This creates problems for the LLM.

To solve this,

we use:

MMR (Maximum Marginal Relevance)
# 2. What is MMR?

## Definition

Maximum Marginal Relevance (MMR) is an advanced retrieval strategy that balances:

Relevance + Diversity

to retrieve documents that are both:

highly relevant to the query
sufficiently different from each other
Simple Definition

MMR tries to avoid retrieving:

same type of chunks repeatedly

and instead retrieves:

relevant but diverse chunks

## Core Idea

MMR optimizes retrieval by balancing:

Goal	Meaning
Relevance	Related to query
Diversity	Different from already selected chunks

# 3. Why MMR is Needed 

Problem with Basic Similarity Search

Suppose user asks:

How AI helps hospitals?

Simple similarity search may retrieve:

Chunk 1 → AI diagnosis systems
Chunk 2 → AI diagnosis models
Chunk 3 → AI diagnosis accuracy
Problem

All chunks talk about:

SAME THING

This causes:

❌ Redundant context
❌ Poor coverage
❌ Wasted token space
❌ Less informative answers

MMR Solves This

MMR retrieves:

Chunk 1 → AI diagnosis
Chunk 2 → Patient monitoring
Chunk 3 → Drug discovery

Now retrieved context becomes:

✅ Diverse
✅ More informative
✅ Better coverage

## 4. Problem with Vanilla Similarity Search
Vanilla Search Workflow
Query
   ↓
Similarity Search
   ↓
Top Similar Documents

What Happens?

Similarity search only optimizes:

Query relevance

It does NOT care about:

❌ Redundancy
❌ Diversity
❌ Context variety

Example

Query:

AI in healthcare
Retrieved Chunks
1. AI diagnosis system
2. AI diagnosis neural network
3. AI diagnosis deep learning
4. AI diagnosis prediction

All highly similar.

Problem for LLM

LLM receives:

Repeated information

instead of broader context.

# 5. Relevance vs Diversity
MOST IMPORTANT MMR CONCEPT

MMR balances:

Relevance AND Diversity
Relevance

Means:

How related document is to query

Diversity

Means:

How different document is from already selected documents
Goal

Retrieve documents that are:

✅ Relevant to query
✅ Different from each other

Visualization
Query: AI in healthcare

Relevant Chunks:
-------------------------
Diagnosis AI
Monitoring AI
Drug Discovery AI
Medical Imaging AI

Instead of:

Diagnosis AI
Diagnosis AI
Diagnosis AI
Diagnosis AI

# 6. How MMR Avoids Redundant Information

MOST IMPORTANT PRACTICAL CONCEPT

MMR checks:

Similarity between retrieved documents themselves.
Normal Similarity Search

Only checks:

Document ↔ Query
MMR Checks Both
Document ↔ Query
AND
Document ↔ Selected Documents
Important Insight

If new document is TOO SIMILAR to already selected document,

MMR penalizes it.

Result

Redundant chunks are avoided.

Visualization
Selected:
AI Diagnosis

Candidate:
AI Diagnosis Neural Network

Highly similar.

MMR reduces its score.

But This Document
AI Patient Monitoring

is relevant AND diverse.

So MMR prefers it.

# 7. How Diversity Improves Coverage

Why Diversity Matters

LLMs work better when context covers:

Multiple aspects of topic.
Example

Query:

Applications of AI in hospitals
Without Diversity
Diagnosis
Diagnosis
Diagnosis
Diagnosis
With Diversity
Diagnosis
Patient Monitoring
Drug Discovery
Medical Imaging
Benefit

LLM can now generate:

✅ Richer answers
✅ Broader explanations
✅ Better reasoning
✅ More informative responses

8. Real-World Example — Research Paper Chatbot

MOST IMPORTANT REAL-WORLD USE CASE

Suppose you build:

Research Paper Analysis Chatbot
User Query
How transformers improved NLP?
Vanilla Similarity Search

May retrieve:

Transformer attention mechanism
Transformer attention improvement
Transformer attention scaling

All repetitive.

MMR Retrieval
Attention Mechanism
Transfer Learning
BERT Architecture
Scaling Laws
Result

Chatbot gets:

✅ Broader understanding
✅ Better paper coverage
✅ More comprehensive response

Why MMR is Excellent for Research Systems

Research questions require:

Diverse perspectives

not repetitive paragraphs.

1. MMR Retrieval Flow
User Query
      ↓
Generate Query Embedding
      ↓
Initial Similarity Search
(fetch_k candidates)
      ↓
Select Most Relevant Document
      ↓
MMR Re-ranking
(Relevance + Diversity)
      ↓
Select Final k Documents
      ↓
Pass to LLM
# 1.  MMR Formula
MOST IMPORTANT FORMULA

MMR(Di)=λ⋅Similarity(Di,Q)−(1−λ)⋅maxDj ∈S Similarity(Di,Dj)

## 11. Understanding Each Term in Formula
Symbol	Meaning
Di  Candidate document
Q	User query
Dj Already selected document
S	Set of selected documents
λ	Balance between relevance and diversity
Formula Meaning

MMR score depends on:

Positive Component
Similarity(Document, Query)

Higher relevance increases score.

Negative Component
Similarity(Document, Selected Documents)

Higher redundancy decreases score.

Core Goal

Choose documents that are:

✅ Relevant
❌ Not repetitive

# 12. Step-by-Step MMR Calculation Example
Query
AI in healthcare
Candidate Documents
Document	Query Similarity
D1 Diagnosis AI	0.95
D2 Monitoring AI	0.85
D3 Diagnosis Neural Network	0.92
D4 Drug Discovery	0.80
Step 1 — Select Highest Relevance

D1 selected first.

Selected Set S = {D1}
Step 2 — Calculate MMR for Remaining Docs

Suppose:

lambda = 0.7
Candidate D2

Query similarity:

0.85

Similarity with D1:

0.30
Formula

MMR(D2)=0.7(0.85)−0.3(0.30)

Result

0.595−0.09=0.505

Candidate D3

Query similarity:

0.92

Similarity with D1:

0.89

Highly redundant.

Formula

MMR(D
3
	​

)=0.7(0.92)−0.3(0.89)

Result

0.644−0.267=0.377

Candidate D4

Query similarity:

0.80

Similarity with D1:

0.20
Formula

MMR(D
4
	​

)=0.7(0.80)−0.3(0.20)

Result

0.56−0.06=0.50

Final Ranking
Document	MMR Score
D2	0.505
D4	0.50
D3	0.377
Important Observation

Although D3 had high query relevance,

it was penalized because:

It was too similar to D1.
# 13. k, fetch_k and lambda_mult 
k Final number of documents returned.

Example
k=3

Means:

Return final 3 documents

fetch_k

Initial candidate documents retrieved before MMR reranking.

Example
fetch_k=20

Means:

Fetch top 20 candidates first
Then apply MMR
Why Important?

MMR needs larger candidate pool to choose diverse documents.

lambda_mult

Controls:

Relevance vs Diversity tradeoff

# 14. How lambda_mult Manages Diversity and Relevance
    
MOST IMPORTANT PRACTICAL TUNING PARAMETER
lambda_mult = 1

Only relevance.

No diversity.

Equivalent to simple similarity search.

lambda_mult = 0

Only diversity.

No query relevance.

May retrieve unrelated documents.

Balance
lambda_mult	Behavior
1.0	Only relevance
0.8	Mostly relevance
0.5	Balanced
0.2	Mostly diversity
0.0	Only diversity
Best Practice

Usually:

0.5 → 0.8

works well.

Visualization
lambda = 1
Highly relevant
Low diversity

lambda = 0
Highly diverse
Low relevance

# 1.  MMR Implementation using LangChain

## Installation 

pip install langchain
pip install chromadb
pip install langchain-chroma
pip install langchain-openai

## Imports 

from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

## Create Embedding Model 

embedding_model = OpenAIEmbeddings(
    model=\"text-embedding-3-small\"
)

## Sample Documents

texts = [
    \"AI diagnosis systems in hospitals\",
    \"AI monitoring for ICU patients\",
    \"Deep learning diagnosis models\",
    \"Drug discovery using AI\",
    \"Medical imaging with transformers\"
]
## Create Vector Store

vector_store = Chroma.from_texts(
    texts=texts,
    embedding=embedding_model
)

## Create MMR Retriever 

retriever = vector_store.as_retriever(
    search_type=\"mmr\",
    search_kwargs={
        \"k\": 3,
        \"fetch_k\": 10,
        \"lambda_mult\": 0.7
    }
)

## Explanation
 
k	Final returned documents

fetch_k	Candidate pool

lambda_mult	Relevance-diversity balance

Invoke Retriever
docs = retriever.invoke(
    \"How AI helps healthcare?\"
)

for doc in docs:
    print(doc.page_content)
# 16. Complete LangChain Example 

from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

## Embedding model

embedding_model = OpenAIEmbeddings(
    model=\"text-embedding-3-small\"
)

## Documents
texts = [
    \"AI diagnosis systems\",
    \"Patient monitoring AI\",
    \"Drug discovery using AI\",
    \"Medical imaging transformers\",
    \"AI diagnosis neural networks\"
]

## Vector store
vector_store = Chroma.from_texts(
    texts=texts,
    embedding=embedding_model
)

## MMR retriever
retriever = vector_store.as_retriever(
    search_type=\"mmr\",
    search_kwargs={
        \"k\": 3,
        \"fetch_k\": 5,
        \"lambda_mult\": 0.6
    }
)

## Query
query = \"Applications of AI in hospitals\"

## Retrieval
docs = retriever.invoke(query)

## Output
for i, doc in enumerate(docs):
    print(f\"Document {i+1}:\\n\")
    print(doc.page_content)
    print(\"-\"*50)
# 17. MMR Workflow Internally
User Query
      ↓
Embedding Generation
      ↓
Fetch fetch_k Candidate Documents
      ↓
Select Most Relevant Document
      ↓
Calculate MMR Scores
(Relevance - Redundancy)
      ↓
Select Next Best Diverse Document
      ↓
Repeat Until k Documents Selected
      ↓
Pass Diverse Context to LLM
# 18. Advantages and Disadvantages of MMR

## Advantages
1. Reduces Redundancy

Avoids repeated chunks.

2. Improves Context Diversity

Provides broader knowledge.

3. Better LLM Answers

More informative context.

4. Better Coverage

Different aspects of topic retrieved.

5. Excellent for Research Systems

Useful for:

Research papers
Long documents
Knowledge-heavy chatbots

## Disadvantages

1. Slightly More Computation

Needs document-to-document similarity calculations.

2. More Complex Than Vanilla Search

Extra reranking logic.

3. Requires Tuning

Need proper:

lambda_mult
k
fetch_k
4. Wrong Diversity Can Hurt Relevance

Too much diversity may reduce answer quality.

# 19. Enterprise Use Cases 

Example 1 — Research Paper Chatbot

Avoid repetitive sections.

Example 2 — Legal AI Assistant

Retrieve diverse legal clauses.

Example 3 — Enterprise Knowledge Search

Retrieve broad organizational knowledge.

Example 4 — Medical RAG

Retrieve different medical perspectives.

# 20. Interview Questions & Answers
Q1. What is MMR?

MMR is a retrieval strategy balancing relevance and diversity.

Q2. Why do we need MMR?

To avoid redundant retrieval and improve context diversity.

Q3. What problem does vanilla similarity search create?

It may retrieve repetitive chunks.

Q4. What is lambda_mult?

It controls tradeoff between relevance and diversity.

Q5. What is fetch_k?

Number of candidate documents retrieved before MMR reranking.

Q6. Difference between k and fetch_k?

fetch_k = candidate pool
k = final returned documents

Q7. Why is MMR useful in research systems?

Because research questions need broad context coverage.

# 21. Key Takeaways
Important Concepts

✅ MMR balances relevance and diversity

✅ Avoids redundant retrieval

✅ Improves answer coverage

✅ Uses document-to-document similarity

✅ fetch_k creates candidate pool

✅ k defines final returned documents

✅ lambda_mult controls diversity-relevance tradeoff

✅ Excellent for research-heavy RAG systems

Final Summary

MMR (Maximum Marginal Relevance) is one of the MOST IMPORTANT advanced retrieval techniques in modern RAG systems.

Instead of retrieving:

only highly similar chunks

MMR retrieves:

relevant AND diverse chunks

by balancing:

Query relevance
Inter-document diversity

using reranking strategies.

This significantly improves:

✅ Context quality
✅ Answer coverage
✅ LLM grounding
✅ Research-style retrieval

making MMR highly effective for:

Research assistants
Enterprise search
Knowledge chatbots
Long-document RAG systems.