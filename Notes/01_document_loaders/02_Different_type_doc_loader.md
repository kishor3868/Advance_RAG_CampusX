## 1. Text Loader (TextLoader)
### Definition

TextLoader is used to load plain text files (.txt) into LangChain as Document objects.

Import
from langchain_community.document_loaders import TextLoader
Code Example
sample.txt
LangChain is a framework for building LLM applications.
Python Code
from langchain_community.document_loaders import TextLoader

loader = TextLoader("sample.txt")

documents = loader.load()

print(documents)
Output
[
    Document(
        page_content='LangChain is a framework for building LLM applications.',
        metadata={'source': 'sample.txt'}
    )
]

### Common Functions
#### 1. load()

Loads the entire file.

docs = loader.load()

Returns:

List[Document]
#### 2. lazy_load()

Loads documents lazily (one by one).

for doc in loader.lazy_load():
    print(doc.page_content)

Useful for large files.

#### 3. aload()

Async version of load().

docs = await loader.aload()

## 2. CSV Loader (CSVLoader)
### Definition

CSVLoader is used to load CSV files and convert each row into a separate Document.

Import
from langchain_community.document_loaders import CSVLoader
Example CSV
employee.csv
id,name,department
1,John,Data Science
2,Alice,HR
Code Example
from langchain_community.document_loaders import CSVLoader

loader = CSVLoader(file_path="employee.csv")

documents = loader.load()

print(documents[0].page_content)
Output
id: 1
name: John
department: Data Science

Each row becomes one document.

### Common Functions
1. load()
docs = loader.load()

Loads all rows.

2. lazy_load()
for doc in loader.lazy_load():
    print(doc)

Loads row by row.

3. aload()
docs = await loader.aload()

Async loading.

Useful Parameters
source_column

Specify which column should be used as metadata source.

loader = CSVLoader(
    file_path="employee.csv",
    source_column="name"
)

3. PDF Loader (PyPDFLoader)
Definition

PyPDFLoader is used to load PDF documents and extract text page by page.

Each page becomes a separate Document.

Import
from langchain_community.document_loaders import PyPDFLoader
Code Example
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("rag_notes.pdf")

documents = loader.load()

print(documents[0].page_content)
Output
Document(
    page_content='Introduction to RAG...',
    metadata={
        'source': 'rag_notes.pdf',
        'page': 0
    }
)
Common Functions
1. load()

Loads all PDF pages.

docs = loader.load()
2. lazy_load()

Loads page by page.

for page in loader.lazy_load():
    print(page.page_content)

Useful for large PDFs.

3. aload()

Async loading.

docs = await loader.aload()
4. load_and_split()

Loads and directly splits the PDF.

from langchain_text_splitters import RecursiveCharacterTextSplitter

loader = PyPDFLoader("rag_notes.pdf")

docs = loader.load_and_split(
    RecursiveCharacterTextSplitter(
        chunk_size=1000,
        chunk_overlap=200
    )
)

## What All Loaders Return?

All loaders return LangChain Document objects.

Example:

Document(
    page_content="Actual Text",
    metadata={
        "source": "file_name"
    }
)
### Quick Comparison
Loader	File Type	Output
TextLoader	.txt	One or more Documents
CSVLoader	.csv	One Document per row
PyPDFLoader	.pdf	One Document per page

## PyPDF (pypdf)
### What is it?

PyPDF is a Python library used to:

Read PDF files
Extract text
Merge PDFs
Split PDFs
Rotate pages
Access metadata

LangChain's PyPDFLoader internally uses pypdf.

Example
from pypdf import PdfReader

reader = PdfReader("sample.pdf")

for page in reader.pages:
    print(page.extract_text())
Limitation

PyPDF works best when the PDF contains selectable text.

✅ Good:

This is a digital PDF.

❌ Not good:

Scanned PDF (image of text)

For scanned PDFs, extract_text() often returns little or no text.

## 2. PDFMiner (pdfminer.six)
### What is it?

PDFMiner is a more advanced PDF text extraction library.

It focuses on:

Detailed text extraction
Preserving layout
Character-level positioning
Font information
Example
from pdfminer.high_level import extract_text

text = extract_text("sample.pdf")

print(text)
### Advantages over PyPDF
Better text extraction accuracy
Better handling of complex layouts
More control over PDF structure
### Disadvantages
Slower than PyPDF
More complex

## How Do We Extract Text From PDF Images?

We use OCR (Optical Character Recognition).

### Popular OCR tools:

Tesseract OCR
Google Vision API
Azure Document Intelligence
AWS Textract
OCR Pipeline
PDF
 ↓
Extract Images
 ↓
OCR
 ↓
Text
 ↓
Chunking
 ↓
Embeddings
 ↓
Vector DB
In LangChain

For scanned/image-heavy PDFs, a common flow is:

from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("document.pdf")

docs = loader.load()

If text extraction is poor:

PDF
 ↓
Convert pages to images
 ↓
OCR
 ↓
Extract text
 ↓
Create Documents
Real Production RAG Systems

Modern RAG systems often use:

PDF
 ├── Text Extraction (PyPDF/PDFMiner)
 ├── Table Extraction
 └── Image Extraction
         ↓
      OCR/Vision Model

Then all extracted content is converted into text before embedding.

## Different types of Loader

Modern RAG systems need knowledge from many different sources.

Examples:

APIs
Websites
JSON responses
Documentation portals
Internal company wikis
Blogs
Databases
Microservices

### Different sources require different loaders.

Two extremely important loaders are:

1. JSONLoader
2. WebBaseLoader

These loaders are widely used in:

Enterprise AI systems
AI agents
Web-based RAG
API-driven applications
Documentation chatbots 

### Why Different Loaders Are Needed in RAG

Not all knowledge exists in PDFs.

Modern applications generate huge amounts of:

JSON data
Web content
API responses
Dynamic webpages

Example:

A company may store:

Data Type	Example
JSON	API response
HTML	Website docs
XML	Configurations
CSV	Reports
Markdown	Developer docs

## What is JSONLoader?

### Definition

JSONLoader is a document loader used to:

✅ Load JSON files ✅ Parse structured data ✅ Extract selected fields ✅ Convert into document objects

Why JSON is Important

JSON is one of the MOST COMMON data formats in modern systems.

Used in:

APIs
Web applications
Microservices
Databases
AI applications
Cloud systems
Example JSON
{
  "employee": {
    "name": "Kishor",
    "department": "AI",
    "experience": 3
  }
}
Problem with Raw JSON

LLMs cannot directly process deeply nested JSON efficiently.

JSONLoader helps by:

✅ Extracting useful content ✅ Flattening structures ✅ Standardizing output

4. Structure of JSON Data

JSON can be:

1. Flat JSON
{
  "name": "John",
  "age": 25
}
2. Nested JSON
{
  "employee": {
    "name": "John",
    "skills": ["Python", "RAG"]
  }
}
3. JSON Arrays
[
  {"name": "John"},
  {"name": "Alice"}
]
Challenge

Nested structures are difficult for direct retrieval.

JSONLoader solves this.

5. How JSONLoader Works
Step-by-Step Flow
JSON File
   ↓
JSONLoader
   ↓
Parse JSON Structure
   ↓
Extract Selected Fields
   ↓
Convert into Document Objects
   ↓
Ready for Chunking & Embeddings

### Internal Working
Step 1 — Load JSON File

Reads JSON from:

Local files
APIs
Cloud storage
Step 2 — Parse JSON

Understands:

Keys
Values
Nested structures
Arrays
Step 3 — Extract Content

Relevant fields are selected.

Step 4 — Create Document Objects

Output becomes:

Document(
   page_content="John works in AI department",
   metadata={"source": "employee.json"}
)
### JSONLoader Architecture Flow 


                ┌──────────────────┐
                │ JSON File/API    │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ JSONLoader       │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ JSON Parsing     │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ Extract Fields   │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ Document Objects │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ RAG Pipeline     │
                └──────────────────┘
### Metadata Handling in JSONLoader

Metadata is extremely important.

Example Metadata
metadata={
   "source": "employees.json",
   "record_id": 101,
   "department": "AI"
}
Why Metadata Matters
Better Retrieval

Search only:

AI department
Recent records
Specific categories
Source Attribution

LLM can cite original data source.

Filtering

Useful in enterprise search systems.

## What is WebBaseLoader?
### Definition

WebBaseLoader is used to:

✅ Load webpages ✅ Extract website text ✅ Parse HTML content ✅ Convert web content into document objects

### Why Web Loaders Are Important

Most knowledge today exists on websites.

Examples:

Documentation portals
Blogs
Wikipedia
Company websites
Knowledge bases
Tutorials

Without web loaders:

❌ Web knowledge cannot enter RAG systems.

Real-World Analogy

Imagine an AI researcher reading websites manually.

WebBaseLoader automates that process.

### How WebBaseLoader Works

Workflow

Website URL
    ↓
HTTP Request
    ↓
Download HTML
    ↓
Parse HTML
    ↓
Extract Text
    ↓
Document Object

Step-by-Step

Step 1 — Send Request

Loader accesses webpage.

Step 2 — Download HTML

Raw webpage HTML is fetched.

Step 3 — Parse HTML

HTML tags removed.

Example:

<h1>RAG Tutorial</h1>

becomes:

RAG Tutorial
Step 4 — Create Document Objects

Content converted into standard format.

### Simple WebBaseLoader Example

from langchain.document_loaders import WebBaseLoader


loader = WebBaseLoader("https://example.com")
docs = loader.load()
Output
[
   Document(page_content="Website text...",
            metadata={"source": "https://example.com"})
]
###  Web Scraping Flow in RAG
                ┌────────────────┐
                │ Website URL    │
                └───────┬────────┘
                        │
                        ▼
                ┌────────────────┐
                │ WebBaseLoader  │
                └───────┬────────┘
                        │
                        ▼
                ┌────────────────┐
                │ HTML Parsing   │
                └───────┬────────┘
                        │
                        ▼
                ┌────────────────┐
                │ Text Extraction│
                └───────┬────────┘
                        │
                        ▼
                ┌────────────────┐
                │ Document Object│
                └───────┬────────┘
                        │
                        ▼
                ┌────────────────┐
                │ RAG Pipeline   │
                └────────────────┘


## Recursive URL Loader 

### What is Recursive Loading?

Sometimes one webpage links to multiple subpages.

Example:

Docs Homepage
   ├── Installation
   ├── API Guide
   ├── Tutorials
   └── FAQ

Loading only one page is not enough.

Recursive loaders:

✅ Follow internal links ✅ Crawl subpages ✅ Load entire documentation websites

Why Recursive Loading is Needed

Modern documentation sites are hierarchical.

Examples:

LangChain docs
AWS docs
Azure docs
Kubernetes docs

Knowledge is spread across multiple pages.

### Recursive Loading Flow
Main URL
   ↓
Extract Internal Links
   ↓
Visit Child URLs
   ↓
Extract Content
   ↓
Repeat Recursively
Example Concept
RecursiveUrlLoader(
   url="https://docs.example.com",
   max_depth=2
)
#### What is max_depth?

Controls how deep crawler goes.

Example:

Depth	Meaning
1	Only main page
2	Main + child pages
3	Deeper subpages
Benefits of Recursive Loading

✅ Complete knowledge ingestion ✅ Better enterprise search ✅ Large-scale documentation indexing

Risks

❌ Huge data volume ❌ Duplicate pages ❌ Slow crawling ❌ Irrelevant links

## Lazy Loading
### What is Lazy Loading?

Lazy loading means:

Load data only when needed

instead of loading everything at once.

Traditional Loading
Load Entire Website → Huge Memory Usage
Lazy Loading
Load One Document → Process → Load Next
Example
for doc in loader.lazy_load():
    print(doc)
### Why Lazy Loading is Beneficial
1. Lower Memory Usage

Large datasets may contain:

Millions of documents
Huge webpages
Large PDFs

Loading all at once may crash memory.

Lazy loading prevents this.

2. Faster Streaming Pipelines

Documents can be processed while loading.

No need to wait for entire dataset.

3. Better Scalability

Useful for enterprise-scale systems.

4. Efficient for Crawlers

Web crawlers can process page-by-page.

5. Better Resource Utilization

CPU and memory usage become optimized.

## Load vs Lazy Load

Feature	load()	lazy_load()
Loading Type	Entire dataset	Incremental
Memory Usage	High	Low
Speed	May be slower initially	Faster streaming
Scalability	Limited	Better
Enterprise Usage	Small datasets	Large datasets 
