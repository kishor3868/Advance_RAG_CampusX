## Introduction

In RAG systems, text splitting is one of the MOST IMPORTANT stages.

But not all documents are simple plain text.

Modern enterprise systems contain:

Source code
JSON files
HTML pages
Markdown documentation
Configuration files
XML
YAML
APIs

These documents have:

Structural Meaning

Example:

A Python file contains:

Functions
Classes
Methods
Imports

Similarly:

A JSON file contains:

Nested objects
Key-value pairs
Arrays

A Markdown file contains:

Headings
Sections
Bullet points

If we split them blindly using character count:

❌ Semantic meaning breaks ❌ Code logic breaks ❌ Retrieval quality becomes poor

## This is why we use: Document Structure-Based Splitting

## What is Document Structure-Based Splitting?

### Definition

Document Structure-Based Splitting is a chunking technique that splits a document according to its natural structure, such as headings, sections, chapters, paragraphs, markdown headers, HTML tags, or document titles, instead of splitting by a fixed number of characters or tokens.

Instead of splitting by:

Character count
Token count

we split using:

Programming structures
JSON hierarchy
HTML tags
Markdown headings
XML nodes

### Purpose

The purpose is to keep related information together so that each chunk represents a complete topic or section. This helps the retriever and LLM understand the content more accurately.

### Why is Document Structure-Based Splitting Needed?

In real documents, information is already organized into sections.

For example:

Employee Handbook
 ├── Leave Policy
 ├── Payroll Policy
 ├── Insurance Policy
 └── Code of Conduct

If we use only length-based splitting, one chunk might contain half of the Leave Policy and half of the Payroll Policy, mixing two different topics together.

Document Structure-Based Splitting avoids this by creating chunks based on section boundaries

## Difference Between Text Structure-Based and Document Structure-Based Splitting

VERY IMPORTANT INTERVIEW QUESTION.

### Text Structure-Based Splitting

Splits based on:

Paragraphs
Sentences
New lines
Words

Example:

RecursiveCharacterTextSplitter
Goal

Preserve:

Human-readable semantic meaning
Example Data
Articles
PDFs
Blogs
Plain text

### Document Structure-Based Splitting

Splits based on:

Code blocks
JSON hierarchy
HTML tags
Markdown headings
XML nodes
Goal

Preserve:

Structural meaning
Logical hierarchy
Syntax relationships

### Comparison Table

Feature	Text Structure Splitting	        Document Structure Splitting
Focus	Semantic text	                    Document structure
Used For	Articles, PDFs	Code,           JSON, HTML
Separator Type	Paragraphs, sentences	    Classes, tags, headings
Example	RecursiveCharacterTextSplitter	    Language splitters
Goal	Human meaning	                    Structural integrity 


Types of Document Structure-Based Splitting

Mainly used for:

Document Type	                            Splitting Strategy
Python code	                                Function/class splitting
JSON	                                    Object hierarchy splitting
HTML	                                    Tag-based splitting
Markdown	                                Heading-based splitting
XML	                                        Node-based splitting 

## Language-Based Splitting

### What is Language-Based Splitting?

Language-Based Splitting is a chunking technique that splits content according to the rules and structure of a specific language. The language can be a natural language (English, Hindi, Chinese) or a programming language (Python, Java, SQL, JavaScript).

Instead of splitting randomly, it tries to split at meaningful boundaries such as sentences, functions, classes, methods, or code blocks.

### Why Needed?
Different languages have different structures. If we use normal length-based splitting, important information may get broken in the middle.

Example

For Python code:

def calculate_salary():
    bonus = 1000
    return bonus

A normal splitter might cut the function in the middle, making it hard to understand.

Language-Based Splitting keeps the entire function together.

Benefits
Preserves the meaning and structure of the language.
Improves retrieval accuracy.
Keeps functions, classes, and code blocks intact.
Helps LLMs better understand and generate accurate responses.

### Architecture Flow

Source Code File
        ↓
Language-Aware Splitter
        ↓
Detect Classes / Functions
        ↓
Create Logical Chunks
        ↓
Embeddings
        ↓
Code RAG Retrieval

## LangChain Language Splitter

LangChain provides:

### RecursiveCharacterTextSplitter.from_language()

Supported Languages
Language
Python
Java
JavaScript
Go
C++
HTML
Markdown

### Python Code Splitting

Example Python Code
class Employee:


    def calculate_salary(self):
        return self.salary * 12




def process_data():
    print("Processing")
Problem with Random Splitting

Function body may separate from definition.

Solution

Use language-aware splitter.

LangChain Example
from langchain.text_splitter import RecursiveCharacterTextSplitter, Language


python_splitter = RecursiveCharacterTextSplitter.from_language(
    language=Language.PYTHON,
    chunk_size=300,
    chunk_overlap=30
)
What Happens Internally?

Splitter understands:

class
def
indentation
syntax structure
Splitting Output Conceptually

Chunk 1:

class Employee:
    def calculate_salary(self):
        return self.salary * 12

Chunk 2:

def process_data():
    print("Processing")

Logical integrity preserved.

Why Important in RAG?

Code assistants require:

✅ Function-level retrieval ✅ Dependency understanding ✅ Better semantic embeddings

Real-World Example

GitHub Copilot-like systems.

They retrieve:

Relevant functions
Similar classes
APIs

using structure-aware chunking.

### HTML Document Splitting
Why HTML Needs Special Splitting

HTML contains hierarchy:

<html>
  <body>
    <h1>Title</h1>
    <p>Paragraph</p>
  </body>
</html>

Blind splitting may break tags.

Structure-Aware HTML Splitting

Uses:

Headings
Sections
Tags
DOM hierarchy
Goal

Preserve webpage structure.

Example Use Cases
Documentation websites
Knowledge bases
Enterprise portals
Tutorials
Example Concept
<h1>Introduction</h1>
    ↓
Chunk 1


<h2>Installation</h2>
    ↓
Chunk 2
Benefits

✅ Better semantic retrieval ✅ Section-aware embeddings ✅ Preserves webpage hierarchy

### JSON Document Splitting
Why JSON Splitting is Important

JSON contains nested hierarchy.

Example:

{
  "employee": {
    "name": "Kishor",
    "skills": ["Python", "RAG"]
  }
}
Problem with Random Splitting

Nested objects may break.

Example:

Chunk 1:

{
  "employee": {

Chunk 2:

"skills": ["Python"]

Meaning breaks.

Structure-Based JSON Splitting

Splits based on:

Objects
Arrays
Key-value hierarchy
Architecture
JSON File
    ↓
Hierarchy Parser
    ↓
Object-Level Splitting
    ↓
Semantic Chunks
Example Concept

Chunk 1:

{
 "employee": {
   "name": "Kishor"
 }
}

Chunk 2:

{
 "employee": {
   "skills": ["Python", "RAG"]
 }
}
Why Important?

Useful in:

API RAG
Configuration systems
Cloud applications
Microservices
Example LangChain Concept
from langchain.text_splitter import RecursiveJsonSplitter
JSON Splitter Workflow
Nested JSON
     ↓
Hierarchy Detection
     ↓
Object-Level Chunking
     ↓
Preserved Relationships

### Markdown Document Splitting 

Why Markdown Needs Special Splitting

Markdown documents contain:

Headings
Subheadings
Bullet lists
Code blocks
Sections

Example:

" # Introduction


" ## Installation


" ## Usage
Problem with Generic Splitting

Random splitting may separate:

Heading
Related content
Markdown-Aware Splitting

Uses:


Heading

Subheading

Sections

LangChain Markdown Splitter
from langchain.text_splitter import MarkdownHeaderTextSplitter
Example
headers_to_split_on = [
    ("#", "Header 1"),
    ("##", "Header 2")
]


markdown_splitter = MarkdownHeaderTextSplitter(
    headers_to_split_on=headers_to_split_on
)
What Happens?

Document split according to:

Main sections
Subsections
Example Output Concept

Chunk 1:

" # Introduction
Content...

Chunk 2:

"## Installation
Content...
Why Markdown Splitting is Important

Modern developer documentation uses Markdown heavily.

Examples:

GitHub docs
LangChain docs
LangGraph docs
README files


## Real-World Enterprise Examples

Enterprise Documentation
Company Wiki
 ├── HR Policies
 ├── Payroll
 ├── Benefits
 └── Leave Policy

Each section becomes a separate chunk.

API Documentation
Authentication API
User API
Payment API

Each API section is indexed separately.

Software Repositories
Class A
Class B
Class C

Each class or function can become an independent chunk.

Research Papers
Abstract
Introduction
Methodology
Results
Conclusion

Each section is retrieved independently.

## Advantages of Document Structure-Based Splitting
Better Semantic Preservation

Related information remains together.

Higher Retrieval Accuracy

Queries retrieve the exact section instead of mixed content.

Better Context Quality

LLM receives complete topics rather than fragmented text.

Reduced Hallucinations

Focused retrieval leads to more grounded answers.

Preserves Document Hierarchy

Headings and section relationships remain intact.

## Disadvantages of Document Structure-Based Splitting
Uneven Chunk Sizes

Some sections may be extremely small or extremely large.

Not All Documents Have Structure

Raw text files may not contain headings or sections.

Large Sections Need Further Splitting

A 20-page section may still exceed token limits.

Additional Processing Required

Structure detection increases preprocessing complexity.

1. Best Practices
Use Hybrid Chunking

Combine structure-based splitting with length-based splitting.

Structure Split
      +
Recursive Split

This is the most common production approach.

Preserve Metadata

Store:

Heading
Section
Page Number
Source

to improve retrieval and filtering.

Keep Related Content Together

Do not split in the middle of:

Functions
Classes
Sections
Tables
Evaluate Retrieval Quality

Always test:

Context Precision
Context Recall
Answer Relevance

instead of assuming a chunking strategy is optimal.