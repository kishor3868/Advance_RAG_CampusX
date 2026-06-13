## What is a Document Loader?
### Definition

A document loader is a component that:

✅ Loads documents from a source ✅ Parses the content ✅ Extracts useful information ✅ Converts output into standardized document objects

Document Loader = Data Reader + Parser + Standardizer

### Main Tasks of a Document Loader

A document loader mainly performs 3 important tasks.

#### (a) Load Document

The loader first reads the raw document.

Example:

loader = PyPDFLoader("resume.pdf")

It accesses:

File path
Cloud storage
URL
Database

#### (b) Parse Document

Now the loader extracts meaningful information.

Example:

From PDF:

Text
Images
Tables
Page structure

This step is called:

Parsing

#### (c) Extract Output in Unified Format

After parsing, all outputs are converted into:

Document Objects

This standardized structure is called:

Unified Document Format
Unified Document Format Example
Document(
    page_content="Employee gets 24 paid leaves",
    metadata={"page": 3, "source": "hr_policy.pdf"}
)

Now all documents look similar internally.

This makes downstream tasks easier:

Chunking
Embeddings
Retrieval
Filtering

#### Complete Flow 
Raw PDF
   ↓
Document Loader
   ↓
Parser
   ↓
Extract Text + Metadata
   ↓
Convert into Document Object
   ↓
Ready for RAG Pipeline

## Document Object in LangChain

In LangChain, most loaders return data in the form of:

Document Object

This is one of the MOST IMPORTANT concepts.

Structure of Document Object

Document(
    page_content="Actual text content",
    metadata={
        "source": "file.pdf",
        "page": 1
    }
)
### Main Components
Component	Meaning
page_content	Actual extracted text
metadata	Additional information
Example
from langchain.schema import Document


Document(
    page_content="RAG improves LLM responses",
    metadata={
        "source": "rag_notes.pdf",
        "page": 5
    }
)
### Why Document Object is Important

Because every downstream component expects:

Standardized structure
Uniform processing
Metadata support

This makes pipelines modular.

### Metadata in Documents

Metadata means:

Data about data

It gives additional contextual information.

#### Common Metadata Examples 

Metadata	Meaning
source	File name
page	PDF page number
author	Document author
timestamp	Creation time
section	Chapter name
URL	Website source
Example
metadata={
    "source": "policy.pdf",
    "page": 4,
    "author": "HR Team"
}
#### Why Metadata is Important in RAG 

1. Source Tracking

LLM can mention:

“According to page 4 of policy.pdf...”

2. Filtering

Example:

Retrieve only:

HR documents
Technical manuals
Recent files
3. Better Retrieval

Metadata improves search quality.

4. Citations

Used in enterprise-grade AI systems

## BaseLoader in LangChain

Most loaders in LangChain inherit from:

BaseLoader

It acts like:

Parent Class for all loaders
### Why BaseLoader Exists

Because all loaders should follow:

Common structure
Standard methods
Consistent behavior
Simplified Concept


                BaseLoader
                     │
     ┌───────────────┼───────────────┐
     │               │               │
PyPDFLoader   CSVLoader    WebBaseLoader


### Common Methods

Method	   Purpose

load()	   Load entire document
lazy_load()	 Load document lazily
aload()	   Async loading
Example
documents = loader.load()

Returns:
List[Document]

## Types of Document Loaders

LangChain provides many loaders.

Popular             Document Loaders
Loader	            Purpose
PyPDFLoader	        Load PDFs
PDFMinerLoader	    Advanced PDF parsing
PDFPlumberLoader	Table-aware PDF parsing
TextLoader	        TXT files
CSVLoader	        CSV files
JSONLoader	        JSON files
UnstructuredPDFLoader	Complex PDFs
WebBaseLoader	    Websites
DirectoryLoader	    Entire folders
Docx2txtLoader	    Word files

### Why different type of document loader requred for each file?

Ans: Because different file have different structure and thus they required different type of parsing style,thats why we required different document loader

## What is a Parser?

A parser extracts structured information from raw documents. 
Example

Raw PDF contains:

Text
Tables
Images
Layout

Parser identifies and extracts these components.

### Types of Parsers

Parser Type	        Purpose
Text Parser	        Extract plain text
Layout Parser	    Understand structure
OCR Parser	        Extract text from images
Table Parser	    Extract tables
Image Parser	    Extract images
HTML Parser	Parse   webpage structure