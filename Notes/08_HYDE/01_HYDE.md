# HYDE (Hypothetical Document Embeddings) in RAG — Detailed Notes
Table of Contents
Introduction
What is HYDE?
Why We Need HYDE
The Problem HYDE Solves
How Researchers Came Up With HYDE
Query vs Document Distribution Problem
Why User Queries are Often Poor Retrieval Queries
HYDE Workflow
Query → Hypothetical Document Transformation
Average Semantic Meaning Concept
Why We Still Need Vector Store if LLM Generates Document
Complete Retrieval Workflow
Numerical Example
Architecture Diagram
LangChain Implementation
LangGraph Workflow
Advantages and Disadvantages
Interview Questions
Key Takeaways
# 1. Introduction

Traditional RAG retrieval works like this:

User Query
     ↓
Embedding Model
     ↓
Query Embedding
     ↓
Vector Search
     ↓
Retrieved Documents

This works very well when:

Query ≈ Document Language

Unfortunately, this assumption often fails.

Example

User asks:

How did transformers change NLP?

Documents inside vector database contain:

Self-attention mechanism
Encoder-decoder architecture
BERT model
Parallel sequence modeling

Problem:

The query and documents use different vocabulary.

Result:

Poor retrieval quality.

This is exactly why HYDE was introduced.

# 2. What is HYDE?
Definition

HYDE (Hypothetical Document Embeddings) is a retrieval technique where an LLM first generates a hypothetical answer document from the query, embeds that generated document, and uses its embedding for retrieval instead of using the original query embedding.

Simple definition:

Query
 ↓
Generate hypothetical document
 ↓
Embed generated document
 ↓
Retrieve real documents
Key Idea

Instead of embedding:

Short query

we embed:

Rich hypothetical document
# 3. Why We Need HYDE

This is the MOST IMPORTANT question.

User queries are often:

short
ambiguous
incomplete
poorly defined
missing technical terms

Example:

Explain transformers.

This query contains almost no information.

Documents in vector database may contain:

Self-attention mechanism
Positional encoding
Encoder architecture
Decoder architecture
Multi-head attention

The query embedding becomes weak.

Retrieval quality suffers.

HYDE solves this problem by enriching the query.

Example

Original query:

Explain transformers.

Generated hypothetical document:

Transformers are deep learning architectures that use self-attention mechanisms instead of recurrent networks. They use encoder and decoder blocks along with positional encodings and multi-head attention to process sequences efficiently.

This generated document contains:

self attention
encoder
decoder
positional encoding
multi head attention

Now retrieval becomes much better.

# 4. How Researchers Came Up With HYDE

Researchers observed a very interesting behavior:

Documents inside vector databases are usually:

Long
Detailed
Technical
Information Rich

Queries are usually:

Short
Vague
Incomplete

Example:

Documents:

Transformer architecture uses multi-head attention...

Query:

Transformers

Embedding model struggles because:

Query distribution
≠
Document distribution

Researchers asked:

What if we first transform the query into something that looks like the documents we are searching?

This question led to HYDE.

# 5. Query Distribution vs Document Distribution

This is one of the most important concepts.

Vector databases contain embeddings of:

Large documents

Query embedding comes from:

Small sentence

Embedding spaces work better when both belong to similar distributions.

Example:

Type	Average Length
Query	5 words
Document	500 words

This mismatch hurts retrieval.

HYDE reduces this mismatch.

# 6. User Queries Are Often Poor Retrieval Queries

Examples:

Query:

transformers

Query:

AI hospitals

Query:

laptop gaming

These are not good semantic representations.

Embedding models perform much better on:

Detailed text

than on:

Keywords

HYDE converts poor queries into strong semantic representations.

# 7. HYDE Workflow

The complete workflow:

User Query
      ↓
LLM generates hypothetical document
      ↓
Embedding Model
      ↓
Hypothetical Document Embedding
      ↓
Vector Search
      ↓
Retrieved Documents

Notice:

The original query embedding is never used.

# 8. Query → Hypothetical Document Transformation

Suppose query:

How AI helps hospitals?

LLM generates:

Artificial intelligence improves healthcare through diagnosis systems, patient monitoring, predictive analytics, medical imaging and treatment recommendation systems.

This document now looks very similar to documents stored inside the vector database.

Therefore retrieval improves.

# 9. Average Semantic Meaning Concept

This is one of the most important ideas behind HYDE.

Generated hypothetical document contains many concepts:

Diagnosis
Monitoring
Imaging
Prediction
Treatment

Embedding model converts this into:

Single vector

This vector becomes:

Average semantic meaning

of all concepts inside the hypothetical document.

This is similar to document embeddings inside vector databases.

Example:

Generated document discusses:

diagnosis
imaging
prediction

Embedding becomes roughly located in the center of these concepts.

Visualization:

Diagnosis
     \
      \
       \
        X  ← Average semantic vector
       /
      /
Imaging ----- Prediction

This average semantic location helps retrieval discover all related documents.

# 10. Then We Perform Similarity Search

After generating hypothetical embedding:

Generated Document
       ↓
Embedding
       ↓
Vector Store Search

Similarity search now operates using:

Rich semantic representation

instead of:

Short query representation

Retrieval quality improves dramatically.

# 11. Why Are We Going To Vector Store If LLM Already Generated Document?

This is probably the MOST IMPORTANT interview question.

Many students ask:

If LLM generated the answer, why search at all?

Answer:

The generated document is:

Hypothetical

not factual.

Example:

LLM generates:

Transformers were invented in 2016.

Actual fact:

Transformers were introduced in 2017.

Generated document only serves as:

Semantic retrieval guide

Actual answer must come from:

Real documents

inside vector database.

Therefore:

LLM generation is used only to improve retrieval.

Final answer always comes from:

Retrieved evidence

not from hypothetical document.

HYDE Philosophy
Generated document helps us find
the real documents.
# 12. Complete Workflow
User Query
      ↓
LLM generates hypothetical document
      ↓
Generate embedding
      ↓
Search vector store
      ↓
Retrieve actual documents
      ↓
Pass actual documents to LLM
      ↓
Generate final answer
Architecture Diagram
User Query
      ↓
┌────────────────────┐
│        LLM         │
│ Generate Document  │
└────────────────────┘
      ↓
Hypothetical Document
      ↓
Embedding Model
      ↓
Hypothetical Embedding
      ↓
Vector Database
      ↓
Real Documents Retrieved
      ↓
Final LLM Answer
# 13. Why HYDE Gives Better Retrieval

Because it solves:

Vocabulary mismatch

Example:

User query:

AI hospitals

Generated document:

Artificial intelligence improves healthcare through diagnosis, patient monitoring and predictive analytics.

Now vector search can retrieve:

diagnosis
healthcare AI
patient monitoring

Without HYDE these documents may never be retrieved.

# 14. LangChain Implementation
from langchain_openai import ChatOpenAI
from langchain_openai import OpenAIEmbeddings
from langchain_core.output_parsers import StrOutputParser
Step 1: Generate Hypothetical Document
llm = ChatOpenAI(
    model="gpt-4.1-mini"
)

prompt = """
Generate a detailed answer document
for the following question:

Question:
{question}
"""
from langchain_core.prompts import ChatPromptTemplate

template = ChatPromptTemplate.from_template(
    prompt
)

hyde_chain = (
    template
    | llm
    | StrOutputParser()
)

Generate document:

query = "How transformers changed NLP?"

hypothetical_doc = hyde_chain.invoke(
    {"question": query}
)
Step 2: Create Embedding
embeddings = OpenAIEmbeddings(
    model="text-embedding-3-small"
)

query_embedding = embeddings.embed_query(
    hypothetical_doc
)
Step 3: Search Vector Store
docs = vector_store.similarity_search_by_vector(
    query_embedding,
    k=5
)
# 15. LangGraph Workflow
User Query
      ↓
Generate Hypothetical Document Node
      ↓
Embedding Node
      ↓
Retriever Node
      ↓
Generation Node

Example:

def generate_hyde_doc(state):

    query = state["query"]

    doc = hyde_chain.invoke(
        {"question": query}
    )

    return {
        "hyde_doc": doc
    }
def retrieve(state):

    embedding = embeddings.embed_query(
        state["hyde_doc"]
    )

    docs = vector_store.similarity_search_by_vector(
        embedding
    )

    return {
        "documents": docs
    }
# 16. Advantages

✅ Better retrieval recall

✅ Handles vague queries

✅ Solves vocabulary mismatch

✅ Improves semantic search

✅ Works extremely well for domain-specific knowledge bases

# 17. Disadvantages

❌ Extra LLM call

❌ Higher latency

❌ Additional cost

❌ Generated document may introduce bias

❌ Sometimes generated document drifts away from user intent

# 18. Interview Questions
What is HYDE?

Generate hypothetical document first and use its embedding for retrieval.

Why do we need HYDE?

Because user queries are often short and poorly defined.

Why not answer directly from hypothetical document?

Because it may contain hallucinations.

Why does HYDE improve retrieval?

Because generated document resembles documents stored inside vector databases.

What problem does HYDE solve?

Query-document distribution mismatch.

# 19. Key Takeaways

✅ HYDE = Hypothetical Document Embeddings

✅ Query is converted into a hypothetical answer document.

✅ Hypothetical document embedding is used instead of query embedding.

✅ Generated document only guides retrieval.

✅ Final answer always comes from real retrieved documents.

✅ HYDE is particularly powerful for short, ambiguous and poorly specified queries.

✅ One of the most elegant query transformation techniques in modern RAG systems.