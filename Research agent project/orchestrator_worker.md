A. Worker roles 
1. Research/search worker — runs web search + fetch, returns raw findings with source metadata
2. Retrieval/RAG worker — queries a local corpus or uploaded files (embeddings/index), returns relevant chunks + provenance
3. Source analysis worker — deep-reads a single URL/doc, extracts structured claims/evidence from it (as opposed to a broad search)
4. Critique worker — reviews evidence for gaps, bias, contradictions, weak sourcing; doesn't add new evidence
5. Citation verifier — cross-checks a specific claim against an independent source before it ships
6. Writing/formatting worker — turns merged evidence into report prose/sections (note: your CLAUDE.md says the orchestrator synthesizes — so this role, if it exists separately, would be for final formatting/style passes, not merging evidence)

B. Execution patterns

1. Pure function / single-shot worker — one prompt, one tool call (or none), structured output. Simplest, cheapest, easiest to test with fakes.
2. Fixed pipeline worker — hardcoded stage sequence (e.g., fetch → clean → extract → summarize). No LLM-driven branching; predictable cost/latency.
3. Tool-calling agentic worker — its own small agent loop (plan → call tool → observe → repeat until done or budget hit). Needed when the worker must decide how many searches or pages to look at.
4. Plan-execute-reflect worker — agentic loop plus a self-check step before returning (e.g., "did I actually answer the sub-question?"). More expensive; usually reserved for high-value or ambiguous sub-tasks.

More details:
┌─────┬───────────────────────┬──────────────────────────────────────────────┬─────────────────────┬─────────────────────────────────────────────┬──────────────────────────────────────────────────┐
│  #  │        Pattern        │                Internal shape                │      Uses LLM?      │             Best-fit capability             │                  Main risk/cost                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 1   │ Rule-based            │ Pure code, no reasoning step                 │ No                  │ formatter, simple fetch-by-known-API tasks  │ None architecturally — but can't handle anything │
│     │                       │                                              │                     │                                             │  not hardcoded                                   │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│     │                       │ Reads prior evidence from                    │                     │                                             │ Staleness — must know when cached evidence no    │
│ 2   │ Cache/lookup          │ context_store/workspace instead of doing new │ No                  │ Follow-up jobs reusing earlier evidence     │ longer applies                                   │
│     │                       │  work                                        │                     │                                             │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 3   │ Human-in-the-loop     │ Pauses and asks the user                     │ No (human is the    │ Ambiguous judgment calls, explicit          │ Blocks the run; needs control/human_in_loop.py   │
│     │                       │                                              │ "model")            │ escalation points                           │ wiring                                           │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 4   │ External service      │ Wraps a third-party black-box API, reshapes  │ Opaque to you       │ Anything a hosted service already does well │ No visibility into its internal loop or cost;    │
│     │ delegation            │ its output into WorkerResult                 │                     │                                             │ you can't trace it                               │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 5   │ Single-shot RAG       │ One retrieval step, one LLM call over the    │ Yes, 1 call         │ source_analysis on a small known set of     │ No multi-hop — can't decide to search again      │
│     │                       │ retrieved content                            │                     │ URLs                                        │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 6   │ Deterministic         │ Fixed function chain; LLM called only at     │ Yes, N fixed calls  │ citation_verification                       │ Can't adapt when the fixed steps don't fit the   │
│     │ pipeline              │ specific steps for judgment                  │                     │                                             │ input                                            │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 7   │ Generate–critique     │ One call generates, another critiques,       │ Yes, loop of 2      │ critique, or as a quality gate on writing   │ Can loop without converging if not budget-capped │
│     │ loop                  │ repeat until convergence/budget              │ alternating calls   │                                             │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 8   │ Ensemble /            │ Same sub-task run N times independently,     │ Yes, N parallel     │ Reliability-critical judgments (source      │ N× cost for the reliability gain                 │
│     │ self-consistency      │ merged by vote/confidence                    │ calls               │ quality labeling, entailment checks)        │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│     │                       │ One LLM call produces a full step sequence,  │ Yes, 1 planning     │ Medium-complexity tasks that need adapting  │ Can't recover if reality diverges from the plan  │
│ 9   │ Plan-then-execute     │ code executes it mechanically                │ call + fixed        │ to input but not re-planning mid-flight     │ partway through                                  │
│     │                       │                                              │ execution           │                                             │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│     │ Graph/state-machine   │ Explicit internal graph with conditional     │ Yes, 1 call per     │ When you want adaptiveness but strict       │                                                  │
│ 10  │ worker                │ branches; code picks the next node from the  │ node                │ boundedness/inspectability                  │ More upfront design work to enumerate branches   │
│     │                       │ LLM's output, not the LLM itself             │                     │                                             │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 11  │ Bounded ReAct /       │ Think → call a tool → observe → repeat,      │ Yes, loop, model    │ research — genuinely needs multi-hop search │ Must self-enforce its own step/budget cap or it  │
│     │ tool-use loop         │ until done or step cap hit                   │ chooses each step   │                                             │ silently overspends                              │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│ 12  │ Recursive             │ The worker is itself a mini orchestrator     │ Yes, nested         │ Only if one capability needs internal       │ Unbounded recursion risk without hard            │
│     │ sub-orchestrator      │ fanning out its own sub-tasks in parallel    │                     │ parallelism                                 │ depth/budget caps                                │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│     │                       │ Inspects the incoming task and picks one of  │                     │ A capability whose difficulty varies a lot  │ Adds a routing decision that itself needs        │
│ 13  │ Adaptive/router       │ the other 12 patterns dynamically            │ Varies              │ task-to-task (e.g. research: sometimes      │ testing/tracing                                  │
│     │                       │                                              │                     │ 1-hop, sometimes 5-hop)                     │                                                  │
├─────┼───────────────────────┼──────────────────────────────────────────────┼─────────────────────┼─────────────────────────────────────────────┼──────────────────────────────────────────────────┤
│     │ Provider-hosted agent │ Delegates the entire loop to a vendor's      │                     │                                             │ Single-vendor lock-in — conflicts with the       │
│ 14  │  SDK                  │ built-in agent runtime (Claude Agent SDK,    │ Yes, vendor-managed │ Fast prototyping only                       │ project's provider-neutral requirement (already  │
│     │                       │ OpenAI Agents SDK)                           │                     │                                             │ ruled out for the orchestrator in ADR_001)       │
└─────┴───────────────────────┴──────────────────────────────────────────────┴─────────────────────┴─────────────────────────────────────────────┴──────────────────────────────────────────────────┘

C. Cross-cutting design dimensions
- Contract — strict input/output schema (task spec in; evidence + sources + confidence + limitations out), matching communication/contracts.py
- Capability/tool access — which tools each worker type is allowed to call (search API, browser fetch, RAG index, none) — this is what capability_registry.py and worker_registry.py are for
- Statefulness — stateless per-task (safe for parallelism) vs. maintains scratchpad across retries within one job
- Isolation — each worker call gets its own clean context window, no shared mutable state (matches your "each research job has a clean, explicit context" rule)
- Model/provider binding — worker declares what it needs (reasoning vs. cheap extraction), provider boundary picks the model — keeps workers swappable
- Failure handling — does the worker retry internally, or does it fail fast and let the orchestrator's retry_policy/circuit_breaker handle it? (Your stubs suggest orchestrator-owned resilience — workers stay simple.)
- Budget enforcement — token/time/source caps passed into the worker call, not self-imposed, so the orchestrator's budget tracker stays authoritative