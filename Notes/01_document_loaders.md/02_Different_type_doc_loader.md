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