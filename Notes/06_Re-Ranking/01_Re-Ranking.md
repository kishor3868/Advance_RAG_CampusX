# Re-Ranking in RAG — Detailed Notes
Table of Contents
Introduction to Re-Ranking
What is Re-Ranking in RAG?
When is Re-Ranking Performed?
Why Re-Ranking is Required?
How Embedding Information Loss Leads to Re-Ranking
Retrieval Pipeline Before and After Re-Ranking
Bi-Encoder Architecture
Cross-Encoder Architecture
Difference Between Bi-Encoder and Cross-Encoder
How Cross Encoder Works Using BERT
Input Format in Cross Encoder
Query-Document Interaction
Query-to-Document and Document-to-Query Attention
Complete Re-Ranking Workflow
Cohere ReRank Implementation
FlashRank Implementation
LangChain ReRank Pipeline
LangGraph ReRank Workflow
Advantages and Disadvantages
Interview Questions
Key Takeaways
# 1. Introduction to Re-Ranking

In a traditional RAG system:

User Query
    ↓
Embedding Model
    ↓
Vector Search
    ↓
Top K Documents
    ↓
    LLM
Problem

The retrieved Top-K documents are often:

relevant but not perfectly ranked
partially relevant
noisy
missing important context
Why?

Because embeddings compress information.

Solution

Before sending retrieved documents to the LLM we perform:

Re-Ranking
# 2. What is Re-Ranking in RAG?
Definition

Re-ranking is the process of reordering retrieved documents according to their actual relevance to the query before passing them to the LLM.

Simple Definition

Retrieval gives:

Candidate documents

Re-ranking gives:

Best ordered documents
Important Idea

Retrieval focuses on:

Fast approximate search

Re-ranking focuses on:

Accurate final ranking
Example

Initial Retrieval:

Rank	Document
1	AI diagnosis
2	Hospital history
3	Patient monitoring
4	Medical imaging

After Re-ranking:

Rank	Document
1	Patient monitoring
2	AI diagnosis
3	Medical imaging
4	Hospital history
# 3. When is Re-Ranking Performed?

This is one of the most important concepts.

Re-ranking happens AFTER retrieval.

Pipeline:

User Query
    ↓
Retriever
    ↓
Top 20 Retrieved Documents
    ↓
Re-Ranker
    ↓
Top 5 Best Documents
    ↓
LLM
Therefore:
Retrieval First
Re-Ranking Second
Generation Third
# 4. Why Re-Ranking is Required?

Approximate retrieval techniques like:

HNSW
IVF
ANN Search

prioritize:

Speed

not:

Perfect ranking

Embedding retrieval can produce:

near misses
weakly related chunks
duplicated chunks

Re-ranking solves:

✅ better ordering

✅ higher precision

✅ lower hallucination

✅ stronger grounding

# 5. How Embedding Information Loss Creates Need for Re-Ranking

This is extremely important.

Embedding model converts:

Long text

into:

768 dimensional vector

During compression:

some information gets lost.

Example:

Document contains:

AI diagnosis
Medical imaging
Insurance claims
Patient monitoring

Embedding becomes:

Average semantic representation

Query:

patient monitoring

may retrieve:

AI diagnosis

instead of:

patient monitoring

This problem is called:

Semantic Compression Loss

Re-ranking solves this by:

Re-reading actual text

instead of relying only on embeddings.

# 6. Retrieval Pipeline with Re-Ranking
User Query
      ↓
Embedding Model
      ↓
Vector Search
      ↓
Top 20 Candidate Documents
      ↓
Cross Encoder ReRanker
      ↓
Top 5 Best Documents
      ↓
LLM
Important Observation

Retriever optimizes:

Recall

Re-ranker optimizes:

Precision
# 7. Bi-Encoder Architecture
## Definition

Bi-Encoder independently encodes:

query
document

into separate embeddings.

Architecture:

Query
  ↓
Encoder
  ↓
Query Embedding

Document
   ↓
Encoder
   ↓
Document Embedding

Similarity Calculation
Example
Query → 768 dimensions
Document → 768 dimensions
Cosine Similarity

This is exactly how:

vector databases
embedding retrieval
semantic search

work.

Advantages

✅ Fast

✅ Scalable

✅ Can precompute embeddings

Disadvantages

❌ Cannot model token-level interaction

❌ Limited contextual understanding

# 8. Cross Encoder Architecture
Definition

Cross Encoder jointly processes query and document together and predicts a relevance score.

Architecture:

Query + Document
        ↓
Single Transformer
        ↓
Relevance Score

Unlike Bi-Encoder:

documents are NOT embedded separately.

Cross Encoder directly answers:

How relevant is this document to this query?
Advantages

✅ Highest ranking quality

✅ Full token interaction

✅ Better semantic understanding

Disadvantages

❌ Very slow

❌ Cannot precompute embeddings

# 9. Bi-Encoder vs Cross Encoder
Feature	Bi Encoder	Cross Encoder
Speed	Very Fast	Slow
Retrieval Stage	Yes	No
Re-Ranking Stage	No	Yes
Token Interaction	No	Yes
Precompute Embeddings	Yes	No
Ranking Quality	Medium	Excellent
# 10. How Cross Encoder Works Using BERT

Cross encoder uses:

BERT

architecture.

Input:

[CLS] query tokens [SEP] document tokens [SEP]

Example:

[CLS]
How AI helps hospitals
[SEP]
AI based patient monitoring improves hospitals
[SEP]

The model sees:

Query and document together
# 11. Input Format

The exact input format is:

[CLS]
query token
query token
query token
[SEP]
document token
document token
document token
[SEP]

Example:

[CLS]
patient
monitoring
AI
[SEP]
AI
monitoring
systems
improve
ICU
care
[SEP]
# 12. Query-Document Relationship

Cross encoder learns:

How query relates to document.

Example:

Query token:

monitoring

attends to:

monitoring systems

inside document.

Similarly document tokens attend back to query tokens.

This creates:
Bidirectional interaction
# 13. Query Related to Document and Document Related to Query

Bi encoder:

Query → vector

Document → vector

No interaction.

Cross encoder:

Query token ↔ Document token

Every token sees every other token.

This enables:

deeper semantic understanding
exact contextual matching
stronger ranking accuracy
# 14. Complete Re-Ranking Workflow
User Query
      ↓
Retriever
      ↓
Top 50 Documents
      ↓
Cross Encoder ReRanker
      ↓
Relevance Scores

Doc1 → 0.95
Doc2 → 0.89
Doc3 → 0.78
Doc4 → 0.65

      ↓
Top 5 Returned
      ↓
    LLM
# 15. Cohere ReRank Implementation using LangChain
from langchain_cohere import CohereRerank
from langchain.retrievers.contextual_compression import (
    ContextualCompressionRetriever
)

compressor = CohereRerank(
    model="rerank-english-v3.0"
)

compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=retriever
)

docs = compression_retriever.invoke(
    "How AI helps hospitals?"
)

for doc in docs:
    print(doc.page_content)
Workflow
Retriever
   ↓
Cohere ReRank
   ↓
Best Ranked Documents
# 16. FlashRank Implementation

Installation:

pip install flashrank
pip install langchain-community

Code:

from langchain_community.document_compressors import (
    FlashrankRerank
)

compressor = FlashrankRerank()

compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=retriever
)

docs = compression_retriever.invoke(
    "How AI helps hospitals?"
)
FlashRank Advantages
local inference
no API cost
very fast
lightweight
# 17. LangChain Re-Rank Pipeline
Retriever
    ↓
Candidate Documents
    ↓
ReRank Compressor
    ↓
Top Ranked Documents

Example:

compression_retriever = ContextualCompressionRetriever(
    base_retriever=retriever,
    base_compressor=compressor
)

docs = compression_retriever.invoke(query)
# 18. LangGraph Workflow
from langgraph.graph import StateGraph

Retriever Node:

def retrieve(state):

    docs = retriever.invoke(
        state["query"]
    )

    return {"docs": docs}

ReRank Node:

def rerank(state):

    docs = compression_retriever.invoke(
        state["query"]
    )

    return {"reranked_docs": docs}

Graph:

User Query
      ↓
Retrieve Node
      ↓
ReRank Node
      ↓
Generate Node
      ↓
Answer
# 19. Advantages and Disadvantages
## Advantages

✅ Higher ranking quality

✅ Better grounding

✅ Less hallucination

✅ Better context quality

✅ Improved answer accuracy

## Disadvantages

❌ Higher latency

❌ Additional computation

❌ Expensive for large candidate sets

❌ Cross encoder cannot scale to millions of documents

# 20. Interview Questions
What is re-ranking?

Re-ordering retrieved documents according to relevance.

When is re-ranking performed?

After retrieval and before generation.

Why do we need re-ranking?

Embedding retrieval loses information due to semantic compression.

Difference between bi encoder and cross encoder?

Bi encoder independently embeds query and document.

Cross encoder jointly processes them.

Why is cross encoder more accurate?

Because token level interaction exists between query and document.

Why not use cross encoder directly for retrieval?

Because it is computationally expensive.

# 21. Key Takeaways

✅ Retrieval optimizes recall.

✅ Re-ranking optimizes precision.

✅ Embedding compression causes semantic information loss.

✅ Bi encoders are used for retrieval.

✅ Cross encoders are used for re-ranking.

✅ Cross encoder uses:

[CLS] query [SEP] document [SEP]

format.

✅ Cohere ReRank and FlashRank are popular production re-rankers.

✅ Modern enterprise RAG systems almost always use re-ranking before generation.