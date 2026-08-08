Source: http://platform.claude.com/cookbook/patterns-agents-basic-workflows
Source: https://www.anthropic.com/engineering/building-effective-agents

                  │  no cycle              │  has cycle
──────────────────┼────────────────────────┼──────────────────────────
 shape fixed      │  chaining              │  (rare — a fixed-count
 by you           │  parallelization       │   refine loop, e.g.
                  │  routing               │   "draft, then revise once")
──────────────────┼────────────────────────┼──────────────────────────
 shape decided    │  orchestrator-workers  │  evaluator-optimizer
 at runtime       │                        │  agent tool-loop

Augmented LLM: a LLM with retrieval, tools, memory
----------------------------------------------------------------------------------------------------
There are a few common patterns for workflow:     
1, Chaining: The output of one LLM call is passed as input or context to the next call.
2, Parallelization: Several independent LLM calls run concurrently and process their tasks separately.
3, Routing: One LLM call classifies the input and directs it to the most suitable prompt, model, tool, or agent.
4, Orchestrator-workers: An Orchestration LLM split the problem and create subtask after that. Routing have subtasks before that, while Orchestrator read problems then create subtask
5, Evaluator-optimizer: 2 model: generator model and evaluator model; every time generator model generate an answer, it goes though the evaluator model; if the answer satisfy, return to user, else, send back to generator model
---------------------------------------------------------------------------------------------------
Agents: LLM plan, use tool, check environment output, and continue until finish or stop by harness, more autonomous than workflow

They can also be combine:
Input
  ↓
Routing
  ↓
Selected branch
  ↓
Parallel analysis
  ↓
Chain of refinement steps
  ↓
Final answer