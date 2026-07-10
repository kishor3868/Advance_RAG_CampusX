# CLIP (Contrastive Language–Image Pre-training)

# Table of Contents

1. Introduction
2. Why do we need CLIP?
3. What is CLIP?
4. How CLIP is Different from Previous Strategies?
5. Specialty of CLIP
6. How CLIP Solves Representation and Retrieval Problems
7. How CLIP Works
8. CLIP Training (Contrastive Learning)
9. Attractive and Repulsive Learning
10. Why Attractive Learning Alone is Not Enough?
11. Representational Collapse
12. Similarity Matrix
13. CLIP in Multimodal RAG
14. LangChain Implementation
15. Key Considerations while Building Multimodal RAG
16. Advantages and Disadvantages
17. Interview Questions
18. Key Takeaways

---

# 1. Introduction

In the previous sections, we discussed two approaches for building Multimodal RAG.

## Strategy 1

Convert image → Text using Vision Language Model (VLM)

```
Image

↓

Vision LLM

↓

Image Description

↓

Text Embedding
```

---

## Strategy 2

Generate embeddings directly for

- Text
- Images

using a Multimodal Embedding Model.

One of the most popular multimodal embedding models is

# CLIP

(CLIP = Contrastive Language–Image Pre-training)

It was introduced by **OpenAI**.

---

# 2. Why do we need CLIP?

Earlier we faced two major problems.

## Problem 1 : Representation Problem

Images are made up of pixels.

Text is made up of words.

How can we represent both in a common format?

---

## Problem 2 : Retrieval Problem

Suppose the user asks

```
Show me Tesla revenue chart.
```

The query is text.

The stored data is an image.

How can a text query retrieve an image?

Traditional embedding models cannot solve this problem.

---

CLIP solves both problems simultaneously.

---

# 3. What is CLIP?

## Definition

**CLIP (Contrastive Language–Image Pre-training)** is a multimodal embedding model that learns a **shared embedding space** for both images and text.

Both image embeddings and text embeddings lie in the same semantic vector space.

---

## Simple Definition

> CLIP converts both images and text into the same embedding language (shared embedding space), allowing text queries to retrieve images directly.

---

# 4. How is CLIP Different from Previous Strategies?

## Strategy 1

```
Image

↓

Vision LLM

↓

Description

↓

Embedding
```

Image is converted into text.

Information may be lost.

---

## Strategy 2

```
Image

↓

Embedding Model

↓

Vector
```

```
Text

↓

Embedding Model

↓

Vector
```

Problem

Different embedding models may produce different vector spaces.

---

## CLIP

```
Image

↓

CLIP Image Encoder

↓

Shared Embedding Space

Text

↓

CLIP Text Encoder

↓

Shared Embedding Space
```

Both encoders are trained together.

Therefore,

their vectors become directly comparable.

---

# 5. Specialty of CLIP

The biggest specialty of CLIP is:

> It converts both images and text into the same embedding language.

This means

```
Text

↓

Vector
```

and

```
Image

↓

Vector
```

are represented in the **same semantic space**.

Because of this,

a text query can retrieve an image,

and an image can retrieve related text.

---

# 6. How CLIP Solves Earlier Problems

## Representation Problem

Earlier

```
Image

↓

?????

↓

Embedding
```

CLIP introduces

```
Image

↓

CLIP Image Encoder

↓

Embedding
```

Representation Problem solved.

---

## Retrieval Problem

Earlier

```
Text Embedding

×

Image Embedding
```

could not be compared.

CLIP produces

```
Text Embedding

↓

Shared Embedding Space

↓

Image Embedding
```

Now,

Cosine Similarity works perfectly.

Retrieval Problem solved.

---

# 7. How CLIP Works

Suppose we have a PDF page containing a revenue chart.

---

## During Ingestion

```
PDF

↓

Extract Image

↓

CLIP Image Encoder

↓

Embedding Vector

↓

Vector Database

+

Image Path
```

Example

```
Revenue Chart

↓

CLIP Image Encoder

↓

[0.32,0.41,0.91...]

↓

Stored in Vector DB

Metadata

Source

Page Number

Image Path
```

---

## During Query

User asks

```
What were the Q3 revenue trends?
```

Pipeline

```
User Query

↓

CLIP Text Encoder

↓

Query Vector

↓

Cosine Similarity

↓

Retrieve Revenue Chart

↓

Image

+

Query

↓

Vision Language Model

↓

Final Answer
```

Notice

The image is retrieved

without converting it into text.

---

# Complete Retrieval Pipeline

```
PDF

↓

Images

↓

CLIP Image Encoder

↓

Vector Database

------------------------

User Query

↓

CLIP Text Encoder

↓

Similarity Search

↓

Retrieved Image

↓

Vision Language Model

↓

Final Answer
```

---

# 8. How is CLIP Trained?

CLIP is trained using

```
Image

+

Caption
```

pairs.

Example

```
Image

Dog Playing

Caption

"A dog playing with a ball."
```

The model learns

```
Image

⇄

Caption
```

should represent the same meaning.

---

# 9. Contrastive Learning

CLIP is trained using

# Contrastive Learning

Contrastive Learning tries to

Bring similar pairs closer

and

Push dissimilar pairs farther apart.

There are two learning objectives.

---

## Attractive Learning

Similar image-caption pairs

should move closer.

Example

```
Image A

Dog

Caption A

A dog playing

↓

Should be close
```

---

## Repulsive Learning

Dissimilar pairs

should move apart.

Example

```
Image A

Dog

Caption B

A car

↓

Should be far apart
```

---

Visualization

```
Image A

●

Caption A

●

(close)

----------------------------

Image B

●

Caption B

●

(close)

----------------------------

Image A

×

Caption B

(far)

----------------------------

Image B

×

Caption A

(far)
```

This simultaneous attraction and repulsion is called

# Contrastive Learning

---

# 10. Why Attractive Learning Alone is Not Enough?

Suppose we only use Attractive Learning.

The model may "cheat."

Example

```
Image A

↓

Vector

[1,1,1]

Caption A

↓

Vector

[1,1,1]

Image B

↓

Vector

[1,1,1]

Caption B

↓

Vector

[1,1,1]
```

Everything gets exactly the same vector.

Similarity becomes

100%.

The loss becomes very small.

Training appears successful.

But...

the model has learned nothing.

---

# Representational Collapse

This situation is called

# Representational Collapse

Definition

> Representational Collapse occurs when the model maps every input to nearly the same embedding vector, preventing it from learning meaningful differences.

---

# Solution

Introduce

Repulsive Learning.

Now

```
Dog Image

↓

Dog Caption

↓

Close
```

but

```
Dog Image

↓

Car Caption

↓

Far
```

The model is forced to learn

actual semantic meaning.

---

# 11. Similarity Matrix

During training,

suppose we have

2 Images

2 Captions.

```
Image 1

Image 2

Caption 1

Caption 2
```

The model computes

Cosine Similarity

between every image and every caption.

Similarity Matrix

|            | Caption 1 | Caption 2 |
|------------|-----------|-----------|
| Image 1 | 0.95 | 0.12 |
| Image 2 | 0.10 | 0.92 |

Notice

Correct pairs have

high similarity.

Incorrect pairs have

low similarity.

The model continuously optimizes this matrix during training.

---

# Why is it Called Contrastive Learning?

Because the model learns by

**Contrasting**

positive pairs

against

negative pairs.

Positive Pair

```
Dog Image

Dog Caption
```

Negative Pair

```
Dog Image

Car Caption
```

The objective is

```
Positive

↓

Close

Negative

↓

Far
```

Hence the name

Contrastive Learning.

---

# 12. CLIP in Multimodal RAG

Complete Architecture

```
Knowledge Source

(PDF)

│

├───────────────┐

▼               ▼

Text          Images

│               │

▼               ▼

CLIP Text      CLIP Image

Encoder         Encoder

│               │

└──────┬────────┘

       ▼

Shared Embedding Space

       ▼

Vector Database

---------------------------

User Query

↓

CLIP Text Encoder

↓

Query Embedding

↓

Similarity Search

↓

Retrieved Images

+

Retrieved Text

↓

Vision Language Model

↓

Final Answer
```

Notice

CLIP handles

retrieval.

VLM handles

reasoning and answer generation.

---

# 13. LangChain Implementation (Conceptual)

Most LangChain pipelines treat CLIP as the embedding model.

Example (conceptual)

```python
from langchain_chroma import Chroma

# CLIP embedding model (provider-specific wrapper)
embedding_model = clip_embedding_model

vectorstore = Chroma.from_documents(
    documents=documents,
    embedding=embedding_model
)

retriever = vectorstore.as_retriever()

results = retriever.invoke(
    "Show Tesla revenue chart."
)
```

The retrieved metadata contains

```
Image Path

Source

Page Number
```

These retrieved images,

along with the query,

are then passed to a Vision Language Model.

---

# 14. Key Considerations while Building Multimodal RAG

## 1. Do not rely only on OCR

OCR extracts text,

but not visual meaning.

Always preserve original images.

---

## 2. Use VLM for Final Generation

Final input should be

```
User Query

+

Retrieved Text

+

Retrieved Images
```

↓

Vision Language Model

↓

Answer

---

## 3. Store Image Metadata

Store

- Image Path
- Source
- Page Number
- Image ID

This allows the original image to be retrieved.

---

## 4. Generate Embeddings Carefully

```
Text

↓

Text Encoder
```

```
Images

↓

Image Encoder
```

or

```
CLIP

↓

Shared Embedding Space
```

---

## 5. Handle Tables Carefully

Many PDFs store tables as images.

If a table is embedded only as an image,

important numerical information may be missed.

Good practice:

- Preserve original table image.
- Retrieve the table image.
- Let the Vision Language Model analyze it during response generation.

---

# 15. Advantages

- Solves Representation Problem.
- Solves Retrieval Problem.
- Supports Text ↔ Image retrieval.
- Better semantic alignment.
- No need to describe every image manually.
- Higher retrieval accuracy.

---

# 16. Disadvantages

- Requires pretrained CLIP model.
- Computationally expensive.
- Depends on embedding quality.
- Complex images may still require Vision LLM reasoning.
- Tables and dense charts may need additional processing.

---

# 17. Interview Questions

### Q1. What is CLIP?

CLIP is a multimodal embedding model that maps images and text into a shared embedding space using contrastive learning.

---

### Q2. What is the biggest advantage of CLIP?

Text and images become directly comparable.

---

### Q3. Why is CLIP different from Strategy 1?

Strategy 1 converts images into text first.

CLIP directly embeds images without converting them into text.

---

### Q4. What problems does CLIP solve?

- Representation Problem
- Retrieval Problem

---

### Q5. Why is CLIP trained using Contrastive Learning?

To pull similar image-text pairs together while pushing unrelated pairs apart.

---

### Q6. What is Representational Collapse?

A situation where every input receives nearly the same embedding vector, causing the model to stop learning meaningful distinctions.

---

# 18. Key Takeaways

- **CLIP (Contrastive Language–Image Pre-training)** is a multimodal embedding model that learns a **shared embedding space** for both text and images.
- It is trained on **image-caption pairs** using **contrastive learning**, where similar pairs are pulled closer (**attractive learning**) and dissimilar pairs are pushed farther apart (**repulsive learning**).
- Using only attractive learning can lead to **representational collapse**, where all embeddings become similar and the model fails to learn useful representations.
- CLIP solves the two major Multimodal RAG challenges:
  - **Representation Problem** by embedding images directly.
  - **Retrieval Problem** by placing text and image embeddings in the same semantic space.
- During **ingestion**, image embeddings (along with metadata such as image path) are stored in the vector database.
- During **retrieval**, a text query is encoded using the CLIP text encoder, similarity search retrieves the most relevant images, and the retrieved images plus text are passed to a **Vision Language Model (VLM)** for final reasoning and response generation.
- In practical Multimodal RAG systems, **CLIP is responsible for retrieval**, while the **Vision Language Model is responsible for understanding and generating the final answer**.