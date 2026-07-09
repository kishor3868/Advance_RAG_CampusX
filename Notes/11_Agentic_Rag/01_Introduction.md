# Introduction to Agentic RAG
# 1. Why Traditional RAG Was Not Enough?

Traditional RAG pipeline:

Query
  ↓
Retriever
  ↓
Retrieved Documents
  ↓
LLM
  ↓
Answer
Demerits of Traditional RAG
## 1. It cannot decide whether retrieval is needed or not.

Example:

Query: What is 2 + 2 ?

RAG still performs retrieval even though no external knowledge is required.

## 2. It cannot verify the relevancy of retrieved documents.

If retriever returns:

Football document
Cricket document

for an AI question, the LLM still tries to answer.

## . It cannot verify whether generated answer is grounded in documents.

The model may use:

Retrieved documents
Parametric memory (model knowledge)

and mix them together.

## 4. It cannot verify whether the answer is useful for the user query.

A technically correct answer may still not solve the user's problem.

Summary

Traditional RAG blindly follows:

Retrieve → Generate

without any validation.

# 2. Why CRAG Was Introduced?

CRAG introduced:

Retrieval Evaluator

to improve retrieval quality.

What CRAG Solved?

CRAG checks:

✅ Are retrieved documents correct?

and classifies retrieval as:

Correct
Ambiguous
Incorrect
Demerits of CRAG
## 1. It still assumes retrieval is always required.

Even simple factual questions go through retrieval.

## 2. Retrieval evaluator is fixed and rule-based.

Its behavior is predefined:

Correct → Refinement
Ambiguous → Refinement + Search
Incorrect → Search

There is no flexibility.

## 3. Pipeline remains static.

The execution path is already decided.

The system cannot dynamically decide:

Retry retrieval
Change retriever
Use SQL database
Use API
Skip retrieval
Summary

CRAG improves retrieval quality but:

Pipeline is still fixed.
# 3. Why Self-RAG Was Introduced?

Self-RAG introduced:

Self Reflection

inside the LLM.

Self-RAG checks:

1. Is retrieval needed?
2. Are retrieved documents relevant?
3. Is generated response grounded in retrieved evidence?
4. Is generated response useful to answer the query?

This is a major improvement over CRAG.

Demerits of Self-RAG
## 1. Reflection logic is still predefined.

The model follows fixed reflection steps such as:

Retrieve
Reflect
Generate
Support Check
Utility Check
## 2. Pipeline remains mostly static.

It cannot dynamically decide:

Use web search
Use SQL database
Call external API
Use calculator
Use code execution
Change retrieval strategy
## 3. Limited corrective actions.

If retrieval fails, options are limited to:

Retrieve again
Generate again
Summary

Self-RAG introduces intelligence but still lacks:

Dynamic reasoning and planning.
# 4. The Core Problem

Even after:

Traditional RAG
CRAG
Self-RAG

our pipeline still looks like:

Fixed workflow

The system cannot decide:

Should I retrieve?
Should I search the web?
Should I use SQL?
Should I rewrite the query?
Should I call an API?
Should I use another tool?
This Leads to Agentic RAG
# 5. What is Agentic RAG?

Agentic RAG replaces:

Fixed Pipelines

with

AI Agents

Instead of using:

Retrieval Evaluator
Reflection Module
Hardcoded Decision Logic

we use:

AI Agent

that can think and decide dynamically.

Traditional RAG
Retrieve → Generate
CRAG
Retrieve
   ↓
Evaluator
   ↓
Generate
Self-RAG
Retrieve
   ↓
Reflection
   ↓
Generate
Agentic RAG
Think
 ↓
Plan
 ↓
Choose Tools
 ↓
Execute
 ↓
Evaluate
 ↓
Retry if needed
 ↓
Generate
# 6. Properties of AI Agents
1. Autonomous

The agent performs:

Decision Making

without predefined flow.

Example:

Do I need retrieval?
Do I need web search?
Do I need SQL query?

The agent decides automatically.

2. Adaptive

The agent adapts when something fails.

Examples:

Retry retrieval
Use multiple retrieval sources
Use web search
Perform knowledge refinement
Change retrieval strategy

Example:

Vector DB failed
      ↓
Try Web Search
      ↓
Try API
      ↓
Combine Results
3. Dynamic

The workflow is no longer fixed.

The agent can:

Breakdown tasks

Example:

Compare Tesla and BYD revenue.

Agent may split into:

Find Tesla revenue
Find BYD revenue
Compare results
Generate answer
Rewrite queries

Example:

AI in hospitals

can become:

Applications of AI in healthcare
Medical diagnosis systems
Patient monitoring AI
Rewrite generation prompts

The agent may modify prompts dynamically based on:

User intent
Retrieved context
Missing information
Final Evolution of RAG
Traditional RAG
      ↓
CRAG
      ↓
Self-RAG
      ↓
Agentic RAG
One-Line Summary
RAG Type	Main Question
Traditional RAG	Retrieve and generate
CRAG	Are retrieved docs correct?
Self-RAG	Is retrieval useful and grounded?
Agentic RAG	What should I do next?

This final question:

What should I do next?

is the core idea behind Agentic RAG and is what makes AI agents fundamentally different from all previous RAG architectures.