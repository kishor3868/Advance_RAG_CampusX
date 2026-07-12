# RAGAS Metrics - Noise Sensitivity, Response Relevance & Faithfulness

# Table of Contents

1. Introduction
2. Why do we need Generator Metrics?
3. Noise Sensitivity
   - Definition
   - Why do we need it?
   - Formula
   - Step-by-Step Calculation
   - Example
   - Score Interpretation
   - LangChain/RAGAS Implementation
4. Response Relevance
   - Definition
   - Why do we need it?
   - Formula
   - Step-by-Step Calculation
   - Example
   - Score Interpretation
   - LangChain/RAGAS Implementation
5. Faithfulness
   - Definition
   - Why do we need it?
   - Formula
   - Step-by-Step Calculation
   - Example
   - Score Interpretation
   - LangChain/RAGAS Implementation
6. Comparison of All Three Metrics
7. Key Takeaways

---

# 1. Introduction

In the previous notes, we evaluated the **Retriever** using:

- Context Recall
- Context Precision

Now we move to the second component of the RAG pipeline:

# Generator (LLM)

Even if the retriever returns the correct documents, the LLM can still make mistakes.

Some common failures are:

- Hallucination
- Using irrelevant information (Noise)
- Giving an irrelevant answer
- Ignoring retrieved context

To evaluate these situations, RAGAS provides the following metrics:

- Noise Sensitivity
- Response Relevance
- Faithfulness

These metrics evaluate the **Generator (LLM)**.

---

# Generator Evaluation Pipeline

```
               User Query

                    │

                    ▼

               Retriever

                    │

                    ▼

          Retrieved Context

                    │

                    ▼

                 LLM

                    │

                    ▼

         Generated Response

                    │

                    ▼

             RAGAS Metrics

     ┌────────────┬───────────────┐

     ▼            ▼               ▼

Noise       Response        Faithfulness

Sensitivity Relevance
```

---

# 2. Why do we need Generator Metrics?

Suppose our retriever correctly retrieves:

```
Elon Musk is the CEO of Tesla.

Tesla headquarters is Austin.
```

But the LLM generates:

```
Tesla CEO is Tim Cook.
```

The retriever worked correctly.

The error occurred in the Generator.

Generator Metrics help us identify these failures.

---

# 3. Noise Sensitivity

## Definition

**Noise Sensitivity evaluates how much the LLM is misled by irrelevant chunks in the retrieved context.**

It measures the robustness of the LLM.

If irrelevant (noisy) documents influence the generated response,

Noise Sensitivity increases.

---

## Official Definition

> Noise Sensitivity measures how much incorrect information in the generated response comes from irrelevant retrieved context.

---

# Why do we need Noise Sensitivity?

Real-world retrievers rarely retrieve only relevant documents.

Example

```
Retriever Output

Relevant Chunk

Relevant Chunk

Noise

Noise
```

A good LLM should ignore the noisy chunks.

If it starts using them,

it is **sensitive to noise**.

---

# Formula

```
Noise Sensitivity

=

Number of Incorrect Claims

Attributed to Noise

-------------------------------------

Total Number of Claims

in Generated Response
```

---

# Understanding the Formula

Suppose

Generated Response contains

```
Claim 1

Claim 2

Claim 3

Claim 4
```

Out of these

```
Claim 3

Claim 4
```

came from noisy documents.

Then

```
Noise Sensitivity

=

2 / 4

=

0.5
```

---

# Step-by-Step Calculation

Question

```
When was the Eiffel Tower built and how tall is it?
```

---

## Reference Answer

```
The Eiffel Tower was built in 1889.

It is 330 metres tall.
```

Extracted Facts

```
Fact 1

Built in 1889

Fact 2

Height = 330 metres
```

---

## Retrieved Context

| Chunk | Type |
|-------|------|
| Eiffel Tower completed in 1889 | Relevant |
| Eiffel Tower stands 330 metres tall | Relevant |
| Louvre Museum attracts 9 million visitors | Noise |
| Arc de Triomphe is 50 metres tall | Noise |

---

## Generated Response

```
The Eiffel Tower was built in 1889.

It stands 330 metres tall.

It attracts 9 million visitors per year.

It is 50 metres tall.
```

---

## LLM extracts claims

Generated Claims

```
Built in 1889

330 metres

9 million visitors

50 metres
```

Correct Claims

```
Built in 1889

330 metres
```

Noise Claims

```
9 million visitors

50 metres
```

---

# Calculation

Incorrect claims due to noise

```
2
```

Total claims

```
4
```

Therefore,

```
Noise Sensitivity

=

2/4

=

0.5
```

---

# Interpretation

## Low Noise Sensitivity

```
≈0
```

Means

- LLM ignored irrelevant chunks.
- Very robust model.
- Better reasoning.

---

## High Noise Sensitivity

Means

- LLM trusted noisy context.
- More hallucination.
- Poor robustness.

---

# LangChain + RAGAS Example

```python
from ragas.metrics import noise_sensitivity
from ragas import evaluate

result = evaluate(
    dataset,
    metrics=[noise_sensitivity]
)

print(result)
```

---

# 4. Response Relevance

## Definition

**Response Relevance evaluates whether the generated answer actually answers the user's question.**

Notice

This metric **does not check correctness**.

It checks usefulness.

---

## Official Definition

> Response Relevance measures how relevant and useful the generated response is with respect to the user query.

---

# Why do we need Response Relevance?

Sometimes the answer is correct,

but does not answer the question.

Example

Question

```
Explain RAG.
```

Generated Response

```
Large Language Models are useful.
```

Although the statement is true,

it does not answer the question.

Therefore,

Response Relevance is low.

---

# Formula

Unlike Recall,

there is no simple mathematical formula.

RAGAS uses

LLM as Judge.

Conceptually,

```
Response Relevance

=

Semantic Similarity

between

User Query

and

Generated Response
```

Higher semantic similarity

↓

Higher relevance.

---

# Step-by-Step Calculation

Question

```
What is Graph RAG?
```

Generated Response

```
Graph RAG combines Knowledge Graphs with Retrieval-Augmented Generation to improve multi-hop reasoning.
```

Judge LLM checks

```
Does this answer the user's question?
```

If Yes

↓

High Score

---

Example

Question

```
What is Graph RAG?
```

Generated

```
Python is an interpreted language.
```

Correct sentence?

Yes.

Useful?

No.

Response Relevance

↓

Very Low

---

# Interpretation

## High Response Relevance

Means

- Directly answers the query.
- Useful.
- Focused.

---

## Low Response Relevance

Means

- Off-topic.
- Incomplete.
- Doesn't solve user problem.

---

# LangChain + RAGAS Example

```python
from ragas.metrics import answer_relevancy

result = evaluate(
    dataset,
    metrics=[answer_relevancy]
)

print(result)
```

---

# 5. Faithfulness

## Definition

**Faithfulness measures whether the generated response is fully supported by the retrieved context.**

It detects hallucinations.

Faithfulness evaluates whether every claim made in the generated response can be
traced back to the retrieved context. It is the primary hallucination detection
metric in RAGAS.
A response is considered faithful if the LLM only says things that are directly
supported by the retrieved chunks - it should not bring in outside knowledge, make
assumptions, or invent facts that areDot present in the context.


Faithfulness = Number of claims in response that are supported by retrieved context/Total number of claims in response



---

## Official Definition

> Faithfulness measures how factually consistent the generated response is with respect to the retrieved context.

---

# Why do we need Faithfulness?

Suppose

Retriever returns

```
Elon Musk is CEO of Tesla.
```

Generated

```
Elon Musk founded Tesla in 1990.
```

The retrieved context never mentioned

1990.

Therefore,

the response is **not faithful**.

---

# Formula

```
Faithfulness

=

Number of Supported Claims

---------------------------------

Total Claims

in Response
```

---

# Step-by-Step Calculation

Retrieved Context

```
Elon Musk is CEO of Tesla.

Tesla headquarters is Austin.
```

Generated Response

```
Elon Musk is CEO.

Tesla headquarters is Austin.

Tesla was founded in 1990.
```

Extract Claims

```
Claim 1

CEO

Supported

✓

Claim 2

Austin

Supported

✓

Claim 3

Founded in 1990

Not Supported

✗
```

---

Calculation

Supported Claims

```
2
```

Total Claims

```
3
```

Faithfulness

```
2/3

=

0.67
```

---

# Workflow

```
Retrieved Context

↓

Generated Response

↓

LLM extracts claims

↓

LLM verifies

Supported?

↓

Formula

↓

Faithfulness Score
```

---

# Interpretation

## High Faithfulness

Means

- No hallucination.
- Every statement supported by context.
- Trustworthy answer.

---

## Low Faithfulness

Means

- Hallucination.
- Unsupported claims.
- Dangerous in production.

---

# LangChain + RAGAS Example

```python
from ragas.metrics import faithfulness

result = evaluate(
    dataset,
    metrics=[faithfulness]
)

print(result)
```

---

# 6. Comparison of All Three Metrics

| Metric | Measures | Uses | High Score Means |
|---------|----------|------|------------------|
| Noise Sensitivity | How much noise influences the response | LLM Robustness | LLM ignores noisy chunks (Ideally Noise Sensitivity should be **low**, close to 0) |
| Response Relevance | Whether the response answers the user query | Usefulness | Highly relevant answer |
| Faithfulness | Whether the response is supported by retrieved context | Hallucination Detection | No hallucination |

---

# Visual Comparison

```
Retriever

↓

Retrieved Context

↓

LLM

↓

Generated Response

↓

Noise Sensitivity

↓

Was LLM distracted by Noise?

------------------------------------

Generated Response

↓

Response Relevance

↓

Did the answer solve the user's query?

------------------------------------

Generated Response

↓

Faithfulness

↓

Are all claims supported by context?
```

---

# Real-Life Analogy

Imagine a student answering an exam question.

### Noise Sensitivity

The student also reads irrelevant notes.

Question:

Did those irrelevant notes affect the answer?

---

### Response Relevance

Question:

Did the student answer the actual question?

---

### Faithfulness

Question:

Are all statements supported by the textbook?

Or

Did the student make up facts?

---

# Key Takeaways

- **Noise Sensitivity** measures how much the LLM is influenced by **irrelevant (noisy) retrieved context**. A lower score indicates a more robust LLM.
- **Response Relevance** measures whether the generated response actually answers the **user's query**, regardless of whether it is factually correct.
- **Faithfulness** measures whether every claim in the generated response is **supported by the retrieved context**, making it the primary metric for detecting hallucinations.
- All three metrics use **LLM as a Judge** to perform semantic evaluation rather than exact keyword matching.
- Together, these metrics evaluate different aspects of the **Generator (LLM)**:
  - **Noise Sensitivity** → Robustness against irrelevant information.
  - **Response Relevance** → Usefulness and query alignment.
  - **Faithfulness** → Groundedness and factual consistency.
- A high-quality RAG system should ideally have:
  - **Low Noise Sensitivity**
  - **High Response Relevance**
  - **High Faithfulness**