# Contextual Compression Retriever in RAG — Detailed Notes
Table of Contents
Introduction
Problem with Normal Retrieval
What is Contextual Compression Retriever?
Why Contextual Compression is Needed
Big Picture Architecture
How Contextual Compression Works
Why We Use LLM in Contextual Compression
LLMChainExtractor
Contextual Compression Workflow
Types of Compressors
LLM Compressor
Embedding Filter
Chain Filter Compressor
LLM Compressor Working
Embedding Filter Working
Chain Filter Working
Contextual Compression Retriever using LangChain
LLMChainExtractor Implementation
Embedding Filter Implementation
Chain Filter Compressor Implementation
Real-World Examples
Advantages and Disadvantages
Interview Questions & Answers
Key Takeaways

# 1. Introduction

In RAG systems,

retrievers often retrieve:

❌ Large chunks
❌ Irrelevant information
❌ Extra context
❌ Noisy documents

This creates problems for LLMs.

Problem

LLMs have:

Context window limitations
Token cost
Attention limitations

Passing unnecessary information reduces:

❌ Answer quality
❌ Efficiency
❌ Precision

Solution

We use:

Contextual Compression Retriever

# 2. Problem with Normal Retrieval

Suppose query:

How AI helps hospitals?
Retriever Returns
10-page document about healthcare

But maybe only:

2 paragraphs

are actually useful.

Problem

LLM receives:

❌ Extra noise
❌ Unnecessary tokens
❌ Irrelevant sections

Result
Higher token cost
Slower response
Reduced answer quality

# 3. What is Contextual Compression Retriever?
Definition

Contextual Compression Retriever is a retriever that compresses retrieved documents by keeping only the most relevant information related to the query before passing context to the LLM.

Simple Definition

Contextual Compression Retriever:

Removes irrelevant parts from retrieved documents.
Important Goal

Instead of sending:

whole document

send only:

important query-related context
Main Benefit

✅ Better context quality
✅ Lower token usage
✅ Better LLM focus

# 4. Why Contextual Compression is Needed

MOST IMPORTANT PRACTICAL CONCEPT

Even good retrievers may return:

Long chunks
Partially relevant documents
Noisy content
Example

Query:

Benefits of transformers in NLP
Retrieved Chunk
Transformer history...
hardware requirements...
GPU training...
attention mechanism...
tokenization...

But maybe user only needs:

attention mechanism section
Contextual Compression Solves This

It extracts:

Only relevant content.

# 5. Big Picture Architecture

User Query
      ↓
Base Retriever
      ↓
Retrieved Documents
      ↓
Contextual Compression
      ↓
Compressed Relevant Context
      ↓
    LLM

Important Insight

Compression happens:

AFTER retrieval
BEFORE generation

# 6. How Contextual Compression Works
Workflow
Query
   ↓
Retriever fetches documents
   ↓
Compressor analyzes relevance
   ↓
Irrelevant content removed
   ↓
Compressed context returned
Goal

Provide:

✅ Cleaner context
✅ Smaller context
✅ More focused retrieval

# 7. Why We Use LLM in Contextual Compression

MOST IMPORTANT CONCEPT

Traditional retrievers only retrieve.

They do NOT understand:

exact relevance
sentence importance
contextual meaning deeply
LLM Helps By

✅ Understanding semantic relevance
✅ Extracting useful information
✅ Removing irrelevant sentences
✅ Query-aware filtering

Example

Query:

How AI improves diagnosis?
LLM Compression

Keeps:

AI helps diagnosis using deep learning models.

Removes:

Hospital management history...

## 1. LLMChainExtractor
   
MOST IMPORTANT LANGCHAIN COMPONENT
Definition

LLMChainExtractor uses an LLM to extract only the relevant portions of retrieved documents based on the query.

Simple Definition

LLMChainExtractor = Query-aware intelligent content extractor.

Important Idea

It performs:

Query-based document compression

using LLM reasoning.

1. Contextual Compression Workflow
User Query
      ↓
Retriever
      ↓
Retrieved Chunks
      ↓
LLMChainExtractor
      ↓
Relevant Sentences Extracted
      ↓
Compressed Documents
      ↓
LLM Response

# 1.  Types of Compressors

Three major compressors:

Compressor Type
LLM Compressor
Embedding Filter
Chain Filter Compressor

## 11. LLM Compressor
Definition

LLM Compressor uses a Large Language Model to intelligently extract relevant content from retrieved documents.

Working

LLM analyzes:

Query
Retrieved document

Then keeps only:

Most relevant parts.
Example

Query:

Applications of AI in hospitals

Retrieved Document

Hospital history...
AI diagnosis...
AI monitoring...
hospital construction...
LLM Compressor Output
AI diagnosis...
AI monitoring...

Why Powerful?

Because LLM understands:

Semantic meaning

not just keywords.

## 12. Embedding Filter
Definition

Embedding Filter removes documents or chunks whose embedding similarity score is below a threshold.

Simple Definition

Embedding Filter = Similarity-based filtering.

Working: 

Query Embedding
       ↓
Compare with Document Embeddings
       ↓
Remove low similarity chunks

### Important Observation

Embedding filter does NOT rewrite content.

It only:

✅ Keeps relevant chunks
❌ Removes irrelevant chunks

Example

Threshold:

0.75

Documents below similarity:

0.75

are removed.

## 13. Chain Filter Compressor
Definition

Chain Filter Compressor combines:

Embedding Filter + LLM Compressor
Simple Definition

Two-stage compression pipeline.

Workflow

Retriever
    ↓
Embedding Filter
(removes irrelevant docs)
    ↓
LLM Compressor
(extracts relevant sentences)
    ↓
Final compressed context
Why Powerful?

Combines:

✅ Fast filtering
✅ Intelligent extraction

Benefit

Efficient + high-quality compression.

# 14. LLM Compressor Working
Detailed Workflow
User Query
      ↓
Retrieved Document
      ↓
LLM analyzes relevance
      ↓
Extract relevant text
      ↓
Compressed document
Important Insight

LLM behaves like:

Intelligent editor

that keeps only useful information.

# 15. Embedding Filter Working
Workflow
Query Embedding
      ↓
Document Embeddings
      ↓
Similarity Calculation
      ↓
Threshold Filtering
      ↓
Relevant Documents Retained
Advantage

Very fast.

Limitation

Cannot understand:

Fine-grained contextual relevance

like LLM can.

# 16. Chain Filter Working
Workflow
Retriever
    ↓
Embedding Filter
    ↓
Relevant Chunks
    ↓
LLM Extraction
    ↓
Compressed Relevant Context

Why Efficient?

Embedding filter reduces:

unnecessary chunks

before expensive LLM processing.

Result

✅ Lower cost
✅ Better efficiency
✅ Better context quality

# 17. Contextual Compression Retriever using LangChain

## Installation
pip install langchain
pip install langchain-openai
pip install chromadb
## Imports
from langchain_openai import ChatOpenAI
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
## Create Documents
documents = [
    \"AI helps diagnosis in hospitals\",
    \"Football world cup updates\",
    \"AI monitoring systems improve ICU care\"
]
## Embeddings
embedding_model = OpenAIEmbeddings(
    model=\"text-embedding-3-small\"
)
## Vector Store
vector_store = Chroma.from_texts(
    documents,
    embedding_model
)
## Base Retriever
retriever = vector_store.as_retriever()
# 18. LLMChainExtractor Implementation
Imports
from langchain.retrievers.document_compressors import (
    LLMChainExtractor
)
## Create LLM
llm = ChatOpenAI(
    model=\"gpt-4.1-mini\"
)
## Create Compressor
compressor = LLMChainExtractor.from_llm(
    llm
)
## Contextual Compression Retriever
from langchain.retrievers import (
    ContextualCompressionRetriever
)
## Create Compression Retriever
compression_retriever = (
    ContextualCompressionRetriever(
        base_compressor=compressor,
        base_retriever=retriever
    )
)
## Query
docs = compression_retriever.invoke(
    \"How AI helps hospitals?\"
)

for doc in docs:
    print(doc.page_content)
What Happens Internally?
Retriever fetches chunks
        ↓
LLM extracts relevant text
        ↓
Compressed context returned

# 19. Embedding Filter Implementation
Imports
from langchain.retrievers.document_compressors import (
    EmbeddingsFilter
)
## Create Filter
embeddings_filter = EmbeddingsFilter(
    embeddings=embedding_model,
    similarity_threshold=0.75
)
Compression Retriever
compression_retriever = (
    ContextualCompressionRetriever(
        base_compressor=embeddings_filter,
        base_retriever=retriever
    )
)
Query
docs = compression_retriever.invoke(
    \"AI diagnosis\"
)
Workflow
Retriever
    ↓
Embedding Similarity Filtering
    ↓
Relevant Chunks
# 20. Chain Filter Compressor Implementation
Imports
from langchain.retrievers.document_compressors import (
    DocumentCompressorPipeline,
    EmbeddingsFilter,
    LLMChainExtractor
)
## Embedding Filter
embedding_filter = EmbeddingsFilter(
    embeddings=embedding_model,
    similarity_threshold=0.70
)
## LLM Extractor
llm_extractor = LLMChainExtractor.from_llm(
    llm
)
## Pipeline Compressor
pipeline_compressor = DocumentCompressorPipeline(
    transformers=[
        embedding_filter,
        llm_extractor
    ]
)
## Contextual Compression Retriever
compression_retriever = (
    ContextualCompressionRetriever(
        base_compressor=pipeline_compressor,
        base_retriever=retriever
    )
)
Query
docs = compression_retriever.invoke(
    \"Applications of AI in hospitals\"
)
Complete Workflow
Retriever
    ↓
Embedding Filter
(remove weak chunks)
    ↓
LLM Extractor
(extract relevant sentences)
    ↓
Compressed Relevant Context

# 21. Real-World Examples
Example 1 — Research Paper Chatbot

Compresses:

long papers
irrelevant paragraphs

before passing to LLM.

Example 2 — Legal AI Assistant

Extracts:

relevant legal clauses

from huge contracts.

Example 3 — Enterprise Knowledge Search

Filters irrelevant company documents.

Example 4 — Medical RAG

Keeps only:

medically relevant context

for accurate answers.

# 22. Advantages and Disadvantages
## Advantages
1. Reduces Token Usage

Smaller prompts.

2. Better Context Quality

Cleaner information.

3. Improves LLM Focus

Less noise.

4. Reduces Hallucination Risk

Better grounding.

5. Better Long-Document Handling

Excellent for PDFs and research papers.

6. More Efficient Context Window Usage

Uses context budget intelligently.

## Disadvantages
1. Extra Latency

Compression adds additional step.

2. LLM Compression Cost

LLM extraction increases API usage.

3. Possible Information Loss

Over-compression may remove useful details.

4. More Complex Pipeline

Additional retrieval components.

5. Embedding Filters are Limited

Cannot deeply understand semantic nuance.

# 23. Interview Questions & Answers
Q1. What is Contextual Compression Retriever?

It compresses retrieved documents by keeping only query-relevant content.

Q2. Why do we need contextual compression?

To reduce irrelevant context and improve LLM efficiency.

Q3. What is LLMChainExtractor?

LLMChainExtractor uses an LLM to extract relevant content from documents.

Q4. What is Embedding Filter?

Embedding Filter removes low-similarity chunks using embedding similarity.

Q5. What is Chain Filter Compressor?

It combines embedding filtering and LLM extraction together.

Q6. Why is contextual compression important in RAG?

Because retrievers may return noisy or oversized context.

Q7. Difference between Embedding Filter and LLM Compressor?

Embedding filter uses similarity thresholds.
LLM compressor uses semantic reasoning.

Q8. Why is chain filtering powerful?

Because it combines fast filtering with intelligent extraction.

24. Key Takeaways
Important Concepts

✅ Contextual compression removes irrelevant content

✅ Compression happens after retrieval

✅ LLMChainExtractor performs intelligent extraction

✅ Embedding Filter uses similarity thresholding

✅ Chain Filter combines filtering + extraction

✅ Compression improves context quality

✅ Reduces token cost and hallucination risk

✅ Important for long-document RAG systems

Final Summary

Contextual Compression Retriever is one of the MOST IMPORTANT advanced retrieval optimization techniques in modern RAG systems.

Instead of passing:

full noisy retrieved documents

it intelligently compresses retrieved content using:

LLM reasoning
Embedding similarity
Multi-stage filtering pipelines

to produce:

cleaner, smaller, and highly relevant context

for the LLM.

Techniques such as:

LLM Compressor
Embedding Filter
Chain Filter Compressor

help improve:

✅ Retrieval precision
✅ Token efficiency
✅ LLM grounding
✅ Long-document understanding

making contextual compression extremely valuable in production-grade RAG systems.