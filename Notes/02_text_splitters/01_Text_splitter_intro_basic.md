## Introduction to Text Splitting

One of the MOST IMPORTANT steps in RAG systems is:

Text Splitting (Chunking)

After documents are loaded, we cannot directly send the entire document to the LLM.

### Why?

Because LLMs have limitations.

Example:

A PDF may contain:

500 pages
Thousands of words
Huge context

But LLMs cannot process infinite text.

So documents are divided into smaller pieces called:

Chunks

This process is called:

Text Splitting or Chunking
Real-World Analogy

Imagine preparing for an exam.

Would you study:

❌ Entire 1000-page book at once?

No.

You divide it into:

Chapters
Topics
Notes

Text splitting works similarly.

## What is a Text Splitter?

### Definition

Text Splitter (Chunking) in RAG

A Text Splitter is a component in a RAG pipeline that breaks large documents into smaller, manageable pieces called chunks before creating embeddings and storing them in a vector database.

The goal is to:

Fit content within the embedding model's token limits.
Improve retrieval accuracy by making chunks more focused and semantically meaningful.
Ensure only the most relevant portions of a document are retrieved for a user query.

## Why Do We Need Text Splitters?

Text splitters are needed because large documents cannot be processed effectively as a single unit. They break documents into smaller chunks before embedding and retrieval.

Key Reasons

### Embedding Model Context Limit

Embedding models have a maximum token limit.
If a document exceeds this limit, it cannot be embedded directly and must be split into smaller chunks.
### More Accurate Retrieval

Retrieving a small relevant chunk is much better than retrieving an entire 100-page document.
Smaller chunks improve similarity search precision.
### Avoid "Lost in the Middle" Problem

Important information located in the middle of a very large chunk may receive less attention from the LLM.
Smaller chunks increase the chance that relevant information is retrieved and noticed.
### Reduce Noise

Large chunks may contain a lot of irrelevant information.
Chunking helps retrieve only the relevant section needed to answer the query.

## What Happens Without Text Splitting?

Without chunking, the entire document is treated as a single piece of text for embedding, retrieval, and generation, which leads to several problems in a RAG system.

### Problem 1 — Token Limit Errors

Embedding models and LLMs have a maximum context window (token limit).

If a document is larger than this limit:

The model cannot process it completely.
Some content may be truncated.
Embedding generation may fail or lose information.
Example
Document Size = 20,000 tokens
Embedding Model Limit = 8,000 tokens

The entire document cannot be embedded at once.

### Problem 2 — Poor Retrieval

Without chunking, the entire document is converted into a single embedding.

As a result:

Multiple topics are represented by one vector.
Similarity search becomes less precise.
The retriever may return a large document even when only a small section is relevant.
Example

User asks:

What is the leave policy?

Retriever returns:

100-page Employee Handbook

instead of the specific leave policy section.

### Problem 3 — Semantic Mixing

Different topics get mixed together into a single embedding.

Example

One document contains:

HR Policy
Payroll Information
Leave Rules
Insurance Benefits

When embedded as one vector, the model creates a combined semantic representation of all topics.

Result:

Query about leave policy may retrieve payroll information.
Query about insurance may retrieve HR rules.
Retrieval relevance decreases.
### Problem 4 — Higher Cost

Large documents mean larger contexts.

This leads to:

More tokens processed
Higher API costs
Increased memory usage
Slower embedding generation
Slower inference and response time
Example
100-page document
↓
Sent to LLM
↓
High token consumption
↓
Higher cost
### Problem 5 — Lower Accuracy

Important information can become buried inside a huge document.

This causes:

Retrieval to miss critical sections
The LLM to overlook relevant information
Increased chances of incorrect answers

It also contributes to the "Lost in the Middle" problem, where information located in the middle of a long context receives less attention than information near the beginning or end.

Example
Page 1      → Introduction
Page 50     → Important Answer
Page 100    → Summary

The information on Page 50 may be less likely to influence the model compared to information near the start or end.

### Interview Answer

Without text splitting, documents may exceed embedding model token limits, retrieval becomes less accurate because entire documents are embedded as a single vector, different topics get semantically mixed together, API costs and latency increase due to larger contexts, and important information can get buried, leading to lower retrieval and generation accuracy.

## Why Text Splitting is Needed Even After PDF Loading 

### When using: PyPDFLoader() PDF already gets divided into pages. So students often ask: “Why do we still need text splitting?”

Because: A PDF page does NOT guarantee semantic meaning.
One page may contain:

Multiple topics
Headers
Tables
Mixed information

So page-based splitting alone is not sufficient.

## text Splitter Functionalities in LangChain

### 1. split_text()
What it does

split_text() takes a plain string (raw text) as input and splits it into smaller chunks based on the chunk size and overlap configuration.

Input
text = "Long text document..."
Output
[
  "Chunk 1",
  "Chunk 2",
  "Chunk 3"
]
Interview Answer

split_text() is used when we have raw text data and want to divide it into smaller text chunks. It returns a list of strings and does not preserve metadata.

### 2. split_documents()
What it does

split_documents() takes a list of LangChain Document objects and splits them into smaller Document chunks while preserving metadata such as source, page number, author, etc.

Input
[
  Document(
      page_content="Long content...",
      metadata={"source":"pdf"}
  )
]
Output
[
  Document(chunk1, metadata),
  Document(chunk2, metadata),
  Document(chunk3, metadata)
]
Interview Answer

split_documents() is used when documents are already loaded using loaders such as TextLoader, CSVLoader, or PyPDFLoader. It splits the content into smaller documents while retaining the original metadata.

### 3. create_documents()
What it does

create_documents() converts raw text directly into LangChain Document objects and optionally attaches metadata. It performs both document creation and chunking.

Input
[
  "Text 1",
  "Text 2"
]
Output
[
  Document(chunk1),
  Document(chunk2),
  Document(chunk3)
]
Interview Answer

create_documents() is used when we have raw text instead of Document objects. It creates LangChain Document objects and splits them into chunks, optionally attaching metadata.

### Quick Comparison

Function	Input	Output	Metadata Preserved
split_text()	Raw Text (String)	List of Strings	❌ No
split_documents()	List of Documents	List of Chunked Documents	✅ Yes
create_documents()	List of Text Strings	List of Documents	✅ Can Add Metadata
### Easy Way to Remember

split_text() → Text ➜ Text Chunks
split_documents() → Documents ➜ Chunked Documents
create_documents() → Text ➜ Documents + Chunks 

## Types of Text Splitting Strategies

There are mainly two major types.

Type	Description

Length-Based Splitter	Splits using fixed size
Structure-Based Splitter	Splits using text structure

### What are Length-Based Splitters?

Length-Based Splitters divide a document into smaller chunks based on a predefined length (number of characters or tokens) rather than the meaning of the text. They are used to ensure chunks fit within embedding model and LLM context limits, making storage, retrieval, and processing more efficient.

Common Length-Based Splitters

#### 1. CharacterTextSplitter

What: Splits text based on a fixed number of characters.

Why: Simple, fast, and useful when semantic boundaries are not critical.

How: Creates chunks using chunk_size and optionally preserves context using chunk_overlap.

Chunk Size = 1000 characters
Chunk Overlap = 200 characters

#### 2. RecursiveCharacterTextSplitter

What: An advanced splitter that tries multiple separators (\n\n, \n, space, etc.) before forcing a split.

Why: Preserves sentences and paragraphs better than fixed character splitting.

How: Recursively searches for the best place to split while staying within the chunk size limit.

Most commonly used splitter in RAG applications.

#### Token-Based Splitting

What is it?

Token-based splitting divides text according to the number of tokens rather than characters. Since LLMs and embedding models process tokens, this approach is more accurate than character-based splitting.

Why is it needed?

Different words consume different numbers of tokens. A chunk that looks small in characters may exceed the model's token limit.

How does it work?

The splitter uses the model's tokenizer (e.g., OpenAI tokenizer, tiktoken) to count tokens and create chunks that stay within the model's context window.

Chunk Size = 500 tokens
Chunk Overlap = 50 tokens
Benefits
Prevents token limit errors
Aligns directly with LLM processing
Produces more consistent chunk sizes
Preferred when working with specific embedding or chat models

#### Interview Answer

Length-Based Splitters divide documents based on size constraints to ensure they fit within model context limits. Common types include CharacterTextSplitter, which splits by character count, and RecursiveCharacterTextSplitter, which preserves document structure by splitting on paragraphs, lines, and words. Token-Based Splitters split using token counts instead of characters, making them more accurate because LLMs and embedding models operate on tokens rather than raw text.

## How is Chunk Size Decided?

Chunk size is chosen based on the embedding model's token limit, the nature of the data, and the amount of context needed to preserve meaning. There is no fixed ideal size; it is usually determined through experimentation and evaluation.

### Trade-off Between Chunk Size and Performance

Small Chunks (e.g., 100–300 tokens)

Advantages

More precise retrieval
Less irrelevant information
Better similarity matching

Disadvantages

Context may be lost
Information may be split across chunks
More chunks to store and search
Large Chunks (e.g., 1000–2000 tokens)

Advantages

More context preserved
Better for complex explanations and reasoning

Disadvantages

More irrelevant information (noise)
Lower retrieval precision
Higher token cost and increased "Lost in the Middle" risk
How to Choose a Chunk Size?
For FAQs / Short Documents
100–300 tokens
For General RAG Applications
500–1000 tokens
For Research Papers / Technical Documents
1000–1500 tokens
Importance of Chunk Overlap

Chunk overlap helps preserve context across chunks.

Example:

Chunk 1: ...Machine Learning is a subset...
Chunk 2: ...subset of Artificial Intelligence...

Without overlap, important context may be lost at chunk boundaries.

Typical overlap:

10%–20% of chunk size 

## Separators in Text Splitting

Separators determine:

Where text should split.
Examples
Separator	Meaning
"\n\n"	Paragraph
"\n"	Line
"."	Sentence
" "	Word 

## Why Do We Need Chunk Overlap?

Chunk Overlap means repeating a small portion of text from the end of one chunk at the beginning of the next chunk. It helps preserve context when information spans across chunk boundaries.

Example
Chunk 1:
Machine Learning is a subset of Artificial...

Chunk 2:
...Artificial Intelligence that enables systems...

Without overlap, the relationship between the two chunks may be lost.

Advantages of Chunk Overlap
1. Preserves Context

Important information split between two chunks remains connected.

2. Improves Retrieval Accuracy

The retriever is more likely to retrieve the complete context instead of a partial sentence or idea.

3. Reduces Boundary Problems

Prevents important sentences, paragraphs, or concepts from being cut off abruptly.

4. Better Answer Generation

The LLM receives more complete information, reducing misunderstandings and hallucinations.

5. Useful for Long Documents

Helps maintain continuity across sections in books, reports, research papers, and PDFs.

Disadvantages of Chunk Overlap
1. Increased Storage

Repeated text creates more chunks and larger vector databases.

2. Higher Embedding Cost

Overlapping content is embedded multiple times, increasing processing cost.

3. Slower Retrieval

More chunks need to be searched and ranked.

4. Duplicate Information

Retriever may return multiple chunks containing the same overlapping text.

5. More Token Usage

Repeated context increases the number of tokens sent to the LLM, increasing inference cost.

## Advantages and Disadvantages of Length-Based Splitters

### Advantages
1. Simple and Fast

Easy to implement and computationally efficient because splitting is based only on character or token count.

2. Predictable Chunk Sizes

Produces consistent chunk lengths, making embedding generation and vector storage easier to manage.

3. Prevents Token Limit Issues

Ensures chunks remain within the embedding model's and LLM's context window limits.

4. Works with Any Text

Can be applied to documents regardless of structure, format, or language.

### Disadvantages

1. May Break Semantic Meaning

Chunks can split in the middle of a sentence, paragraph, or concept, causing loss of context.

2. Lower Retrieval Quality

Important information may be distributed across multiple chunks, reducing retrieval accuracy.

3. Requires Chunk Overlap

Additional overlap is often needed to preserve context, increasing storage and cost.

4. Ignores Document Structure

Does not consider headings, sections, paragraphs, or topic boundaries when splitting.

