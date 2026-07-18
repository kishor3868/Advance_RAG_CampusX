# Guardrails in RAG – Part 2B : Retrieval-Level Guardrails

# Table of Contents

1. Introduction to Retrieval-Level Guardrails
2. Why Retrieval-Level Guardrails are Required
3. Retrieval-Level Guardrail Pipeline
4. Relevance Threshold
5. Source Whitelisting
6. PII Scrubbing
7. Redundancy Filtering
8. Context Window Control
9. Complete Retrieval Guardrail Pipeline
10. LangChain Implementation
11. Key Takeaways

---

# 1. Introduction to Retrieval-Level Guardrails

In the previous section, we discussed **Query-Level Guardrails**, which validate the user query before it reaches the Retriever.

Once the Retriever performs similarity search, another important question arises:

> **Can we directly trust every retrieved document?**

The answer is **No**.

A Retriever may retrieve:

- Irrelevant documents
- Duplicate chunks
- Sensitive documents
- Documents from untrusted sources
- More context than the LLM can process effectively

Therefore, before passing the retrieved documents to the LLM, we apply **Retrieval-Level Guardrails**.

These guardrails improve the **quality, safety, and efficiency** of the retrieved context.

---

# 2. Why Retrieval-Level Guardrails are Required?

Suppose a user asks:

```
What is Graph RAG?
```

Retriever returns:

```
Chunk 1
Graph RAG combines Knowledge Graphs with RAG.
✔ Relevant

------------------------------------

Chunk 2
Python was created by Guido van Rossum.
❌ Irrelevant

------------------------------------

Chunk 3
Graph RAG combines Knowledge Graphs with RAG.
❌ Duplicate

------------------------------------

Chunk 4
Employee Salary Database
❌ Confidential

------------------------------------

Chunk 5
Random Blog from Unknown Website
❌ Untrusted Source
```

If we send all these chunks to the LLM,

- Token cost increases.
- Hallucination increases.
- Sensitive information may leak.
- Response quality decreases.

Therefore,

Retrieval-Level Guardrails filter and clean the retrieved context before sending it to the LLM.

---

# Retrieval-Level Guardrail Architecture

```
                  User Query

                      │

                      ▼

                  Retriever

                      │

             Retrieved Chunks

                      │

      Retrieval-Level Guardrails

 ┌───────────────────────────────────────┐

 │ Relevance Threshold                  │

 │ Source Whitelisting                  │

 │ PII Scrubbing                        │

 │ Redundancy Filtering                 │

 │ Context Window Control               │

 └───────────────────────────────────────┘

                      │

                 Clean Context

                      │

                      ▼

                     LLM
```

---

# 3. Relevance Threshold

## Definition

**Relevance Threshold is a guardrail that removes retrieved chunks whose similarity score is below a predefined threshold.**

Only highly relevant chunks are passed to the LLM.

---

# Why is it Needed?

A Vector Database always returns the **Top-K** nearest vectors.

However,

Top-K does **not guarantee** that every retrieved chunk is relevant.

Example

| Chunk | Similarity Score |
|--------|------------------|
| Graph RAG Introduction | 0.94 |
| Graph Database | 0.90 |
| Python Programming | 0.42 |
| Cricket News | 0.28 |

Suppose we set

```
Threshold = 0.75
```

Only

```
0.94

0.90
```

will be forwarded.

The remaining chunks are discarded.

---

# Architecture

```
Retrieved Chunks

↓

Similarity Scores

↓

Threshold

↓

Relevant?

YES

↓

Pass to LLM
```

```
Score

< Threshold

↓

Discard
```

---

# Example

Suppose

```
Threshold = 0.80
```

Retrieved Chunks

```
Chunk A

0.95

✔

-------------------

Chunk B

0.87

✔

-------------------

Chunk C

0.63

✘

-------------------

Chunk D

0.42

✘
```

Only A and B reach the LLM.

---

# LangChain Implementation

```python
retriever = vectorstore.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={
        "score_threshold": 0.80,
        "k":5
    }
)

docs = retriever.invoke(
    "Explain Graph RAG"
)
```

---

# Advantages

- Removes weak matches.
- Reduces hallucination.
- Improves retrieval quality.
- Saves tokens.

---

# 4. Source Whitelisting

## Definition

**Source Whitelisting allows retrieval only from trusted and approved document sources.**

Instead of trusting every retrieved document,

we trust only documents coming from authorized sources.

---

# Why is it Needed?

Suppose your Vector Database contains

- Internal Company Documents
- Employee Policies
- Internet Blogs
- Random PDFs
- User Uploaded Files

If a medical chatbot retrieves data from an unknown blog,

the information may be incorrect.

Instead,

we allow only trusted sources.

---

# Example

Allowed Sources

```
WHO

NIH

Internal Company Wiki

Official Documentation
```

Blocked Sources

```
Random Blog

Unknown Website

Personal Notes
```

---

# Architecture

```
Retrieved Documents

↓

Check Metadata

↓

Trusted Source?

YES

↓

Pass to LLM
```

```
Untrusted Source

↓

Discard
```

---

# Example Metadata

```python
{
    "source":"WHO"
}
```

Allowed

```python
{
    "source":"Internal Wiki"
}
```

Allowed

```python
{
    "source":"randomblog.xyz"
}
```

Rejected

---

# LangChain Implementation

```python
trusted_sources = [
    "WHO",
    "Internal Wiki",
    "Company Docs"
]

filtered_docs = [
    doc for doc in docs
    if doc.metadata["source"] in trusted_sources
]
```

---

# Advantages

- Prevents misinformation.
- Improves trust.
- Ensures document authenticity.
- Better compliance.

---

# 5. PII Scrubbing

## Definition

**PII Scrubbing removes or masks sensitive information present inside retrieved documents before they are sent to the LLM.**

Unlike Query-Level PII Detection,

here we inspect the **retrieved documents**, not the user query.

---

# Why is it Needed?

Suppose Retriever fetches

```
Employee Name

John Smith

Salary

₹25,00,000

PAN

ABCDE1234F
```

The user asked

```
Explain HR Policy.
```

The salary and PAN are unnecessary.

PII Scrubbing removes such information.

---

# Architecture

```
Retrieved Context

↓

PII Detector

↓

Sensitive Data?

YES

↓

Mask Information

↓

LLM
```

---

# Example

Before

```
PAN

ABCDE1234F
```

After

```
PAN

*********
```

---

# LangChain Implementation

```python
import re

for doc in docs:

    doc.page_content = re.sub(
        r"[A-Z]{5}[0-9]{4}[A-Z]",
        "********",
        doc.page_content
    )
```

---

# Advantages

- Prevents sensitive information leakage.
- Improves compliance.
- Protects confidential data.

---

# 6. Redundancy Filtering

## Definition

**Redundancy Filtering removes duplicate or near-duplicate retrieved chunks before sending them to the LLM.**

---

# Why is it Needed?

Suppose Retriever returns

```
Chunk 1

Graph RAG uses Knowledge Graph.

-------------------------

Chunk 2

Graph RAG uses Knowledge Graph.

-------------------------

Chunk 3

Graph RAG combines KG with Retrieval.
```

Chunk 1 and Chunk 2 contain the same information.

Passing both wastes context window.

---

# Architecture

```
Retrieved Chunks

↓

Duplicate Detection

↓

Duplicate?

YES

↓

Remove

NO

↓

Keep
```

---

# Example

Before Filtering

```
Chunk 1

Chunk 2

Chunk 3

Chunk 4
```

After Filtering

```
Chunk 1

Chunk 3
```

---

# LangChain Implementation

```python
unique_docs = []

seen = set()

for doc in docs:

    if doc.page_content not in seen:

        unique_docs.append(doc)

        seen.add(doc.page_content)
```

---

# Advantages

- Removes repeated information.
- Saves tokens.
- Improves diversity.
- Better LLM reasoning.

---

# 7. Context Window Control

## Definition

**Context Window Control limits how much retrieved context is passed to the LLM so that it fits within the model's context window.**

---

# Why is it Needed?

Every LLM has a maximum context length.

Example

```
GPT-4.1

↓

128K Tokens
```

Suppose Retriever returns

```
250K Tokens
```

The LLM cannot process the entire context.

Therefore,

we must control the amount of retrieved context.

---

# Architecture

```
Retrieved Chunks

↓

Token Counter

↓

Within Limit?

YES

↓

LLM
```

```
Too Large

↓

Trim Context

↓

LLM
```

---

# Example

Retrieved

```
12 Chunks

↓

180K Tokens
```

Maximum

```
128K Tokens
```

After Context Control

```
Best 7 Chunks

↓

120K Tokens
```

---

# LangChain Implementation

```python
retriever = vectorstore.as_retriever(
    search_kwargs={
        "k":4
    }
)
```

or

```python
from langchain.retrievers import ContextualCompressionRetriever

compression_retriever = (
    ContextualCompressionRetriever(
        base_retriever=retriever,
        base_compressor=compressor
    )
)
```

---

# Advantages

- Prevents context overflow.
- Reduces API cost.
- Faster inference.
- Better response quality.

---

# 8. Complete Retrieval-Level Guardrail Pipeline

```
                  User Query

                      │

                      ▼

                  Retriever

                      │

                      ▼

            Retrieved Documents

                      │

        ┌──────────────────────────┐

        │ Relevance Threshold      │

        └──────────────┬───────────┘

                       ▼

        ┌──────────────────────────┐

        │ Source Whitelisting      │

        └──────────────┬───────────┘

                       ▼

        ┌──────────────────────────┐

        │ PII Scrubbing            │

        └──────────────┬───────────┘

                       ▼

        ┌──────────────────────────┐

        │ Redundancy Filtering     │

        └──────────────┬───────────┘

                       ▼

        ┌──────────────────────────┐

        │ Context Window Control   │

        └──────────────┬───────────┘

                       ▼

               Clean Context

                       ▼

                      LLM
```

---

# 9. Combined LangChain Example

```python
retriever = vectorstore.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={
        "score_threshold":0.80,
        "k":5
    }
)

docs = retriever.invoke(
    "Explain Graph RAG"
)

trusted_sources = [
    "Company Wiki",
    "WHO"
]

filtered_docs = []

for doc in docs:

    if doc.metadata["source"] not in trusted_sources:
        continue

    if len(doc.page_content) == 0:
        continue

    filtered_docs.append(doc)

# Remove duplicate chunks
unique_docs = []

seen = set()

for doc in filtered_docs:

    if doc.page_content not in seen:

        unique_docs.append(doc)

        seen.add(doc.page_content)

print(unique_docs)
```

---

# 10. Key Takeaways

- **Retrieval-Level Guardrails** validate and clean the retrieved documents before they are passed to the LLM, improving both safety and answer quality.
- **Relevance Threshold** removes documents whose similarity score is below a predefined cutoff, ensuring that only highly relevant chunks are used.
- **Source Whitelisting** restricts retrieval to trusted document sources, reducing the risk of misinformation and improving reliability.
- **PII Scrubbing** detects and masks sensitive information (such as PAN numbers, Aadhaar numbers, or employee details) contained in retrieved documents.
- **Redundancy Filtering** removes duplicate or near-duplicate chunks, saving context window space and reducing unnecessary token usage.
- **Context Window Control** ensures that the retrieved context fits within the LLM's maximum context length by limiting or compressing the context.
- Together, these retrieval guardrails ensure that the LLM receives **clean, relevant, trusted, non-sensitive, and size-controlled context**, leading to more accurate and reliable responses.