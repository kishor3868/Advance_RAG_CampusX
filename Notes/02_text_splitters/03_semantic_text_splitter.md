# Semantic Similarity Based Splitting in RAG — Detailed Notes

# Table of Contents

1. Introduction
2. Why Traditional Chunking is Not Enough
3. What is Semantic Similarity Based Splitting?
4. Understanding Semantic Similarity
5. Why Semantic Chunking is Needed
6. How Semantic Chunking Works
7. Role of Embedding Models
8. Cosine Similarity in Semantic Chunking
9. Threshold Concept in Semantic Splitting
10. Threshold Types and Threshold Amount
11. Architecture Flow of Semantic Chunking
12. Step-by-Step Example
13. LangChain Semantic Chunking
14. LangGraph and Semantic Chunking
15. Real-World Enterprise Examples
16. Advantages of Semantic Similarity Splitting
17. Disadvantages of Semantic Similarity Splitting
18. When to Use Semantic Chunking
19. When NOT to Use Semantic Chunking
20. Comparison with Other Splitters
21. Best Practices
22. Interview Questions & Answers
23. Key Takeaways

---

# 1. Introduction

In RAG systems,
one of the MOST IMPORTANT challenges is:

## How to create meaningful chunks?

Earlier chunking strategies used:

* Character count
* Token limits
* Paragraph splitting
* Recursive separators

But there is still a problem.

---

# Problem

Two consecutive paragraphs may:

❌ Have completely different meanings

while:

Two distant sentences may:

✅ Belong to the same topic.

Traditional chunking cannot fully understand meaning.

This leads to:

* Broken semantic relationships
* Poor retrieval quality
* Irrelevant embeddings

---

# Solution

# Semantic Similarity Based Splitting

This is one of the MOST ADVANCED chunking strategies in modern RAG systems.

---

# 2. Why Traditional Chunking is Not Enough

# Example Problem

Suppose we have this text:

```text
Artificial Intelligence is transforming healthcare.
Hospitals use AI for diagnosis.

The stock market crashed yesterday.
Investors are worried.
```

---

# Character-Based Splitter Problem

It may create:

Chunk 1:

```text
Artificial Intelligence is transforming healthcare.
Hospitals use AI for diagnosis.
The stock market crashed yesterday.
```

Chunk 2:

```text
Investors are worried.
```

---

# Problem Here

AI topic and stock market topic got mixed.

Embedding quality becomes poor.

Retriever may return irrelevant chunks.

---

# Semantic Chunking Solves This

It groups text according to:

# Meaning

not arbitrary size.

---

# 3. What is Semantic Similarity Based Splitting?

## Definition

Semantic chunking creates chunks based on the meaning and semantic similarity of the text rather than arbitrary size limits.

It starts with an initial chunk and keeps adding sentences as long as they remain semantically similar (measured by cosine similarity of embeddings).

When similarity drops below a threshold, a new chunk begins.

This is like grouping related topics in a conversation — you keep talking about one subject until the topic naturally shifts.

---

## Simple Definition

Semantic Chunking = Group Text Based on Meaning

---

# Core Idea

Instead of asking:

❌ “How many characters?”

it asks:

✅ “Do these sentences mean similar things?”

---

# Real-Life Conversation Analogy

Imagine two friends talking.

Conversation:

```text
AI models
Neural networks
Deep learning
Machine learning
```

All belong to same topic.

Then suddenly:

```text
Cricket world cup
```

Topic changes.

Semantic chunking detects this topic shift automatically.

---

# 4. Understanding Semantic Similarity

# What is Semantic Similarity?

Semantic similarity measures:

How similar the meanings of two texts are.

---

# Example

Sentence 1:

```text
AI is transforming healthcare.
```

Sentence 2:

```text
Hospitals are using machine learning.
```

Even though words differ,
meaning is related.

Semantic similarity score becomes high.

---

# Another Example

Sentence 1:

```text
AI is transforming healthcare.
```

Sentence 2:

```text
Football fans celebrated the victory.
```

Meanings unrelated.

Similarity score becomes low.

---

# 5. Why Semantic Chunking is Needed

## Problem 1 — Semantic Fragmentation

Traditional splitting may break related ideas.

---

## Problem 2 — Topic Mixing

Different topics may enter same chunk.

---

## Problem 3 — Poor Retrieval

Embeddings become noisy.

---

## Problem 4 — Lower RAG Accuracy

Retriever returns irrelevant information.

---

## Semantic Chunking Solves These

✅ Preserves topic continuity
✅ Creates cleaner embeddings
✅ Improves retrieval quality
✅ Better context preservation

---

# 6. How Semantic Chunking Works

## Step-by-Step Workflow

```text

Text
 ↓
Sentence Splitting
 ↓
Generate Embeddings
 ↓
Compute Similarity
 ↓
Compare with Threshold
 ↓
If Similar → Same Chunk
If Dissimilar → New Chunk
```

---

# Detailed Workflow

## Step 1 — Split into Sentences

Example:

```text
Sentence 1
Sentence 2
Sentence 3
```

---

## Step 2 — Generate Embeddings

Each sentence converted into vector.

```text
Sentence → Embedding Vector
```

---

## Step 3 — Compute Cosine Similarity

Compare neighboring sentence embeddings.

---

## Step 4 — Threshold Check

If similarity:

```text
> threshold
```

Add to current chunk.

Else:

Start new chunk.

---

# 7. Role of Embedding Models

## MOST IMPORTANT CONCEPT

Semantic chunking heavily depends on:
Embedding Models

---

## Why Embeddings?

Computers cannot directly understand meaning.

Embeddings convert text into:
Numerical semantic vectors

---

### Example

```text
"AI in healthcare"
↓
[0.23, -0.55, 0.91, ...]
```

---

### Similar Meaning → Similar Vectors

Example:

```text
AI in hospitals
Machine learning in healthcare
```

Vectors become close.

---

### Embedding Models Used

| Embedding Model        |
| ---------------------- |
| OpenAI Embeddings      |
| HuggingFace Embeddings |
| Sentence Transformers  |
| BGE Embeddings         |
| Instructor Embeddings  |

---

### 8. Cosine Similarity in Semantic Chunking

### What is Cosine Similarity?
Cosine similarity measures:
Angle similarity between vectors.

---
Output Range

```text
-1 to 1
```

--- Meaning

| Score | Meaning        |
| ----- | -------------- |
| 1     | Highly similar |
| 0     | Unrelated      |
| -1    | Opposite       |

---
 Example

```text
AI and ML → 0.92
AI and football → 0.10
```

---
 Visualization

```text
High Similarity
Vector A → ↗
Vector B → ↗

Low Similarity
Vector A → ↗
Vector B → ↓
```

---

## 9. Threshold Concept in Semantic Splitting

### What is Threshold?

Threshold determines:
When to start a new chunk.

---
Example

```text
Threshold = 0.75
```

If similarity:

```text
0.80 → Same chunk
0.40 → New chunk
```

---
Why Threshold is Important

Threshold controls:

* Chunk quality
* Chunk size
* Topic purity
* Retrieval precision

---

### 10. Threshold Types and Threshold Amount

Different threshold strategies exist.

---
### Fixed Threshold

Example:

```python
threshold = 0.75
```

Simple rule.

---

### Dynamic Threshold

Threshold changes depending on:

* Document type
* Similarity distribution
* Chunk size

---

### High Threshold

Example:

```text
0.90
```

Meaning:

Very strict semantic grouping.

---
Result

✅ Very pure chunks
❌ More fragmented chunks

---
Low Threshold

Example:

```text
0.50
```

Meaning:

More relaxed grouping.

---
Result

✅ Larger chunks
❌ Topic mixing risk

---

### Threshold Experimentation

VERY IMPORTANT IN REAL PROJECTS.

Different datasets require different thresholds.

---
 Example

| Domain         | Suggested Threshold |
| -------------- | ------------------- |
| Legal Docs     | Higher              |
| Casual Blogs   | Medium              |
| Technical Docs | Higher              |
| News Articles  | Medium              |

---

# 11. Architecture Flow of Semantic Chunking

```text
                 ┌──────────────────┐
                 │ Raw Text         │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Sentence Split   │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Embedding Model  │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Embedding Vectors│
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Cosine Similarity│
                 └────────┬─────────┘
                          │
          ┌───────────────┴───────────────┐
          │                               │
          ▼                               ▼
 Similarity High                  Similarity Low
          │                               │
          ▼                               ▼
 Same Chunk                      New Chunk
```

---

## 12. Step-by-Step Example

### Input Text

```text
AI is transforming healthcare.
Hospitals use machine learning.
Doctors use predictive analytics.

The stock market crashed today.
Investors are worried.
```

---

## Step 1 — Sentence Embeddings

Generate embeddings for each sentence.

---

### Step 2 — Similarity Calculation

| Sentence Pair | Similarity |
| ------------- | ---------- |
| S1 ↔ S2       | 0.91       |
| S2 ↔ S3       | 0.88       |
| S3 ↔ S4       | 0.22       |

---
 Step 3 — Threshold Check

Threshold:

```text
0.75
```

---

Result

Chunk 1:

```text
AI is transforming healthcare.
Hospitals use machine learning.
Doctors use predictive analytics.
```

Chunk 2:

```text
The stock market crashed today.
Investors are worried.
```

---

# 13. LangChain Semantic Chunking

LangChain supports semantic chunking.

---

## Example Setup

```python
from langchain_experimental.text_splitter import SemanticChunker
from langchain_openai.embeddings import OpenAIEmbeddings
```

---

### Create Embedding Model

```python
embeddings = OpenAIEmbeddings()
```

---

### Create Semantic Splitter

```python
text_splitter = SemanticChunker(
    embeddings,
    breakpoint_threshold_type="percentile"
)
```

---

### Split Text

```python
docs = text_splitter.create_documents([text])
```

---

### Important Parameters

| Parameter                   | Meaning            |
| --------------------------- | ------------------ |
| embeddings                  | Embedding model    |
| breakpoint_threshold_type   | Threshold strategy |
| breakpoint_threshold_amount | Threshold value    |

---

### 14. Threshold Types in LangChain

#### Percentile Threshold

Most common.

Example:

```python
breakpoint_threshold_type="percentile"
```

---

#### Standard Deviation

Uses statistical deviation.

---

#### Interquartile

Uses quartile distribution.

---

#### Example with Threshold Amount

```python
text_splitter = SemanticChunker(
    embeddings,
    breakpoint_threshold_type="percentile",
    breakpoint_threshold_amount=90
)
```

---

#### Meaning

Chunk break occurs at:

Top 10% similarity changes.

---

#### Experimentation Strategy

VERY IMPORTANT.

Try:

* Different thresholds
* Different embeddings
* Different chunk sizes

Then evaluate:

* Retrieval accuracy
* Chunk coherence
* LLM response quality

---

# 15. LangGraph and Semantic Chunking

LangGraph itself does not directly perform chunking.

But semantic chunking is heavily used in:

## LangGraph-based RAG Architectures

---

### Example Workflow

```text
Document Loader
      ↓
Semantic Chunker
      ↓
Embeddings
      ↓
Vector Database
      ↓
Retriever Node
      ↓
LLM Node
```

---

### Why Useful in LangGraph?

LangGraph workflows require:

✅ Better retrieval precision
✅ Context-aware memory
✅ Multi-agent retrieval

Semantic chunking improves these dramatically.

---

# 16. Real-World Enterprise Examples

## Example 1 — Legal AI Assistant

Legal documents contain:

* Long clauses
* Topic continuity
* Semantically dense content

Semantic chunking preserves legal meaning.

---

## Example 2 — Medical Research RAG

Research papers contain:

* Related methodologies
* Experimental discussions
* Conclusions

Semantic chunking groups related scientific ideas.

---

## Example 3 — Enterprise Wiki Search

Internal documentation contains:

* Mixed topics
* Long explanations

Semantic chunking improves search relevance.

---

## Example 4 — AI Tutoring Systems

Educational content contains:

* Concept continuity
* Related explanations

Semantic chunking preserves learning flow.

---

# 17. Advantages of Semantic Similarity Splitting

## 1. Better Semantic Coherence

Chunks contain related ideas.

---

## 2. Better Retrieval Quality

Embeddings become cleaner.

---

## 3. Reduces Topic Mixing

Different subjects separate naturally.

---

## 4. More Human-Like Chunking

Mimics natural topic transitions.

---

## 5. Better RAG Accuracy

Retriever returns highly relevant chunks.

---

# 18. Disadvantages of Semantic Similarity Splitting

## 1. Computationally Expensive

Requires embeddings for many sentences.

---

## 2. Slower Processing

Similarity calculations increase latency.

---

## 3. Threshold Tuning Complexity

Requires experimentation.

---

## 4. Embedding Dependency

Quality depends heavily on embedding model.

---

## 5. Harder to Scale

Very large corpora increase computation.

---

## 19. When to Use Semantic Chunking

# Best Use Cases

| Use Case                   | Why                       |
| -------------------------- | ------------------------- |
| Legal documents            | Preserve semantic clauses |
| Research papers            | Topic continuity          |
| Medical documents          | Context preservation      |
| Enterprise knowledge bases | Better retrieval          |
| AI tutoring systems        | Concept grouping          |
| Long-form content          | Natural topic transitions |

---

## Use When

✅ Semantic continuity matters
✅ Retrieval quality is critical
✅ Topics shift gradually
✅ Documents are complex

---

# 20. When NOT to Use Semantic Chunking

## Avoid When

❌ Data is very small
❌ Simple character splitting is sufficient
❌ Real-time latency is critical
❌ Huge-scale processing with low compute budget
❌ Structured code splitting required

---

## Example

For:

* Source code
* JSON hierarchy

Use structure-aware splitting instead.

---

# 21. Comparison with Other Splitters

| Splitter                 | Based On            | Best For     |
| ------------------------ | ------------------- | ------------ |
| Character Splitter       | Character count     | Simple text  |
| Recursive Splitter       | Paragraph hierarchy | General text |
| Structure-Based Splitter | Document structure  | Code/HTML    |
| Semantic Splitter        | Meaning similarity  | Advanced RAG |

---

## 22. Best Practices

## Recommended Strategy

Use semantic chunking when:

* Accuracy matters more than speed
* Complex documents exist
* Topic continuity is important

---

## Important Tip

Experiment with:

* Threshold amount
* Embedding model
* Chunk size
* Similarity strategy

There is NO universal best threshold.

---

# 23. Interview Questions & Answers

# Q1. What is semantic chunking?

Semantic chunking creates chunks based on meaning similarity rather than fixed character or token limits.

---

# Q2. Why is semantic chunking needed?

Because traditional chunking may mix unrelated topics or break semantically related content.

---

# Q3. How does semantic chunking work?

It generates embeddings for sentences, computes cosine similarity, and groups semantically similar sentences together.

---

# Q4. What role do embeddings play?

Embeddings represent semantic meaning numerically, enabling similarity comparison.

---

# Q5. What is threshold in semantic chunking?

Threshold determines when semantic similarity is low enough to start a new chunk.

---

# Q6. What are advantages of semantic chunking?

Better semantic coherence, improved retrieval quality, and reduced topic mixing.

---

# Q7. What are disadvantages?

Higher computational cost, slower processing, and threshold tuning complexity.

---

# Q8. When should semantic chunking be used?

For complex, long-form documents where semantic continuity is important.

---

# 24. Key Takeaways

# Important Concepts

✅ Semantic chunking groups text based on meaning

✅ Uses embeddings and cosine similarity

✅ Threshold controls chunk boundaries

✅ Better than arbitrary chunking for complex documents

✅ Improves retrieval quality in RAG systems

✅ Computationally more expensive

✅ Widely used in advanced enterprise RAG systems

---

# Final Summary

Semantic similarity based splitting is one of the MOST ADVANCED chunking techniques in modern Retrieval-Augmented Generation systems.

Unlike traditional chunking methods,
it understands:

# Semantic Meaning

rather than:

* Character count
* Token size
* Simple separators

By using:

* Embedding models
* Cosine similarity
* Threshold-based grouping

semantic chunking creates:

# High-quality, semantically coherent chunks

which significantly improve:

* Retrieval precision
* Embedding quality
* LLM response accuracy
* Enterprise RAG performance
 