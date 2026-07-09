# Agentic RAG — Detailed Notes
Table of Contents
Introduction
What is Agentic RAG?
Why Agentic RAG was introduced
AI Agents in Active Mode (Reasoning → Action Loop)
Properties of Agentic RAG
Query Understanding Phase
Dynamic Retrieval Selection
Retrieval Execution Phase
Verification Phase
Memory in Agentic RAG
Fallback Mechanism using Retry Logic
Complete Agentic RAG Workflow
LangChain/LangGraph Implementation
Advantages and Disadvantages
Interview Questions
Key Takeaways
# 1. Introduction

Traditional RAG pipeline:

Query
  ↓
Retriever
  ↓
Documents
  ↓
LLM

CRAG pipeline:

Retrieve
  ↓
Evaluator
  ↓
Generate

Self-RAG pipeline:

Retrieve
 ↓
Reflect
 ↓
Generate
 ↓
Reflect

All these pipelines are mostly:

Static Pipelines

The execution path is already fixed.

Agentic RAG changes this completely.

Instead of:

Fixed Workflow

we move to:

Dynamic Decision Making

powered by:

AI Agents
# 2. What is Agentic RAG?
Definition

Agentic RAG is a RAG architecture in which an AI Agent dynamically decides:

whether retrieval is required
how retrieval should happen
where retrieval should happen
whether retry is needed
whether answer is grounded
whether answer is useful

before producing the final response.

One-Line Definition
Agentic RAG =
RAG + AI Agent + Dynamic Decision Making
# 3. AI Agents are Always in Active Mode

Traditional pipeline:

Retrieve → Generate

Agentic RAG pipeline:

Reason
 ↓
Act
 ↓
Observe
 ↓
Reason Again
 ↓
Act Again

This loop is called:

Reasoning → Action Loop

or

ReAct Pattern
Example

User asks:

Compare Tesla and BYD revenue in 2025.

Agent thinking:

Need Tesla revenue.
Need BYD revenue.
Need financial source.
Need comparison.
Need final report.

This continuous thinking is the active mode of AI agents.

# 4. Properties of Agentic RAG

Agentic RAG mainly answers four questions:

WHAT ?
WHERE ?
HOW ?
WHEN ?

## WHAT
Query Understanding Layer

The agent first understands:

What should I do?
Example 1

Query:

What is 2 + 2 ?

Decision:

No retrieval required.
Example 2

Query:

What is latest Tesla stock price?

Decision:

Retrieval required.
Ambiguous Query Handling

Example:

Apple revenue

Question:

Apple company?
Apple fruit?
Apple India revenue?
Global revenue?

Agent may rewrite query.

Query Rewriting
Apple revenue

becomes:

Apple Inc annual revenue
Apple company revenue 2025
Multiple Query Generation

Agent may generate:

Q1
Q2
Q3

similar to Multi Query Retrieval.

Complex Query Decomposition

Example:

Compare Tesla and BYD revenue growth.

Agent breaks into:

Task 1:
Find Tesla revenue

Task 2:
Find BYD revenue

Task 3:
Compare growth

Task 4:
Generate report

## WHERE

This phase answers:

Where should I retrieve information from?

Unlike traditional RAG:

Always Vector DB

Agentic RAG dynamically chooses source.

Possible sources:

Vector DB
SQL Database
Web Search
API
Knowledge Graph
PDF Documents
Enterprise Search

Example:

Query:

Today's weather in Bangalore

Decision:

Weather API

Query:

Company policy on leave

Decision:

Enterprise Vector Database

Query:

Revenue of Tesla in 2025

Decision:

Web Search
Financial API

## HOW

This phase answers:

How should retrieval happen?

Agent decides:

Retriever Type

Examples:

Similarity Search
MMR
Hybrid Search
BM25
Self Query Retriever
Parent Retriever
Search Parameters

Agent may dynamically decide:

k = 5

or

k = 20

depending on complexity.

Example:

Simple query:

What is transformer architecture?

Decision:

k = 3

Complex query:

Applications of AI in healthcare.

Decision:

k = 15
fetch_k Selection

Agent may decide:

fetch_k = 50

for MMR retrieval.

Retrieval Strategy

Agent may choose:

Hybrid Search

instead of:

Vector Search
Retry Logic

Suppose retrieval quality is poor.

Agent decides:

Retry retrieval.

Possible retry strategies:

Increase k
Rewrite query
Change retriever
Switch source
Use web search
Example
Vector DB failed
     ↓
Web Search
     ↓
Hybrid Search
     ↓
Final Retrieval
# 5. Steps in Agentic RAG

Complete workflow:

Query
 ↓
Need Retrieval?
 ↓
Query Decomposition
 ↓
Query Rewriting
 ↓
Source Selection
 ↓
Retriever Selection
 ↓
Search Parameter Selection
 ↓
Retrieval
 ↓
Retry if needed
 ↓
Relevance Check
 ↓
Generation
 ↓
Grounding Verification
 ↓
Utility Verification
 ↓
Final Answer
Step 1: Select Retrieval

Example:

Need Retrieval?

Decision:

YES

or

NO
Step 2: Query Decomposition

Complex queries are broken into subtasks.

Step 3: Query Rephrasing

Query becomes optimized for retrieval.

Step 4: Source Selection

Agent chooses source dynamically.

Step 5: Retrieval Strategy Selection

Agent chooses:

MMR
BM25
Hybrid Search
Self Query Retriever
Step 6: Retry Logic

If retrieval quality is poor:

Retry
Step 7: Relevancy Check

Agent verifies:

Are retrieved docs useful?
Step 8: Grounding Verification

Question:

Is answer supported by evidence?
Step 9: Utility Verification

Question:

Does answer satisfy user intent?
# 6. Memory in Agentic RAG

Memory is one of the most important concepts in Agentic RAG.

Short-Term Memory

Stores:

Current query
Retrieved results
Intermediate reasoning
Conversation history
Tool calls
Tool results

Example:

User:
Recommend laptops under 50k.

Later:

User:
Only show gaming options.

Agent remembers:

Budget = 50k
Memory Flow
Query
 ↓
Memory
 ↓
Retriever
 ↓
Retrieved Results
 ↓
Memory Update
Stored Information
Conversation History
Query History
Retrieved Documents
Tool Calls
Tool Results
Agent Reasoning
Final Response
Memory Architecture
User Query
     ↓
Short-Term Memory
     ↓
Retriever
     ↓
Retrieved Documents
     ↓
Generation
     ↓
Memory Updated
# 7. Fallback Mechanism in Agentic RAG

One of the strongest features of Agentic RAG.

Suppose:

Vector Search Failed

Agent may perform:

Retry 1:
Increase k

If still failed:

Retry 2:
Rewrite Query

If still failed:

Retry 3:
Web Search

If still failed:

Retry 4:
Hybrid Search

Example:

User Query
    ↓
Vector Search
    ↓
Low Confidence
    ↓
Query Rewrite
    ↓
Vector Search
    ↓
Still Low Confidence
    ↓
Web Search
    ↓
Answer

This retry mechanism makes Agentic RAG:

Robust
Adaptive
Dynamic
# 8. Complete Agentic RAG Architecture
                    Query
                      ↓
              Need Retrieval?
                ↓        ↓
               No       Yes
               ↓         ↓
             Answer   Query Analysis
                        ↓
              Query Rewrite/Decompose
                        ↓
                 Source Selection
                        ↓
               Retriever Selection
                        ↓
                  Retrieval
                        ↓
                 Quality Check
                        ↓
                  Retry Needed?
                    ↓       ↓
                   Yes      No
                    ↓        ↓
                 Retry     Generate
                              ↓
                     Grounding Check
                              ↓
                       Utility Check
                              ↓
                        Final Answer
# 9. LangChain Example
Dynamic Source Selection
if query_type == "weather":
    tool = weather_api

elif query_type == "company_policy":
    tool = vector_db

elif query_type == "financial":
    tool = web_search
Retry Logic
docs = retriever.invoke(query)

if len(docs) < 3:
    query = rewrite_query(query)
    docs = retriever.invoke(query)
# 10. LangGraph Architecture
Query
 ↓
Planner Node
 ↓
Retriever Selector Node
 ↓
Retriever Node
 ↓
Evaluator Node
 ↓
Retry Node
 ↓
Generator Node
 ↓
Verifier Node
 ↓
Final Answer
# 11. Key Takeaways

✅ Agentic RAG is powered by AI Agents.

✅ Agents operate continuously in:

Reason → Action → Observation

loop.

✅ Agent decides:

What
Where
How
When

retrieval should happen.

✅ Agentic RAG introduces:

Dynamic retrieval
Query decomposition
Query rewriting
Retry mechanisms
Memory
Grounding verification
Utility verification

✅ Agentic RAG is currently considered one of the most advanced RAG architectures used in production AI systems.