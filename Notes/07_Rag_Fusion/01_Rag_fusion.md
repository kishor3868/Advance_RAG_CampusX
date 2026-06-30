# Rank Fusion (RAG Fusion) in RAG — Detailed Notes
Table of Contents
Introduction
What is Rank Fusion in RAG?
Why Rank Fusion is Needed
Why Multi Query Retriever Alone is Not Enough
Relationship Between Multi Query Retrieval and Rank Fusion
What is RAG Fusion?
Components of RAG Fusion
Multi Query Retrieval
Reciprocal Rank Fusion (RRF)
What is Reciprocal Rank Fusion (RRF)?
Why RRF was Introduced
RRF Formula
Understanding Each Term in Formula
Why This Formula Works
Understanding the Constant k_c
Effect of Changing k_c
Numerical Example of RRF
How Re-Ranking Happens Using RRF
Complete RAG Fusion Workflow
Architecture Diagram
LangChain Implementation
LangGraph Workflow
Advantages and Disadvantages
Interview Questions
Key Takeaways

# 1. Introduction

Modern RAG systems often suffer from:

query ambiguity
retrieval blind spots
domain terminology mismatch
incomplete retrieval coverage

Example:

User query:

How transformers improved NLP?

Some documents may contain:

attention mechanism
BERT
encoder-decoder architecture
self attention

A single retrieval may miss many important documents.

Solution:

Multi Query Retrieval

But Multi Query Retrieval introduces a new problem.

# 2. What is Rank Fusion in RAG?
Definition

Rank Fusion is a retrieval technique that combines multiple ranked retrieval results into a single final ranking.

Simple definition:

Many rankings
      ↓
One final ranking
Goal

Combine evidence from multiple retrieval searches.

Example

Query Variants:

Query 1 → AI diagnosis
Query 2 → Medical AI systems
Query 3 → Healthcare machine learning

Each query produces different rankings.

Rank Fusion combines all rankings.

# 3. Why Rank Fusion is Needed

Multi-query retrieval generates:

Multiple query variations

which creates:

Multiple ranked document lists

Example:

Query 1 results:

Rank	Document
1	D1
2	D2
3	D3

Query 2 results:

Rank	Document
1	D3
2	D1
3	D4

Query 3 results:

Rank	Document
1	D2
2	D4
3	D5

Problem:

How do we combine these rankings?

Solution:

Rank Fusion
# 4. If We Have Multi Query Retriever Then Why Need Rank Fusion?

This is one of the most important interview questions.

Multi Query Retriever only performs:

Query Expansion

It does NOT decide:

Which document is globally best.

Multi Query Retriever gives:

Multiple independent rankings.

Rank Fusion performs:

Global ranking aggregation.

Therefore:

Component	Responsibility
Multi Query Retriever	Generate multiple perspectives
Rank Fusion	Merge rankings intelligently
# 5. How These Two Concepts Work Together
Step 1

Multi Query Retriever creates:

Q1
Q2
Q3
Q4
Step 2

Each query performs retrieval.

Step 3

Each retrieval creates:

Ranking List 1
Ranking List 2
Ranking List 3
Ranking List 4
Step 4

RRF combines them.

Step 5

Final ranking is generated.

Complete Flow
User Query
      ↓
LLM Query Expansion
      ↓

Q1 Q2 Q3 Q4

 ↓  ↓  ↓  ↓

R1 R2 R3 R4

 ↓  ↓  ↓  ↓

Reciprocal Rank Fusion

      ↓

Final Ranking

      ↓

LLM
# 6. What is RAG Fusion?

RAG Fusion is the combination of:

Multi Query Retrieval

and

Reciprocal Rank Fusion

Formula:

RAG Fusion =
Multi Query Retrieval
+
RRF
# 7. Components of RAG Fusion
A. Multi Query Retrieval

Purpose:

Generate multiple semantic variations.

B. Reciprocal Rank Fusion

Purpose:

Merge multiple rankings into one ranking.

# 8. What is Reciprocal Rank Fusion (RRF)?
Definition

Reciprocal Rank Fusion is a ranking aggregation algorithm that combines multiple ranked lists using reciprocal rank scores.

Simple definition:

Documents appearing consistently near the top receive higher scores.

Important idea:

Consistency matters more than one-time high rank.
# 9. Why RRF was Introduced

Suppose:

Document D1 appears:

Rank 1
Rank 2
Rank 3
Rank 2

Document D2 appears:

Rank 1
Not found
Not found
Not found

Which one is better?

Answer:

D1

because D1 consistently appears across multiple queries.

RRF captures this behavior.

# 10. RRF Formula

The RRF formula is: 1/(rank + kc)

# 11. Meaning of Each Term
Symbol	    Meaning
D	        Document
rank_i	    Rank of document in ith retrieval list
k_c	        Constant smoothing factor
Σ	        Sum across all rankings
Formula Meaning

Documents receive score from:

1 / (rank + k_c)

Higher rank:

rank = 1

gives larger contribution.

Lower rank:

rank = 20

gives smaller contribution.

12. Why This Formula Works

Suppose:

rank = 1

score:

Suppose:

rank = 10

score:

Notice:

Top positions receive higher reward.

But differences remain smooth.

This prevents:

Rank 1 dominating everything.
# 13. What is k_c Constant?

k_c is a smoothing constant.

Typically:

60

Purpose:

Reduce effect of rank differences.

Without k_c:

Rank1 → 1.0
Rank2 → 0.5
Rank3 → 0.33

Huge differences.

With k_c = 60

Rank1 → 0.01639
Rank2 → 0.01612
Rank3 → 0.01587

Differences become stable.

# 14. Effect of Changing k_c
Small k_c
Top ranks dominate.
Large k_c
Rank differences become smaller.
k_c	Effect
10	Aggressive ranking
30	Moderate ranking
60	Standard ranking
100	Smooth ranking
# 15. Numerical Example

Suppose:

Document rankings:

Document	Q1	Q2	Q3
D1	1	2	3
D2	2	5	-
D3	3	1	2

Assume:

k_c = 60
D1 Score
D2 Score
D3 Score

Final Ranking:

D1
D3
D2
Important Observation

D1 and D3 consistently appear in rankings.

Therefore they win.

# 16. How Re-Ranking Happens Using RRF

Workflow:

Multiple Rankings
      ↓
Calculate RRF Score
      ↓
Sort by RRF Score
      ↓
Final Ranking
# 17. Complete RAG Fusion Workflow
User Query
      ↓
LLM Query Expansion
      ↓

Q1 Q2 Q3 Q4

 ↓  ↓  ↓  ↓

Retriever
 ↓  ↓  ↓  ↓

Ranked Lists
 ↓  ↓  ↓  ↓

RRF Aggregation

      ↓

Final Ranking

      ↓

LLM Generation
# 18. Architecture Diagram
                Query
                  ↓
          Multi Query LLM
                  ↓

     ┌──────┬──────┬──────┐
     ↓      ↓      ↓      ↓
    Q1     Q2     Q3     Q4
     ↓      ↓      ↓      ↓
 Retriever Retriever Retriever
     ↓      ↓      ↓      ↓
    R1     R2     R3     R4
      \     |     |     /
       \    |     |    /
         Reciprocal
         Rank Fusion
              ↓
         Final Ranking
              ↓
              LLM
# 19. LangChain Implementation
Step 1

Create Multi Query Retriever

from langchain.retrievers.multi_query import MultiQueryRetriever

multi_query = MultiQueryRetriever.from_llm(
    retriever=base_retriever,
    llm=llm
)
Step 2

Generate alternative queries

queries = multi_query.generate_queries(
    "How transformers improved NLP?"
)
Step 3

Retrieve for each query

all_docs = []

for q in queries:
    docs = retriever.invoke(q)
    all_docs.append(docs)
Step 4

Apply RRF

from collections import defaultdict

scores = defaultdict(float)

k_c = 60

for docs in all_docs:
    for rank, doc in enumerate(docs):
        scores[doc.page_content] += (
            1 / (rank + 1 + k_c)
        )

final_docs = sorted(
    scores.items(),
    key=lambda x: x[1],
    reverse=True
)
# 20. LangGraph Workflow
from langgraph.graph import StateGraph

Generate Queries Node

def generate_queries(state):

    queries = multi_query.generate_queries(
        state["query"]
    )

    return {"queries": queries}

Retrieval Node

def retrieve(state):

    results = []

    for q in state["queries"]:
        docs = retriever.invoke(q)
        results.extend(docs)

    return {"docs": results}

Fusion Node

def rank_fusion(state):

    scores = {}

    k_c = 60

    for rank, doc in enumerate(
        state["docs"]
    ):
        score = 1/(rank+1+k_c)

        scores[doc.page_content] = (
            scores.get(
                doc.page_content,
                0
            ) + score
        )

    return {"scores": scores}

Workflow:

Generate Queries
       ↓
Retrieve Documents
       ↓
Rank Fusion
       ↓
Generation
# 21. Advantages and Disadvantages
## Advantages

✅ Better retrieval recall

✅ Better document coverage

✅ Reduces query wording issues

✅ Robust to retrieval failures

✅ Improves answer quality

## Disadvantages

❌ Higher latency

❌ More retrieval operations

❌ More computational cost

❌ Duplicate handling required

❌ More engineering complexity

# 22. Interview Questions
What is Rank Fusion?

Combining multiple retrieval rankings into one ranking.

What is RRF?

A ranking aggregation algorithm based on reciprocal rank scores.

Why use RRF instead of averaging similarity scores?

Ranks are more stable across retrieval systems than similarity values.

Why do we need RRF if Multi Query exists?

Multi Query creates rankings.
RRF merges rankings.

What is standard value of k_c?

Usually:

60
What happens if k_c becomes smaller?

Top ranks dominate more strongly.

What happens if k_c becomes larger?

Ranking becomes smoother.

# 23. Key Takeaways

✅ RAG Fusion = Multi Query Retrieval + RRF

✅ Multi Query improves recall.

✅ RRF improves ranking aggregation.

✅ Documents consistently appearing near top receive higher scores.

✅ k_c controls smoothing.

✅ RAG Fusion is one of the most powerful retrieval techniques in production RAG systems.

✅ Widely used in research assistants, enterprise search, legal AI and medical RAG systems.