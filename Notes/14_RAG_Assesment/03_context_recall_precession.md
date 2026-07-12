# RAGAS Metrics - Context Recall & Context Precision

# Table of Contents

1. Introduction
2. Why do we need Retriever Metrics?
3. Context Recall
   - Definition
   - Why do we need it?
   - Formula
   - Step-by-Step Calculation
   - Comparison with Machine Learning Recall
   - Example
   - Score Interpretation
   - LangChain/RAGAS Implementation
4. Context Precision
   - Definition
   - Why do we need it?
   - Formula
   - Step-by-Step Calculation
   - Comparison with Machine Learning Precision
   - Example
   - Score Interpretation
   - LangChain/RAGAS Implementation
5. Context Recall vs Context Precision
6. Key Takeaways

---

# 1. Introduction

After building a RAG pipeline, one of the first questions we ask is:

> **Did my retriever retrieve the correct information?**

A retriever can fail in two ways:

1. It may **miss important information** (Low Recall).
2. It may **retrieve unnecessary or irrelevant information** (Low Precision).

To evaluate these two situations, RAGAS provides two important retriever metrics:

- Context Recall
- Context Precision

These metrics evaluate **only the Retriever**, not the LLM.

---

# Retriever Evaluation Pipeline

```
                 User Query

                     │

                     ▼

                Retriever

                     │

                     ▼

            Retrieved Context

                     │

             RAGAS Metrics

        ┌────────────┴────────────┐

        ▼                         ▼

 Context Recall            Context Precision
```

Notice that these metrics evaluate the **quality of the retrieved context**, not the generated response.

---

# 2. Why do we need Retriever Metrics?

Imagine the user asks

```
Who is the CEO of Tesla and where is Tesla headquartered?
```

Suppose our retriever returns

```
Chunk 1

Elon Musk is CEO of Tesla.

Chunk 2

Tesla headquarters is Austin.

Chunk 3

Apple headquarters is California.

Chunk 4

Python was created by Guido van Rossum.
```

Questions:

- Did the retriever fetch all required information?

(Context Recall)

- Did it fetch unnecessary information?

(Context Precision)

These two metrics answer these questions.

---

# 3. Context Recall

## Definition

**Context Recall evaluates whether the retriever fetched all the information needed to answer the question correctly.**

It checks for **completeness**.

In simple words,

> **Did the retriever miss any important information?**

---

## Official Definition

> Context Recall measures how many of the relevant pieces of information were successfully retrieved.

---

# What does Context Recall Measure?

It measures

```
Relevant Information

Retrieved?

YES

↓

High Recall

NO

↓

Low Recall
```

---

# Formula

```
Context Recall

=

Number of claims in Reference

that are supported by Retrieved Context

------------------------------------------------

Total Number of Claims

in Reference
```

Mathematically,

```
               Supported Claims

Recall = ----------------------------

         Total Reference Claims
```

---

# Understanding the Formula

Suppose

Reference Answer contains

```
Fact 1

Fact 2

Fact 3

Fact 4
```

Retrieved Context supports

```
Fact 1

Fact 2

Fact 3
```

Then

```
Recall

=

3 / 4

=

0.75
```

---

# How Context Recall is Calculated

One important thing to understand is:

RAGAS uses **LLM as a Judge**.

The LLM performs the following steps.

---

## Step 1

User asks a question.

Example

```
Who is the CEO of Tesla and where is Tesla headquartered?
```

---

## Step 2

We provide the **Reference Answer (Ground Truth)**

```
Elon Musk is the CEO of Tesla.

Tesla is headquartered in Austin.
```

This is our **Grounded Response**.

---

## Step 3

The Judge LLM extracts important facts (claims).

Reference

```
Elon Musk is CEO of Tesla.

Tesla headquarters is Austin.
```

Extracted Claims

```
Claim 1

CEO of Tesla = Elon Musk

Claim 2

Tesla Headquarters = Austin
```

---

## Step 4

Compare these claims with the Retrieved Context.

Retrieved Context

```
Chunk 1

Elon Musk is CEO of Tesla.

Chunk 2

Tesla was founded in 2003.
```

Notice

Claim 1 exists

Claim 2 missing

---

## Step 5

Calculate Recall

Supported Claims

```
1
```

Total Claims

```
2
```

Therefore

```
Recall

=

1 / 2

=

0.5
```

---

# Complete Workflow

```
User Query

↓

Reference Answer

↓

LLM extracts Facts

↓

Retrieved Context

↓

LLM checks

How many facts exist?

↓

Formula

↓

Recall Score
```

---

# Example

Question

```
Who founded Tesla and where is Tesla headquartered?
```

Reference

```
Tesla was founded by Martin Eberhard and Marc Tarpenning.

Tesla headquarters is Austin.
```

Extracted Claims

```
Fact 1

Founder

Fact 2

Headquarters
```

Retriever returns

```
Tesla headquarters is Austin.
```

Supported Facts

```
1
```

Total Facts

```
2
```

Recall

```
1/2

=

0.5
```

---

# Comparison with Machine Learning Recall

Machine Learning Recall

```
Recall

=

True Positive

-------------------

True Positive

+

False Negative
```

Question:

How many relevant samples did we correctly identify?

---

RAGAS Context Recall

```
Recall

=

Supported Claims

-----------------------

Reference Claims
```

Question:

How many important facts from the reference answer were successfully retrieved?

---

## Analogy

Machine Learning

```
Relevant Samples

↓

Retrieved?
```

RAGAS

```
Relevant Facts

↓

Retrieved?
```

The idea is exactly the same.

Only the unit changes.

---

# Interpretation

## High Recall

```
≈ 1
```

Means

- Retriever found almost everything.
- Very little information is missing.
- Good completeness.

---

## Low Recall

Means

- Important information is missing.
- Retriever failed.
- LLM may hallucinate because context is incomplete.

---

# LangChain + RAGAS Example

```python
from ragas import evaluate
from ragas.metrics import context_recall
from ragas import EvaluationDataset

sample = {
    "user_input": "Who is the CEO of Tesla?",
    "retrieved_contexts": [
        "Elon Musk is the CEO of Tesla."
    ],
    "reference":
    "Elon Musk is the CEO of Tesla."
}

dataset = EvaluationDataset.from_list([sample])

result = evaluate(
    dataset,
    metrics=[context_recall]
)

print(result)
```

---

# 4. Context Precision

## Definition

**Context Precision evaluates how much of the retrieved context is actually relevant to the user's question.**

Unlike Recall,

Precision measures

> **How much unnecessary information was retrieved?**

---
Context Precision evaluates whether the relevant chunks are ranked higher in the retrieved
context list. It is not just about whether the right chunks were retrieved - it is about where
they appear in the list.

Context Precision is a metric that evaluates the retriever's ability to rank relevant chunks
higher than irrelevant ones for a given query in the retrieved context. Specifically, it assesses
the degree to which relevant chunks in the retrieved context are placed at the top of the
ranking.

A

It is calculated as the mean of the precision@k for each chunk in the context. Precision@k
is the ratio of the number of relevant chunks at rank k to the total number of chunks at
rank k.


# Official Definition

Context Precision measures whether the retriever returns only the relevant information while avoiding irrelevant or noisy documents.

---

# What does Context Precision Measure?

Suppose the retriever returns

```
Relevant Chunk

Relevant Chunk

Relevant Chunk

Wikipedia

Random Article

Python Notes
```

Retriever retrieved

everything,

but much of it is irrelevant.

Precision becomes low.

---

# Formula

```
Context Precision@K = summation(from k=1 to K)(Precision@k ×vk)/
Total number of relevant chunks in top K

where
Precision@k = true positives@k/ true positives@k + false positivees@k


Context Precision

=

Relevant Retrieved Context

------------------------------------

Total Retrieved Context
```
What a low score tells you:

· Your retriever is fetching relevant chunks but burying them behind irrelevant ones
· The LLM ends up seeing noise before signal - which can hurt the quality of the
generated answer
· You may need to tune your similarity search, re-ranking strategy, or embedding model

What a high score tells you:

· Your retriever is correctly prioritizing the most relevant chunks at the top
· The LLM gets the best context first, giving it the best chance to generate a good answer



---

Suppose

Retriever fetched

```
Chunk 1

Relevant

Chunk 2

Relevant

Chunk 3

Noise

Chunk 4

Noise
```

Precision

```
2 / 4

=

0.5
```

---

# Step-by-Step Calculation

Suppose

Question

```
Who is the CEO of Tesla?
```

Retriever returns

```
Chunk 1

Elon Musk is CEO of Tesla.

Relevant

------------------

Chunk 2

Tesla headquarters Austin.

Relevant

------------------

Chunk 3

Python created by Guido.

Noise

------------------

Chunk 4

Apple revenue.

Noise
```

---

Judge LLM evaluates

Relevant Chunks

```
2
```

Retrieved Chunks

```
4
```

Precision

```
2 / 4

=

0.5
```

---

# Complete Workflow

```
User Query

↓

Retriever

↓

Retrieved Chunks

↓

Judge LLM

↓

Relevant?

Noise?

↓

Formula

↓

Precision Score
```

---

# Comparison with Machine Learning Precision

Machine Learning Precision

```
Precision

=

True Positive

-------------------

True Positive

+

False Positive
```

Question

How many predicted positives are actually correct?

---

RAGAS Context Precision

Question

How many retrieved chunks are actually relevant?

Same intuition.

Different domain.

---

# Interpretation

## High Precision

Means

- Retriever brings only useful documents.
- Very little noise.
- Faster reasoning.
- Better LLM performance.

---

## Low Precision

Means

- Lots of irrelevant documents.
- LLM receives noisy context.
- Increased hallucination risk.
- Increased token cost.

---

# LangChain + RAGAS Example

```python
from ragas import evaluate
from ragas.metrics import context_precision
from ragas import EvaluationDataset

sample = {
    "user_input":"Who is the CEO of Tesla?",
    "retrieved_contexts":[
        "Elon Musk is CEO of Tesla.",
        "Python was created by Guido."
    ],
    "reference":
    "Elon Musk is the CEO of Tesla."
}

dataset = EvaluationDataset.from_list([sample])

result = evaluate(
    dataset,
    metrics=[context_precision]
)

print(result)
```

---

# 5. Context Recall vs Context Precision

| Context Recall | Context Precision |
|----------------|-------------------|
| Measures completeness | Measures relevance |
| Checks whether important information was missed | Checks whether unnecessary information was retrieved |
| Higher Recall = Less missing information | Higher Precision = Less noise |
| Penalizes missing facts | Penalizes irrelevant facts |
| Focuses on Reference Answer | Focuses on Retrieved Context |

---

# Real-Life Analogy

Imagine you ask a librarian:

> "Give me books about Deep Learning."

### High Recall

The librarian gives you **all** the important Deep Learning books.

Nothing important is missing.

✔ High Recall

---

### Low Recall

The librarian gives you only one Deep Learning book and misses many important books.

❌ Low Recall

---

### High Precision

The librarian gives you only Deep Learning books.

✔ High Precision

---

### Low Precision

The librarian gives you:

- Deep Learning
- Cooking
- History
- Cricket
- Biology

along with one Deep Learning book.

❌ Low Precision

---

# Summary Diagram

```
                     Retriever

                         │

        ┌────────────────┴──────────────┐

        ▼                               ▼

Context Recall                 Context Precision

Did we retrieve             Did we retrieve

everything?                 only relevant data?

Missing Facts?              Noise?

Completeness                Relevance
```

---

# Key Takeaways

- **Context Recall** measures whether the retriever successfully fetched **all the information required** to answer the user's question. It focuses on **completeness**.
- **Context Precision** measures how much of the retrieved context is **actually relevant** to the user's query. It focuses on **relevance** and minimizing noise.
- Both metrics use **LLM as a Judge**, which extracts and compares semantic facts instead of relying on exact keyword matching.
- **Context Recall** is conceptually similar to **Machine Learning Recall**, as both measure how many relevant items were successfully retrieved.
- **Context Precision** is conceptually similar to **Machine Learning Precision**, as both measure how many retrieved items are actually relevant.
- **High Recall** means very little important information is missed, while **High Precision** means very little irrelevant information is retrieved.
- A good retriever should achieve **both high Context Recall and high Context Precision**, ensuring that it retrieves **all the necessary information without introducing unnecessary noise**.