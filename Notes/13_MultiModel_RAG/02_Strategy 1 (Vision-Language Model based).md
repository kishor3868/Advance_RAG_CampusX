# Multimodal RAG - Strategy 1 (Vision Language Model Based)

# Table of Contents

1. Introduction
2. Why do we need Strategy 1?
3. What is a Vision Language Model (VLM)?
4. Converting Images into Text using VLM
5. Image Chunk Generation
6. Metadata Handling
7. Embedding
8. Retrieval Pipeline
9. Response Generation using VLM
10. Complete Architecture
11. Bottlenecks of Strategy 1
12. LangChain Implementation
13. Key Takeaways

---

# 1. Introduction

In the previous section, we learned that Multimodal RAG introduces two new challenges:

- Representation Problem
- Retrieval Problem

The first strategy to solve these problems is called the **Vision Language Model (VLM) Based Strategy**.

Instead of directly embedding images, we first **convert images into detailed textual descriptions**. Once an image is represented as text, the remaining pipeline becomes almost identical to Traditional RAG.

This is one of the simplest and most widely used approaches for building Multimodal RAG systems.

---

# 2. Why do we need Strategy 1?

The biggest challenge is:

> Images are made up of pixels, whereas embedding models generally work with text.

For example,

```
Image

↓

?????

↓

Embedding
```

How do we generate an embedding from an image?

Instead of embedding the image directly, we ask a **Vision Language Model (VLM)** to describe the image.

Now the pipeline becomes:

```
Image

↓

Vision Language Model

↓

Detailed Text Description

↓

Text Embedding

↓

Vector Database
```

By converting the image into text, we eliminate the **Representation Problem**.

---

# 3. What is a Vision Language Model (VLM)?

A **Vision Language Model (VLM)** is an AI model capable of understanding both **images and text**.

It can:

- Describe an image
- Understand diagrams
- Explain charts
- Summarize tables
- Answer questions about images

Examples of Vision Language Models include:

- GPT-4o
- GPT-4.1 Vision
- Gemini Vision
- Claude Vision
- LLaVA

---

## Role of VLM in Strategy 1

The VLM acts as an **Image-to-Text Converter**.

Example:

Image

```
Architecture Diagram

User

↓

Retriever

↓

LLM

↓

Answer
```

VLM Output

```
The image illustrates a RAG architecture where the user query is passed to a retriever, which fetches relevant documents. The retrieved documents are then provided to an LLM to generate the final response.
```

Now this description can be embedded like any normal text.

---

# 4. Converting Images into Text using VLM

The complete process is:

```
Image

↓

Vision Language Model

↓

Detailed Description

↓

Text Chunk
```

The generated description should be detailed because:

- More details improve retrieval quality.
- Better descriptions lead to more meaningful embeddings.

---

### Example

Original Image

```
Bar Chart

Tesla Revenue

2022 : 80B

2023 : 95B
```

VLM Description

```
The chart compares Tesla's annual revenue for 2022 and 2023. Revenue increased from approximately 80 billion dollars in 2022 to about 95 billion dollars in 2023, indicating positive growth.
```

This text is then stored instead of the raw image.

---

# 5. Image Chunk Generation

After the VLM generates the description, each image is treated just like a text chunk.

Instead of storing the image directly, we create an **Image Chunk**.

Example

```
Image

↓

VLM

↓

Image Description

↓

Image Chunk
```

Now we have two types of chunks:

### Text Chunks

```
Page Content

Metadata
```

---

### Image Chunks

```
Image Description

Metadata
```

From this point onward, both chunks behave similarly.

---

# 6. Metadata Handling

Metadata is very important because we want to retrieve the **original image** later.

Each image chunk stores metadata such as:

```
ID

Source

Page Number

Image Path

Document Name
```

Example

```
Image Chunk

Description:

Tesla revenue increased in 2023.

Metadata

ID : img_12

Source : annual_report.pdf

Page : 8

Image Path : images/page8_chart.png
```

Notice that the **actual image is not stored inside the chunk**.

Only its **path/reference** is stored.

This allows us to display the original image during retrieval.

---

# 7. Embedding

Now both text chunks and image chunks are represented as text.

Therefore, the same text embedding model can be used.

Pipeline

```
Text Chunk

↓

Embedding Model

↓

Embedding Vector
```

```
Image Description

↓

Embedding Model

↓

Embedding Vector
```

Both vectors now lie in the same embedding space.

This completely solves the **Representation Problem**.

---

# Complete Ingestion Pipeline

```
PDF

│

├──────────────┐

▼              ▼

Text        Images

│              │

▼              ▼

Chunks      Vision Language Model

│              │

▼              ▼

Text      Image Description

└──────────────┘

        │

        ▼

Embedding Model

        │

        ▼

Vector Database
```

---

# 8. Retrieval Pipeline

Suppose the user asks:

```
Explain Tesla revenue growth shown in the report.
```

The retrieval process is:

```
User Query

↓

Text Embedding

↓

Similarity Search

↓

Retrieved Text Chunks

+

Retrieved Image Chunks

↓

Original Image Path Retrieved

↓

Vision Language Model

↓

Final Answer
```

Notice that during retrieval:

We retrieve

- Text
- Image Description
- Original Image

The metadata helps us locate the original image.

---

# 9. Response Generation using VLM

After retrieval, the final context consists of:

```
User Query

+

Retrieved Text

+

Retrieved Image
```

Everything is then passed to the Vision Language Model.

Architecture

```
User Query

↓

Retrieved Text

+

Retrieved Images

↓

Vision Language Model

↓

Final Response
```

The VLM can jointly understand:

- Text
- Image

Therefore, it produces much richer answers.

---

# Complete Retrieval Architecture

```
User Query

↓

Embedding

↓

Vector Database

↓

Retrieved Text

+

Retrieved Images

↓

Vision Language Model

↓

Answer
```

Observe that the only major difference from Traditional RAG is the use of the **Vision Language Model**.

Everything else remains almost the same.

---

# 10. Complete Architecture

```
Knowledge Source

(PDF)

│

├──────────────┐

▼              ▼

Text        Images

│              │

▼              ▼

Chunks      Vision Language Model

│              │

▼              ▼

Text      Image Description

└──────────────┘

        │

        ▼

Embedding Model

        │

        ▼

Vector Database

        │

        ▼

User Query

        │

        ▼

Similarity Search

        │

        ▼

Retrieved Text

+

Retrieved Images

        │

        ▼

Vision Language Model

        │

        ▼

Final Response
```

---

# 11. Bottlenecks of Strategy 1

Although this strategy is simple, it has several limitations.

## 1. VLM Accuracy

The quality of image descriptions depends entirely on the Vision Language Model.

A weak VLM may generate incomplete or incorrect descriptions.

---

## 2. Hallucination

Sometimes the VLM may describe objects that do not actually exist in the image.

This introduces incorrect information into the vector database.

---

## 3. Prompt Quality

The generated description depends heavily on the **System Prompt**.

Example

Weak Prompt

```
Describe this image.
```

Strong Prompt

```
Describe every object, relationship, text, chart, numbers, labels, colors, and overall meaning of the image in detail.
```

Better prompts lead to better retrieval quality.

---

## 4. Information Loss

Once the image is converted into text,

fine visual details may be lost.

For example,

small design differences or exact spatial layouts may not be fully captured.

---

# 12. LangChain Implementation

## Step 1 : Load PDF

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("report.pdf")

documents = loader.load()
```

---

## Step 2 : Extract Images

```python
loader = PyPDFLoader(
    "report.pdf",
    extract_images=True
)
```

---

## Step 3 : Send Image to VLM

Pseudo Workflow

```python
image

↓

Vision Language Model

↓

Detailed Description
```

Example Prompt

```python
prompt = """
Describe this image in complete detail.

Include:

Objects

Charts

Tables

Relationships

Numbers

Labels

Overall meaning
"""
```

---

## Step 4 : Create Image Document

```python
from langchain_core.documents import Document

image_doc = Document(
    page_content=image_description,
    metadata={
        "source":"report.pdf",
        "image_path":"images/chart1.png"
    }
)
```

---

## Step 5 : Embed Documents

```python
from langchain_openai import OpenAIEmbeddings

embeddings = OpenAIEmbeddings()

vector = embeddings.embed_documents(
    [image_doc.page_content]
)
```

---

## Step 6 : Store in Vector Database

```python
from langchain_chroma import Chroma

vectorstore = Chroma.from_documents(
    documents=[image_doc],
    embedding=embeddings
)
```

---

## Step 7 : Retrieval

```python
retriever = vectorstore.as_retriever()

docs = retriever.invoke(
    "Explain Tesla revenue growth."
)
```

Retrieved metadata provides the path to the original image, which can then be sent along with the retrieved text to the Vision Language Model for generating the final response.

---

# 13. Key Takeaways

- **Strategy 1** solves the Representation Problem by converting images into detailed textual descriptions using a **Vision Language Model (VLM)**.
- The generated image descriptions are treated as **image chunks**, similar to text chunks.
- Each image chunk stores **metadata** (such as image path and source) so the original image can be retrieved later.
- Since both text chunks and image chunks are now represented as text, the **same text embedding model** can generate embeddings for both.
- During retrieval, the system retrieves **text chunks**, **image descriptions**, and the **original image** (using metadata).
- A **Vision Language Model** combines the user query, retrieved text, and retrieved image to generate the final answer.
- This approach is simple and easy to implement, but its performance depends heavily on **VLM accuracy**, **prompt quality**, and it may suffer from **hallucinations** or loss of fine visual details.