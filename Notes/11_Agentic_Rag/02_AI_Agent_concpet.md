# What are AI Agents?
Simple Definition

An AI Agent is an intelligent system that can:

Understand a goal
Make decisions
Plan steps
Use tools
Remember previous actions
Adapt when something fails
Execute tasks autonomously
One-Line Definition

An AI Agent is an LLM-powered system that can think, decide, plan, act, observe results, and improve its actions to achieve a goal.

What do AI Agents actually do?

Traditional LLM:

Question → LLM → Answer

AI Agent:

Question
   ↓
Think
   ↓
Plan
   ↓
Choose Tool
   ↓
Execute Tool
   ↓
Observe Result
   ↓
Decide Next Step
   ↓
Generate Final Answer
Example 1: Traditional LLM vs AI Agent

User asks:

What is the weather in Bangalore tomorrow and should I carry an umbrella?
Traditional LLM

LLM may answer from old knowledge:

It may rain.

But it does not know current weather.

AI Agent

Agent workflow:

Step 1: Need real-time information?
          YES

Step 2: Call Weather API

Step 3: Receive forecast

Step 4: Analyze rain probability

Step 5: Generate recommendation

Final answer:

Rain probability is 85%.
You should carry an umbrella.

This is an AI Agent.

How do we call a system an AI Agent?

A system is generally considered an AI Agent if it contains three major components:

Brain
Memory
Tools
AI Agent Architecture
               USER QUERY
                    ↓
               ┌────────┐
               │ BRAIN  │
               │ (LLM)  │
               └────────┘
                    ↓
     ┌──────────────┼──────────────┐
     ↓                             ↓
 MEMORY                        TOOLS
(Database)                  (Actions)
     ↓                             ↓
Conversation              Web Search
History                   Calculator
Context                   SQL Query
Past Actions              API Calls
Tool Calls                Code Execution
## 1. Brain (LLM)

The brain is responsible for:

Thinking
Decision making
Planning
Reasoning
Choosing tools

Examples:

GPT models
Claude
Gemini
Llama
Mistral
Example

User asks:

Compare Tesla and BYD revenue in 2025.

The brain decides:

1. Need Tesla revenue
2. Need BYD revenue
3. Need comparison
4. Need final summary

The brain creates the roadmap.

Human Analogy
Brain → Human brain
## 2. Memory

Memory allows the agent to remember previous information.

Without memory:

Every interaction starts from zero.
What can memory store?
Context
Current conversation context
Conversation History

Example:

User: My budget is 50k.

Later:

Recommend laptops.

Agent remembers:

Budget = 50k
Previous Queries

Example:

User asked:
Best AI course

Later:

Recommend books for that course.
Tool Calls

Example:

Weather API called at 10 AM

Agent can reuse it.

Tool Results

Example:

Tesla revenue = 97 Billion

No need to call API again.

AI Responses

The agent remembers previous answers.

Retrieved Documents

Useful in Agentic RAG.

Memory Example

Without memory:

User:
My name is Kishor.

Later:
What is my name?

AI:
I don't know.

With memory:

AI:
Your name is Kishor.
Types of Memory
Short-Term Memory

Current conversation.

Long-Term Memory

Persistent storage:

Vector DB
SQL Database
Graph Database
Human Analogy
Memory → Human memory
## 3. Tools

Tools allow agents to interact with the outside world.

Examples:

Web Search
Calculator
SQL Database
Python Execution
Email
APIs
Vector Database
Weather API
Example

User:

What is 98765 × 76543 ?

LLM alone may make mistakes.

Agent decides:

Need calculator tool.

Tool executes:

98765 × 76543

Returns:

7555548895
Human Analogy
Tools → Human hands
Human vs AI Agent
Human	AI Agent
Brain	LLM
Memory	Database
Hands	Tools
# Properties of AI Agents
## 1. Autonomous
Definition

Ability to make decisions without human intervention.

Example

Query:

Find today's stock price of Tesla.

Agent decides:

Need web search.

No human tells it to use web search.

This is autonomy.

Traditional RAG
Retriever always called.
Agent
Do I need retrieval?

Decision made automatically.

## 2. Planning
Definition

Breaking a large problem into smaller steps.

Example

Query:

Compare Tesla and BYD revenue.

Plan:

Step 1: Find Tesla revenue
Step 2: Find BYD revenue
Step 3: Compare growth
Step 4: Generate answer

This roadmap is called:

Planning
Human Example

Before travelling:

Book ticket
Reserve hotel
Pack luggage
Travel
## 3. Reasoning
Definition

Thinking about how to execute each step.

Planning says:

Find Tesla revenue.

Reasoning asks:

Should I use web search?
Should I use financial API?
Should I use SQL database?

Reasoning decides:

Web search is best.
Example

User:

Which is better for gaming:
RTX 4060 or RTX 4070?

Reasoning process:

Need FPS comparison
Need price comparison
Need power usage comparison
Need final recommendation
Human Analogy
Planning = What to do
Reasoning = How to do it
## 4. Orchestration
Definition

Managing and coordinating multiple actions and tools.

Example:

Find weather
Find flight price
Find hotel price
Generate itinerary

Agent controls:

Weather API
Flight API
Hotel API
LLM

This coordination is:

Orchestration
Human Analogy

Project manager managing teams.

Example
User Query
     ↓
Web Search
     ↓
Calculator
     ↓
Database Query
     ↓
LLM Summary

Agent controls everything.

## 5. Adaptive
Definition

Ability to change strategy when something fails.

Example:

Vector DB retrieval failed.

Agent decides:

Try web search.

If web search fails:

Try SQL database.

If SQL fails:

Ask user for clarification.

This adaptability makes agents robust.

Adaptive = Dynamic + Robust + Error Recovery
Dynamic

Workflow changes depending on situation.

Robust

System continues working despite failures.

Error Recovery

The agent can retry or choose another path.

Example
Web Search Failed
      ↓
Retry
      ↓
Still Failed
      ↓
Use Database
      ↓
Generate Answer
Complete AI Agent Workflow
User Query
      ↓
Brain (LLM)
      ↓
Planning
      ↓
Reasoning
      ↓
Tool Selection
      ↓
Execution
      ↓
Observe Results
      ↓
Memory Update
      ↓
Final Answer
Final Example: Complete Agent

User asks:

Plan a 3-day trip to Goa under ₹20,000.

Agent performs:

1. Break task into subtasks
2. Search flights
3. Search hotels
4. Calculate budget
5. Build itinerary
6. Validate budget
7. Generate final plan

This system is clearly doing much more than simple retrieval.

This is an AI Agent.

Final One-Line Summary

An AI Agent is an LLM-powered system with Brain (LLM), Memory (Database), and Tools, capable of autonomous decision-making, planning, reasoning, orchestration, and adaptive execution to achieve a goal dynamically.