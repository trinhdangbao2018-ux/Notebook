Source: http://platform.claude.com/cookbook/patterns-agents-basic-workflows

There are three common patterns for organizing multiple LLM calls:
1, Chaining: The output of one LLM call is passed as input or context to the next call.
2, Parallelization: Several independent LLM calls run concurrently and process their tasks separately.
3, Routing: One LLM call classifies the input and directs it to the most suitable prompt, model, tool, or agent.

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