# Quick Notes on Vector Databases + ChromaDB Practical Coding

# 1. Random Important Facts

# ChromaDB uses HNSW

ChromaDB internally uses:

## HNSW (Hierarchical Navigable Small World)

for fast ANN (Approximate Nearest Neighbor) similarity search.

Why?

Because HNSW provides:

* Fast retrieval
* High recall accuracy
* Scalable semantic search

---

# What type of search does FAISS use?

entity["company","FAISS","AI similarity search library"] supports multiple ANN techniques:

* Flat Index (Brute Force)
* IVF (Clustering-based search)
* HNSW
* PQ (Product Quantization)

Most commonly:

## IVF + PQ

for large-scale similarity search.

---

# Why Pinecone is used in production?

entity["company","Pinecone","Vector database company"] is production-focused because:

✅ Fully managed cloud service
✅ Auto scaling
✅ High availability
✅ Fast ANN indexing
✅ Production-grade infrastructure
✅ Easy deployment
✅ Metadata filtering
✅ Distributed architecture

Used heavily in:

* Enterprise RAG
* AI search systems
* Production chatbots

---

# Which DB uses clustering-based algorithm?

Databases using IVF-like clustering techniques:

| Vector Database |
| --------------- |
| FAISS           |
| Milvus          |
| Pinecone        |
| Qdrant          |

Most support:

* IVF
* IVF_PQ
* HNSW

---

# 2. Different Types of Vector Databases

| Database | Type                        |
| -------- | --------------------------- |
| ChromaDB | Lightweight local vector DB |
| FAISS    | Vector similarity library   |
| Pinecone | Managed cloud vector DB     |
| Milvus   | Distributed vector DB       |
| Qdrant   | Open-source vector DB       |
| Weaviate | AI-native vector DB         |

---

# 3. Difference Between Vector Store and Vector Database

| Vector Store                   | Vector Database             |
| ------------------------------ | --------------------------- |
| Generic storage abstraction    | Full database system        |
| Mostly framework-level concept | Infrastructure-level system |
| Used inside LangChain          | Independent DB engine       |
| Simpler                        | Advanced indexing & scaling |
| Example: LangChain wrapper     | Example: Pinecone, Milvus   |

---

# Simple Understanding

## Vector Store

Framework abstraction layer.

---

## Vector Database

Real backend engine storing embeddings.

---

# 4. Why We Generally Use ChromaDB

## Reasons

## 1. Beginner Friendly

Very easy setup.

---

## 2. Local Development

Runs locally without cloud setup.

---

## 3. LangChain Integration

Excellent integration with LangChain.

---

## 4. Persistent Storage Support

Supports:

* In-memory DB
* On-disk persistent DB

---

## 5. Fast Prototyping

Ideal for:

* Learning RAG
* Building POCs
* Small-medium AI systems

---

## 6. Built-in HNSW Search

Provides fast ANN retrieval.

---

# 5. Deep Dive into ChromaDB using LangChain

## Installation

```bash
pip install chromadb
pip install langchain-chroma
pip install langchain-openai
```

---

## 6. In-Memory ChromaDB

### What is In-Memory ChromaDB?

Database exists only during runtime.

After program stops:

❌ Data disappears.

---

### Use Case

Good for:

* Testing
* Learning
* Temporary experiments

---

## Example Code

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings
```

---

## Create Embedding Model

```python
embedding_model = OpenAIEmbeddings(
    model="text-embedding-3-small"
)
```

---

## Sample Documents

```python
texts = [
    "AI helps healthcare",
    "Machine learning improves hospitals",
    "Football world cup updates"
]
```

---

## Create In-Memory ChromaDB

```python
vector_store = Chroma.from_texts(
    texts=texts,
    embedding=embedding_model
)
```

---

## Similarity Search

```python
results = vector_store.similarity_search(
    "How AI helps hospitals?",
    k=2
)

for result in results:
    print(result.page_content)
```

---

# Internal Workflow

```text
Documents
    ↓
Embeddings
    ↓
ChromaDB
    ↓
HNSW Indexing
    ↓
Similarity Search
```

---

# 7. Persistent ChromaDB (On-Disk)

## What is Persistent ChromaDB?

Database stored permanently on disk.

Even after restart:

✅ Data remains available.

---

### Use Case

Used in:

* Production systems
* Persistent RAG pipelines
* Enterprise AI systems

---

### Create Persistent ChromaDB

```python
from langchain_chroma import Chroma
```

---

### Persistent Path

```python
persist_directory = "./chroma_langchain_db"
```

---

### Create DB

```python
vector_store = Chroma.from_texts(
    texts=texts,
    embedding=embedding_model,
    persist_directory=persist_directory
)
```

---

## What Happens?

```text
Embeddings
    ↓
Stored on Disk
    ↓
Available after restart
```

---

## Reload Existing ChromaDB

```python
vector_store = Chroma(
    persist_directory="./chroma_langchain_db",
    embedding_function=embedding_model
)
```

---

# 8. How to Store Data in ChromaDB

## Method 1 — Using Texts

```python
vector_store.add_texts([
    "Deep learning for healthcare",
    "AI diagnosis systems"
])
```

---

## Method 2 — Using Documents

```python
from langchain_core.documents import Document
```

---

### Create Documents

```python
docs = [
    Document(
        page_content="AI in finance",
        metadata={"topic": "finance"}
    ),
    Document(
        page_content="Medical AI systems",
        metadata={"topic": "medical"}
    )
]
```

---

### Add Documents

```python
vector_store.add_documents(docs)
```

---

# 9. CRUD Operations in ChromaDB

## C → Create

### Add New Documents

```python
vector_store.add_texts([
    "Neural networks in healthcare"
])
```

---

## R → Retrieve

### Similarity Search

```python
results = vector_store.similarity_search(
    "AI in hospitals"
)
```

---

### Retrieve with Scores

```python
results = vector_store.similarity_search_with_score(
    "AI in healthcare"
)
```

---

### Metadata Filtering

```python
results = vector_store.similarity_search(
    "AI",
    filter={"topic": "medical"}
)
```

---

## U → Update

### ChromaDB does not directly update text.

Usually workflow is:

```text
Delete Old Record
      ↓
Add Updated Record
```

---

## Example

```python
vector_store.delete(ids=["doc1"])
```

Then:

```python
vector_store.add_texts(
    texts=["Updated healthcare AI text"],
    ids=["doc1"]
)
```

---

## D → Delete

### Delete Documents

```python
vector_store.delete(ids=["doc1"])
```

---

## Delete Entire Collection

```python
vector_store.delete_collection()
```

---

# 10. Similarity Search Variants

## Standard Similarity Search

```python
results = vector_store.similarity_search(
    "AI in healthcare"
)
```

---

## Search with Score

```python
results = vector_store.similarity_search_with_score(
    "AI in healthcare"
)
```

---

## Max Marginal Relevance (MMR)

Used to improve diversity.

```python
results = vector_store.max_marginal_relevance_search(
    "AI in healthcare",
    k=2
)
```

---

# 11. Retriever Interface in LangChain

## Convert Vector Store into Retriever

```python
retriever = vector_store.as_retriever()
```

---

## Retrieve Documents

```python
docs = retriever.invoke(
    "How AI helps hospitals?"
)
```

---

## Configure Retriever

```python
retriever = vector_store.as_retriever(
    search_kwargs={"k": 3}
)
```

---

# 12. ChromaDB Connection Types

## Two Common Connection Modes

| Connection Type    | Description          |
| ------------------ | -------------------- |
| In-memory          | Temporary runtime DB |
| Persistent/on-disk | Permanent storage    |

---

## In-Memory Connection

```python
vector_store = Chroma.from_texts(
    texts=texts,
    embedding=embedding_model
)
```

---

## Persistent Connection

```python
vector_store = Chroma(
    persist_directory="./chroma_langchain_db",
    embedding_function=embedding_model
)
```

---

# 13. Using ChromaDB with PDF Loader

```python
from langchain_community.document_loaders import PyPDFLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
```

---

## Load PDF

```python
loader = PyPDFLoader("sample.pdf")
documents = loader.load()
```

---

## Split Documents

```python
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)

chunks = splitter.split_documents(documents)
```

---

## Store in ChromaDB

```python
vector_store = Chroma.from_documents(
    documents=chunks,
    embedding=embedding_model,
    persist_directory="./pdf_chroma_db"
)
```

---

## Search from PDF Knowledge Base

```python
results = vector_store.similarity_search(
    "What is AI?"
)
```

---

# 14. Complete RAG Flow with ChromaDB

```text
PDF/Documents
      ↓
Document Loader
      ↓
Text Splitter
      ↓
Embedding Model
      ↓
ChromaDB
      ↓
HNSW Indexing
      ↓
Similarity Retrieval
      ↓
LLM
```

---

# 15. Why ChromaDB is Popular in Learning RAG

## Reasons

✅ Very easy setup
✅ No cloud configuration
✅ Excellent LangChain integration
✅ Fast prototyping
✅ Persistent local DB
✅ Lightweight
✅ Beginner friendly

---

# 16. Important Interview Questions

# Q1. Which indexing algorithm does ChromaDB use?

ChromaDB primarily uses HNSW indexing.

---

# Q2. Why is ChromaDB popular in LangChain projects?

Because it is lightweight, beginner-friendly, and integrates easily with LangChain.

---

# Q3. Difference between in-memory and persistent ChromaDB?

In-memory DB disappears after runtime.
Persistent DB stores data permanently on disk.

---

# Q4. How does ChromaDB perform similarity search?

Using ANN indexing with HNSW.

---

# Q5. Why is Pinecone preferred in production?

Because it provides managed scalable infrastructure and production-grade vector search.

---

# Q6. Does ChromaDB support CRUD operations?

Yes, it supports create, retrieve, update (delete+add), and delete operations.

---

# 17. Final Summary

ChromaDB is one of the MOST POPULAR lightweight vector databases used for:

* Learning RAG
* Rapid prototyping
* Local AI applications
* LangChain projects

Internally it uses:

# HNSW indexing

for fast ANN similarity search.

It supports:

✅ In-memory databases
✅ Persistent on-disk storage
✅ CRUD operations
✅ Similarity retrieval
✅ Metadata filtering
✅ LangChain retrievers

making it an excellent choice for building modern RAG systems locally and efficiently.
