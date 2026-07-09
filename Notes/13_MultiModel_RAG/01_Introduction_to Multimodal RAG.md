# Introduction to Multimodal RAG

# Table of Contents

1. Introduction
2. What is Multimodal RAG?
3. Input and Output of Multimodal RAG
4. Traditional (Naive) RAG vs Multimodal RAG
5. Why OCR-based RAG is Not Enough?
6. Document Loading in Multimodal RAG
7. Text Chunks and Image Chunks
8. Representation Problem
9. Retrieval Problem
10. Overall Multimodal RAG Architecture
11. Key Takeaways

---

# 1. Introduction

So far, we have studied **Traditional (Naive) RAG**, where our knowledge source mainly consists of **text documents**. The retriever extracts text from documents, converts it into embeddings, stores it in a vector database, and retrieves the most relevant text chunks to answer a user's query.

However, in real-world documents such as:

- Research Papers
- Annual Reports
- Medical Reports
- User Manuals
- PPTs
- Technical Documentation

important information is often present in **images, diagrams, charts, tables, graphs, and flowcharts**.

Traditional RAG cannot understand these visual elements properly.

This limitation leads to the concept of **Multimodal RAG**.

---

# 2. What is Multimodal RAG?

## Definition

**Multimodal RAG (Multimodal Retrieval-Augmented Generation)** is an advanced RAG architecture that can retrieve and understand information from **multiple data modalities**, such as **text and images**, and use both to generate a final response.

Unlike Traditional RAG, which only retrieves text, Multimodal RAG retrieves both textual and visual information.

---

## Simple Definition

> Multimodal RAG is a RAG system that can retrieve information from both **text** and **images** and use both to answer the user's question.

---

# 3. Inputs and Outputs of Multimodal RAG

## Input

Unlike Traditional RAG, the input is not limited to text.

The knowledge source can contain:

- PDF Documents
- Markdown (.md)
- Python Files (.py)
- HTML
- Images
- PPT
- Word Documents
- Technical Manuals

These documents may contain both textual and visual information.

---

## Output

The output of Multimodal RAG is generated using both:

- Retrieved Text
- Retrieved Images

The final response is therefore richer and more accurate than Traditional RAG.

---

## Input → Output Flow

```
Knowledge Source

(PDF, Images, Markdown, Python, etc.)

              │

              ▼

      Multimodal RAG

              │

              ▼

Retrieved Text

+

Retrieved Images

              │

              ▼

      Final Response
```

---

# 4. Traditional (Naive) RAG vs Multimodal RAG

## Traditional RAG

Traditional RAG works mainly with text.

```
PDF

↓

Extract Text

↓

Chunk Text

↓

Embedding

↓

Vector Database

↓

Retrieve Text

↓

LLM

↓

Answer
```

Only textual information is used.

---

## Multimodal RAG

Multimodal RAG processes both text and images.

```
PDF

↓

Extract Text

+

Extract Images

↓

Text Chunks

+

Image Chunks

↓

Embeddings

↓

Vector Database

↓

Retrieve Text

+

Retrieve Images

↓

Vision Language Model (VLM)

↓

Answer
```

---

## Comparison

| Traditional RAG | Multimodal RAG |
|-----------------|----------------|
| Works only with text | Works with text and images |
| Ignores visual information | Uses visual information |
| Text Embeddings | Text + Image Embeddings (or Image Descriptions) |
| Text Retrieval | Text + Image Retrieval |
| LLM | Vision Language Model (VLM) |

---

# 5. Why OCR-based RAG is Not Enough?

A common question is:

> Earlier, we were already extracting text and images from PDFs using OCR. Then why do we need Multimodal RAG?

---

## OCR Approach

OCR (Optical Character Recognition) converts images into text.

Example:

```
Image

↓

OCR

↓

Extracted Text
```

This works well for:

- Printed Text
- Scanned Documents

---

### Problem

OCR only extracts visible text.

It cannot understand:

- Charts
- Flowcharts
- Diagrams
- Graphs
- Tables
- Images
- Complex Visual Relationships

---

### Example

Suppose a PDF contains this architecture diagram:

```
User

↓

Retriever

↓

LLM

↓

Answer
```

OCR may only extract:

```
User

Retriever

LLM

Answer
```

The relationship between these components is lost.

---

Another example:

A bar chart showing company sales.

OCR may extract:

```
2022

2023

Revenue
```

But it cannot infer:

- Which bar is higher?
- Growth trend
- Comparison between years

---

## Why Multimodal RAG?

Instead of extracting only text,

Multimodal RAG also understands the image itself.

Therefore,

it preserves both:

- Textual Meaning
- Visual Meaning

---

# 6. Document Loading in Multimodal RAG

The first step is exactly the same as Traditional RAG.

```
Knowledge Source

↓

Document Loader
```

However,

instead of extracting only text,

the loader extracts:

```
1. Text

2. Images
```

---

## Example

A PDF contains:

- Paragraphs
- Architecture Diagram
- Flowchart
- Table

The loader produces:

```
Text

Image 1

Image 2

Image 3
```

---

# 7. Text Chunks and Image Chunks

After loading,

the document is divided into two separate streams.

---

## Text Stream

```
PDF

↓

Text

↓

Chunking

↓

Text Chunks
```

Each text chunk becomes a LangChain **Document Object** containing:

- page_content
- metadata

Example

```
Document

page_content

metadata
```

---

## Image Stream

Images are extracted separately.

```
PDF

↓

Images

↓

Image Objects
```

Each image also contains metadata.

Example

```
Image

Metadata

Source

Page Number

Image Path
```

Notice:

Unlike Traditional RAG,

we now have two independent document collections:

- Text Chunks
- Image Chunks

---

# 8. Representation Problem

This is the first major challenge in Multimodal RAG.

---

## The Problem

Text is easy to embed.

```
Text

↓

Embedding Model

↓

Vector
```

But images are made of:

- Pixels
- Binary Data

How can we convert an image into a meaningful embedding?

This challenge is called:

# Representation Problem

---

## Representation Problem Definition

> How can visual information (images) be represented in a form that an embedding model can understand and store for retrieval?

This problem is addressed later using:

- Vision Language Models (VLMs)
- Multimodal Embedding Models

---

# 9. Retrieval Problem

Even if we somehow create image embeddings,

another challenge appears.

Suppose the user asks:

```
Explain Tesla architecture.
```

The query is text.

Therefore,

```
Query

↓

Text Embedding
```

But our database also contains image embeddings.

Question:

How do we compare:

```
Text Embedding

with

Image Embedding ?
```

This challenge is called:

# Retrieval Problem

---

## Retrieval Problem Definition

> How can a text query retrieve relevant images (or vice versa) from a multimodal knowledge base?

This is one of the biggest challenges solved by Multimodal RAG strategies.

---

# 10. Overall Multimodal RAG Architecture

```
                Knowledge Source

          (PDF / Images / Markdown)

                      │

                      ▼

              Document Loader

             ┌────────┴────────┐

             ▼                 ▼

        Text Content       Images

             ▼                 ▼

       Text Chunking     Image Extraction

             ▼                 ▼

        Text Chunks      Image Chunks

             │                 │

             └────────┬────────┘

                      ▼

              Embedding Pipeline

                      ▼

              Vector Database

                      ▼

               User Text Query

                      ▼

              Similarity Search

                      ▼

      Retrieved Text + Retrieved Images

                      ▼

         Vision Language Model (VLM)

                      ▼

                Final Response
```

---

# 11. Key Takeaways

- **Multimodal RAG** extends Traditional RAG by supporting multiple data modalities such as **text and images**.
- The input can be PDFs, Markdown files, Python files, HTML, images, and other document formats containing both textual and visual information.
- Unlike Traditional RAG, Multimodal RAG extracts **text chunks** and **image chunks** separately.
- OCR-based RAG only extracts visible text and cannot understand the meaning of diagrams, charts, flowcharts, or images.
- Multimodal RAG preserves both **textual information** and **visual information**, leading to richer and more accurate responses.
- Two key challenges arise:
  1. **Representation Problem** – How to represent images in an embedding space.
  2. **Retrieval Problem** – How a text query can retrieve relevant images (or vice versa).
- These challenges are solved using **Vision Language Models (VLMs)** or **Multimodal Embedding Models**, which will be discussed in the next section.