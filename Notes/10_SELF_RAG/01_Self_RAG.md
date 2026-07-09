# Self-RAG (Self-Reflective Retrieval Augmented Generation) — Detailed Notes
Table of Contents
Introduction
Problem with Traditional RAG
Why Self-RAG was Introduced
What is Self-RAG?
Difference Between Traditional RAG and Self-RAG
Main Idea Behind Self-RAG
Reflection Tokens in Self-RAG
Retrieval Reflection
Generation Reflection
Utility Reflection
Complete Self-RAG Workflow
Fully Supported, Partially Supported and No Support
Retrieval Loop in Self-RAG
Final Answer Validation Loop
Architecture Diagram
LangChain Implementation
LangGraph Architecture
Advantages and Disadvantages
Interview Questions
Key Takeaways
# 1. Introduction

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

The biggest assumption here is:

The retrieved documents are correct and sufficient.

Unfortunately this assumption is often wrong.

Example

User Query:

What are the applications of AI in healthcare?

Retriever returns:

d1 -> AI diagnosis systems
d2 -> AI in hospital management

But the query actually requires:

diagnosis
medical imaging
drug discovery
patient monitoring
hospital automation

Traditional RAG still generates answer from:

d1 + d2

even though important information is missing.

Problem

Traditional RAG never asks:

Are these retrieved documents actually enough?

Self-RAG was introduced to solve exactly this problem.

# 2. Why Self-RAG was Introduced

The main motivation behind Self-RAG is:

Self Verification

before trusting retrieved information.

Researchers observed:

Traditional RAG performs:

Retrieve
Generate
Finish

There is no:

retrieval quality check
evidence verification
answer validation
usefulness evaluation

Self-RAG introduces:

Reflection

inside the generation process.

# 3. What is Self-RAG?
Definition

Self-RAG is a retrieval-augmented generation framework in which the LLM continuously reflects on the quality of retrieved documents and generated responses before producing the final answer.

Simple definition:

Retrieve
↓
Reflect
↓
Generate
↓
Reflect Again
↓
Improve
↓
Final Answer

Unlike traditional RAG,

Self-RAG does not blindly trust retrieval.

# 4. Difference Between Traditional RAG and Self-RAG
Traditional RAG
Query
 ↓
Retrieve
 ↓
Generate
Self-RAG
Query
 ↓
Retrieve
 ↓
Evaluate Retrieval
 ↓
Generate
 ↓
Evaluate Answer
 ↓
Generate Final Response
# 5. Main Idea Behind Self-RAG

Self-RAG asks two questions:

Question 1
Are retrieved documents useful?
Question 2
Is generated answer useful for the user query?

This creates:

Retrieval Reflection

and

Generation Reflection
# 6. Reflection Tokens in Self-RAG

The original Self-RAG paper introduced special tokens:

Examples:

[Retrieve]
[Relevant]
[Irrelevant]
[Fully Supported]
[Partially Supported]
[No Support]
[Useful]
[Not Useful]

These tokens guide model reasoning.

# 7. Retrieval Reflection

This is the first reflection stage.

After retrieval:

Query
 ↓
d1
d2
d3

The LLM evaluates each retrieved document:

Can this document help answer the query?

Example:

Query:

Applications of AI in healthcare

Retrieved docs:

d1 -> diagnosis systems
d2 -> football news
d3 -> medical imaging

Reflection result:

Document	Decision
d1	Relevant
d2	Irrelevant
d3	Relevant

Only relevant documents survive.

# 8. Example of Retrieval Reflection

Query:

How transformers changed NLP?

Retrieved documents:

d1 -> Attention mechanism
d2 -> BERT architecture
d3 -> Cricket news

LLM evaluates:

d1 -> useful
d2 -> useful
d3 -> useless

Document d3 is removed.

# 9. Fully Supported, Partially Supported and No Support

This is one of the MOST IMPORTANT concepts in Self-RAG.

After generation, Self-RAG asks:

Is this answer supported by retrieved evidence?
Fully Supported

The answer is completely grounded in retrieved documents.

Example:

Query:

Who proposed transformers?

Retrieved document:

Attention Is All You Need introduced Transformers in 2017.

Generated answer:

Transformers were introduced in 2017 in Attention Is All You Need paper.

Decision:

Fully Supported
Partially Supported

Some parts come from retrieval.

Some parts come from model memory.

Example:

Retrieved document:

Transformers introduced in 2017.

Generated answer:

Transformers introduced in 2017 and revolutionized NLP worldwide.

"introduced in 2017" is supported.

"revolutionized NLP worldwide" may not exist in documents.

Decision:

Partially Supported
No Support

Generated answer contains information absent from retrieval.

Example:

Retrieved document:

Transformers introduced in 2017.

Generated answer:

Transformers were developed by Google in 2016.

Decision:

No Support
# 10. Retrieval Loop

This is where Self-RAG becomes powerful.

Suppose retrieval support is:

No Support

Self-RAG performs:

Retrieve Again

Workflow:

Retrieve
 ↓
Generate
 ↓
Support Check

No Support?
 ↓
Retrieve Again
 ↓
Generate Again

The loop continues until:

Fully Supported

or

Maximum iterations reached
# 11. Generation Utility Reflection

Even if answer is factually correct,

it may still be useless.

Example:

Query:

Best laptop under 50k

Answer:

A laptop is a portable computer.

Factually correct?

YES

Useful?

NO

Self-RAG introduces:

Utility Check

Question:

Does answer satisfy user intent?
# 12. Utility Reflection Example

Query:

Best laptop under 50k

Answer:

Laptops are portable computers.

Utility score:

Not Useful

Self-RAG generates again.

# 13. Complete Self-RAG Workflow
User Query
      ↓
Retrieve Documents
      ↓
Evaluate Each Document
      ↓
Keep Relevant Documents
      ↓
Generate Answer
      ↓
Support Evaluation

Fully Supported?
     ↓

YES → Utility Evaluation

NO → Retrieve Again

Utility Useful?

YES → Final Answer

NO → Generate Again
# 14. Complete Architecture Diagram
                     Query
                       ↓
                 Retriever
                       ↓
              d1   d2   d3
                       ↓
            Retrieval Reflection
                       ↓
      Relevant? Useful? Supported?
                       ↓
              Filter Documents
                       ↓
                  Generator
                       ↓
                Generated Answer
                       ↓
               Support Reflection
                       ↓
     ┌──────────────────────────┐
     │ Fully Supported          │
     │ Partially Supported      │
     │ No Support               │
     └──────────────────────────┘
                       ↓
               Utility Reflection
                       ↓
          Useful ?      Not Useful ?
              ↓              ↓
         Final Answer    Generate Again
# 15. How Self-RAG is Different From CRAG
Feature	CRAG	Self-RAG
Retrieval Evaluator	Separate model	Same LLM
Uses Web Search	Yes	Usually No
Reflection Tokens	No	Yes
Self Verification	Partial	Extensive
Retrieval Loop	Limited	Iterative
# 16. LangChain Implementation
Retrieval
retrieved_docs = retriever.invoke(query)
Retrieval Reflection Prompt
reflection_prompt = """
Query:
{query}

Retrieved Document:
{document}

Can this document help answer the query?

Return:
Relevant
or
Irrelevant
"""
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4.1-mini"
)

reflection_chain = (
    ChatPromptTemplate.from_template(
        reflection_prompt
    )
    | llm
)
Support Evaluation
support_prompt = """
Question:
{query}

Retrieved Context:
{docs}

Generated Answer:
{answer}

Classify:

Fully Supported
Partially Supported
No Support
"""
Utility Evaluation
utility_prompt = """
Question:
{query}

Generated Answer:
{answer}

Is this answer useful?

Useful
or
Not Useful
"""
# 17. LangGraph Architecture

This is where LangGraph becomes extremely useful.

                Query
                  ↓
             Retrieve Node
                  ↓
         Retrieval Reflection
                  ↓
         Relevant Documents
                  ↓
            Generation Node
                  ↓
         Support Evaluation
                  ↓
       Fully Supported?
         ↓          ↓
        Yes         No
         ↓          ↓
 Utility Eval   Retrieve Again
         ↓
 Useful?
   ↓      ↓
 Yes      No
 ↓         ↓
Final   Generate Again
LangGraph Pseudocode
graph = StateGraph(State)

graph.add_node(
    "retrieve",
    retrieve
)

graph.add_node(
    "reflection",
    reflection
)

graph.add_node(
    "generate",
    generate
)

graph.add_node(
    "support_check",
    support_check
)

graph.add_node(
    "utility_check",
    utility_check
)
# 18. Advantages

✅ Reduced hallucination

✅ Better grounding

✅ Self verification

✅ Improved factual accuracy

✅ Better answer usefulness

✅ Dynamic retrieval loop

# 19. Disadvantages

❌ More LLM calls

❌ Higher latency

❌ Higher cost

❌ More complex architecture

❌ Multiple iterations increase response time

# 20. Interview Questions
Why was Self-RAG introduced?

To make RAG systems verify retrieval and generated answers before responding.

What are reflection tokens?

Special control tokens used for retrieval and answer evaluation.

What are the three support categories?
Fully Supported
Partially Supported
No Support
What happens in No Support?

The system retrieves again.

What happens after support evaluation?

Utility evaluation is performed.

What is utility reflection?

Checking whether the answer is actually useful to the user.

# 21. Key Takeaways

✅ Traditional RAG trusts retrieval blindly.

✅ Self-RAG introduces self reflection.

✅ The model evaluates retrieved documents.

✅ The model evaluates generated answers.

✅ Self-RAG uses:

Fully Supported
Partially Supported
No Support

for grounding verification.

✅ Utility checking ensures answer usefulness.

✅ Self-RAG is one of the most advanced retrieval architectures in modern RAG systems.