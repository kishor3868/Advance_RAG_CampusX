# Corrective RAG (CRAG) — Detailed Notes
Table of Contents
What problem exists in traditional RAG?
Why do we need Corrective RAG?
What is Corrective RAG (CRAG)?
Architecture of CRAG
Step-by-step working of CRAG
Retrieval Evaluator
Correct, Ambiguous and Incorrect Classification
Knowledge Refinement
Knowledge Searching
Complete Workflow using the given diagram
Why CRAG is better than Traditional RAG
LangChain Implementation
LangGraph Architecture
Advantages and Disadvantages
Interview Questions
Key Takeaways
# 1. What problem exists in Traditional RAG?

This is the exact problem that gave birth to CRAG.

Traditional RAG pipeline:

User Query
    ↓
Retriever
    ↓
Retrieved Documents
    ↓
    LLM
    ↓
Answer
Problem

The LLM generates an answer using whatever documents it receives.

It does NOT verify whether:

retrieved documents are actually relevant
retrieval missed important information
retrieved context is partially correct
retrieved context is outdated
Example

User Query:

Who was the screenwriter for Death of a Batman?

Retriever returns:

d1 -> Batman movie history
d2 -> Batman comic information

Neither document contains:

Death of a Batman screenwriter

Unfortunately traditional RAG still sends:

d1 + d2

to LLM.

Result:

LLM still generates answer

even though evidence is missing.

This creates:

Retrieval Hallucination
Very Important Statement

Traditional RAG answers:

Whether the answer exists in retrieved documents or not.

CRAG answers:

Only if retrieval quality is good enough.

# 2. Why do we need Corrective RAG?

CRAG introduces:

Retrieval Quality Verification

before generation.

Instead of blindly trusting retrieval, CRAG asks:

Are these retrieved documents actually useful?

This simple question dramatically improves RAG quality.

# 3. What is Corrective RAG (CRAG)?
Definition

Corrective RAG (CRAG) is an advanced RAG architecture that evaluates retrieved documents before generation and performs corrective actions such as knowledge refinement and web searching when retrieval quality is poor.

Simple Definition

CRAG introduces a:

Retrieval Evaluator

between retrieval and generation.

Traditional RAG:

Retrieve → Generate

CRAG:

Retrieve → Evaluate → Correct → Generate
# 4. CRAG Architecture

Using your provided diagram:

Query X
   ↓
Retriever
   ↓
Retrieved Documents (d1,d2)
   ↓
Retrieval Evaluator
   ↓
┌────────────┬──────────────┬────────────┐
│ Correct    │ Ambiguous    │ Incorrect  │
└────────────┴──────────────┴────────────┘

Based on this decision:

Decision	Action
Correct	Knowledge Refinement
Ambiguous	Knowledge Refinement + Knowledge Searching
Incorrect	Knowledge Searching
# 5. Complete Workflow of CRAG

Following exactly your steps:

Step 1

User asks question:

X

Example:

Who was the screenwriter for Death of a Batman?
Step 2

Retriever returns:

d1
d2

Example:

d1 = Batman movie article
d2 = Batman comic article
Step 3

Retrieved documents are passed to:

Retrieval Evaluator
# 6. Retrieval Evaluator

This is the core innovation of CRAG.

The retrieval evaluator asks:

Are retrieved documents useful for answering X?

In original CRAG paper:

this evaluator is implemented using:

T5 Transformer

specifically a lightweight evaluator model.

Input:

Question X
+
Retrieved Documents

Output:

Correct
Ambiguous
Incorrect
Example

Input:

Query:
Who was the screenwriter for Death of a Batman?

Retrieved Docs:
Batman movie history
Batman comic history

Evaluator decides:

Incorrect

because retrieved documents cannot answer the question.

# 7. Three Decisions in CRAG
1. Correct

Meaning:

Retrieved documents are sufficient.

Example:

Query:
Who invented Transformer architecture?

Retrieved docs contain:
Attention Is All You Need paper.

Decision:

Correct
# 2. Ambiguous

Meaning:

Retrieved docs partially answer the query.

Example:

Query:
Applications of AI in healthcare.

Retrieved docs contain:
Diagnosis systems only.

Missing:

medical imaging
patient monitoring
drug discovery

Decision:

Ambiguous
# 3. Incorrect

Meaning:

Retrieved documents are irrelevant.

Example:

Query:
Death of a Batman screenwriter

Retrieved docs:
Batman movie history
DC universe information

Decision:

Incorrect
# 8. Knowledge Refinement

Knowledge refinement handles:

Correct
+
Ambiguous

documents.

According to your diagram:

d1,d2
  ↓
Break into strips
  ↓
Filter
  ↓
Recompose
Step 1: Decompose

Documents are broken into:

strip1
strip2
strip3
...
stripk

Example:

Document:

AI helps diagnosis.
AI helps medical imaging.
AI helps patient monitoring.

becomes:

strip1 -> diagnosis
strip2 -> medical imaging
strip3 -> patient monitoring
Step 2: Filter

Irrelevant strips are removed.

Suppose query is:

How AI helps diagnosis?

Remaining strips:

diagnosis

Removed strips:

medical imaging
patient monitoring
Step 3: Recompose

Remaining strips are merged again.

Result:

K_in

which means:

Internal Knowledge

Diagram:

d1,d2
 ↓
Decompose
 ↓
strip1 strip2 strip3
 ↓
Filter
 ↓
strip1
 ↓
Recompose
 ↓
K_in
# 9. Knowledge Searching

Knowledge Searching handles:

Ambiguous
+
Incorrect

cases.

Workflow from your diagram:

X
 ↓
Rewrite Query
 ↓
Web Search
 ↓
k1,k2,k3,...kn
 ↓
Select
 ↓
K_ex
Query Rewriting

Original query:

Who was the screenwriter for Death of a Batman?

CRAG rewrites it into:

Death of a Batman screenwriter Wikipedia

or

Death of a Batman author information

This rewritten query is more searchable.

Web Search

CRAG performs:

External search

Search returns:

k1
k2
k3
...
kn

Example:

k1 = Wikipedia result
k2 = IMDB result
k3 = DC database result
Selection

CRAG selects best external knowledge:

K_ex

where:

K_ex = External Knowledge
# 10. Final Generation Phase

According to your diagram:

Correct Case

Generator receives:

X + K_in
Ambiguous Case

Generator receives:

X + K_in + K_ex
Incorrect Case

Generator receives:

X + K_ex

Visualization:

Correct:
Query + Internal Knowledge

Ambiguous:
Query + Internal Knowledge + External Knowledge

Incorrect:
Query + External Knowledge
Complete CRAG Diagram
Query X
   ↓
Retriever
   ↓
d1,d2
   ↓
Retrieval Evaluator (T5)
   ↓

┌─────────────┬──────────────┬─────────────┐
│ Correct     │ Ambiguous    │ Incorrect   │
└─────────────┴──────────────┴─────────────┘

Correct:
d1,d2
 ↓
Refinement
 ↓
K_in

Ambiguous:
d1,d2
 ↓
Refinement
 ↓
K_in

+
Web Search
 ↓
K_ex

Incorrect:
Web Search
 ↓
K_ex

Final Generation:
X + K_in
or
X + K_in + K_ex
or
X + K_ex
# 11. Why CRAG is Better than Traditional RAG
Traditional RAG	CRAG
Blindly trusts retrieval	Evaluates retrieval
Cannot detect bad retrieval	Detects bad retrieval
Hallucination risk high	Hallucination reduced
No fallback mechanism	Uses web search fallback
Fixed knowledge	Dynamic knowledge
# 12. LangChain Implementation
Retrieval
retrieved_docs = retriever.invoke(query)
Evaluator Chain
evaluation_prompt = """
Question:
{query}

Retrieved Documents:
{documents}

Classify retrieval quality as:
1. Correct
2. Ambiguous
3. Incorrect
"""
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4.1-mini"
)

evaluation_chain = (
    ChatPromptTemplate.from_template(
        evaluation_prompt
    )
    | llm
)
Evaluation
decision = evaluation_chain.invoke(
    {
        "query": query,
        "documents": retrieved_docs
    }
)
Knowledge Refinement
compression_retriever = (
    ContextualCompressionRetriever(
        base_retriever=retriever,
        base_compressor=compressor
    )
)
Web Search
from langchain_community.tools import DuckDuckGoSearchRun

search = DuckDuckGoSearchRun()

web_results = search.invoke(query)
# 13. LangGraph Workflow
User Query
    ↓
Retrieve Node
    ↓
Evaluate Node
    ↓
 ┌───────────────┐
 │Correct        │
 │Ambiguous      │
 │Incorrect      │
 └───────────────┘

Correct → Refinement
Ambiguous → Refinement + Search
Incorrect → Search

      ↓
Generation Node

Example:

def evaluate(state):

    result = evaluator.invoke(
        {
            "query": state["query"],
            "docs": state["docs"]
        }
    )

    return {
        "decision": result
    }
# 14. Advantages

✅ Detects retrieval failures

✅ Reduces hallucination

✅ Supports external knowledge

✅ Better factual accuracy

✅ More robust than standard RAG

# 15. Disadvantages

❌ More latency

❌ More expensive

❌ Additional evaluator model required

❌ More complex pipeline

# 16. Interview Questions
What problem does CRAG solve?

CRAG solves retrieval failures in traditional RAG.

What model is used in retrieval evaluator?

Typically:

T5 Transformer
What are the three evaluator outputs?
Correct
Ambiguous
Incorrect
What is K_in?

Internal refined knowledge from retrieved documents.

What is K_ex?

External knowledge obtained from web search.

Which cases trigger web search?
Ambiguous
Incorrect
# 17. Key Takeaways

✅ Traditional RAG always answers even if retrieval is wrong.

✅ CRAG introduces a retrieval evaluator.

✅ Retrieval evaluator uses a T5 model.

✅ Evaluator classifies retrieval as:

Correct
Ambiguous
Incorrect

✅ Correct and Ambiguous go to Knowledge Refinement.

✅ Ambiguous and Incorrect go to Knowledge Searching.

✅ CRAG combines internal and external knowledge before generation.

✅ CRAG is one of the most important improvements over classical RAG architectures.