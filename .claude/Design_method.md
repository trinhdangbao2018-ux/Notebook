## A sentence to remember:
1, Everything in architechture is a tradeoff: If you find something that have no tradeoff, that mean you haven't see the tradeoff yet
2, Why is more important than how: You can read code to know the how, but never the why -> this why we need an ADR

## Choose everythin by there tradeoff:
Risk = probability × impact.
Three steps, repeated: (1) list and rank risks → (2) pick techniques that target those specific risks → (3) evaluate the remaining risk.

Stopping rule: stop when the risk of technical failure has dropped below your non-technical risks (building the wrong thing, running out of time, losing momentum).

Have to write down the drawback probabilities Board:
    ┌─────────────────────┬──────────────────────────────────────────────────────────┐
    │  Useless statement  │                     Usable statement                     │
    ├─────────────────────┼──────────────────────────────────────────────────────────┤
    │ "I don't know the   │ "Wrong chunk size → low hit@5. Impact: medium, one file  │
    │ right way to chunk" │ to fix. Probability: high. → 1-hour spike, measure"      │
    ├─────────────────────┼──────────────────────────────────────────────────────────┤
    │ "Not sure the LLM   │ "If llama3.2 ignores [n] → the entire answer-generation  │
    │ cites sources       │ design has to be redone. Impact: very high. Probability: │
    │ properly"           │  unknown → spike this first"                             │
    └─────────────────────┴──────────────────────────────────────────────────────────┘

Drawbacks probabilities board -> Technique:
Fairbanks board:
    ┌───────────────────────────┬───────────────────────────────┬────────────────────┐
    │       Risk you have       │   Technique that targets it   │  What it leaves    │
    │                           │                               │       behind       │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Don't know whether the    │ Time-boxed spike; walking     │ Mechanism notes +  │
    │ technology can do this at │ skeleton                      │ 1 ADR              │
    │  all                      │                               │                    │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Don't know what "good     │ Quality attribute scenario    │ 1 scenario         │
    │ enough" means             │ with numbers; golden set      │ sentence +         │
    │                           │                               │ golden.json        │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Afraid of choosing wrong  │ ADR; last responsible moment  │ ADR in docs/adr/   │
    │ when it's hard to change  │ principle                     │                    │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Getting lost in the       │ Named types; C4 level 2–3     │ types.py + 1       │
    │ dataflow (your symptom)   │ diagram; dataflow comments    │ diagram            │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Accidentally breaking     │ Fitness function (automated   │                    │
    │ something that already    │ check for an architectural    │ Test in CI         │
    │ worked                    │ property)                     │                    │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Too slow / too much RAM / │ Scenario with numbers +       │ Baseline           │
    │  too expensive            │ measure on the skeleton       │ measurements       │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Misunderstanding the      │ Ubiquitous language (shared   │ Glossary + usage   │
    │ problem                   │ vocabulary) + glossary; write │ example            │
    │                           │  the usage first              │                    │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Code rotting, tangled     │ Dependency rule, ports &      │ One rule line in   │
    │ dependencies              │ adapters                      │ ADR-0001           │
    ├───────────────────────────┼───────────────────────────────┼────────────────────┤
    │ Someone else (or you in 6 │ Design doc; Diátaxis (split   │ Design doc /       │
    │  months) can't follow it  │ docs by 4 purposes)           │ separating         │
    │                           │                               │ notes-vs-ADR       │
    └───────────────────────────┴───────────────────────────────┴────────────────────┘
Way to use: List out the risk, then check the board. Never run everything in the middle

## Quality attribute scenario:
We called thing that are fast, correct, offline is architectural characteristic or quality attribute. Stuff like -"ility"- ending word: Scalability, testability, availability, .... They define how good the system perform.

    1, This attribute decide the structure, not the function: the ADD(attribute driven design) split the system by choosing the design that suit the needed attribute.
    2, Choose one or two of thoose: Every attribute you commit often hurt the others. Choose 7 equal to choose none

The way to write them so they're actually usable: a quality attribute scenario, six parts — source of stimulus, stimulus, artifact affected, environment, response, and response measure.

## What to write: scaled by risk:
Tier 0: Small script, no risks
A section in Readme.md: the decision recorded as y-statement the one sentence ADR format. Strait from adr.github.io:
    In the context of <use case>, facing <concern>, we decided for <option> to achieve <quality>, accepting <downside>.

Tier 1: A project
    This is your tier. Seven artifacts, now with their industry names:

    ┌─────┬─────────────────────────┬─────────────────────────┬──────────────────────┐
    │  #  │        Artifact         │      Industry name      │   The question it    │
    │     │                         │                         │ forces you to answer │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │     │ Scope + "what this is   │ Context & Scope / Goals │ What am I            │
    │ 1   │ not"                    │  and Non-Goals (Google) │ deliberately not     │
    │     │                         │                         │ doing?               │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │     │ 1–2 driving             │ Quality attribute       │ Failure at which     │
    │ 2   │ characteristics +       │ scenario (SEI)          │ point means total    │
    │     │ scenario with numbers   │                         │ failure?             │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │ 3   │ Ranked risks + spikes   │ Risk-driven model       │ What, if wrong,      │
    │     │                         │ (Fairbanks)             │ forces a full redo?  │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │ 4   │ 20-question golden set  │ Eval-driven development │ Which number tells   │
    │     │                         │                         │ me I'm done?         │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │     │                         │ Walking skeleton        │                      │
    │ 5   │ Walking skeleton        │ (Cockburn) / tracer     │ Does the end-to-end  │
    │     │                         │ bullet (Pragmatic       │ path actually run?   │
    │     │                         │ Programmer)             │                      │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │     │ types.py + diagram +    │ C4 level 2–3; parse,    │ What shape is the    │
    │ 6   │ dependency rule         │ don't validate          │ data, and who may    │
    │     │                         │                         │ depend on whom?      │
    ├─────┼─────────────────────────┼─────────────────────────┼──────────────────────┤
    │ 7   │ 3 ADRs on day 0 + 5     │ MADR / Y-Statement      │ Which options did I  │
    │     │ during the build        │                         │ reject, and why?     │
    └─────┴─────────────────────────┴─────────────────────────┴──────────────────────┘

Tier 3: At work
- Design doc in Google's structure: Context and Scope → Goals and Non-Goals → The Actual Design (with system-context diagram, APIs, data storage) → Alternatives Considered → Cross-cutting concerns (security, privacy, observability). Length: 10–20 pages for large work, 1–3 pages for small work.
- ADRs in the repo, reviewed in pull requests. An ADR is an immutable record; a design doc is a living draft updated when reality diverges from the plan.
- Fitness function — an automated check for an architectural property, running in CI. This is what locks a decision in rather than merely writing it down. A perfect example for you: a 5-line test asserting that src/retriever.py does not import src.config. That's ADR-0002 enforced by machine instead of by willpower. And it catches both bugs I found in hybrid-rag.
- Glossary / ubiquitous language — a shared vocabulary table between technical and non-technical people, the core idea of Domain-Driven Design. For hybrid-rag it's barely needed (you're every stakeholder); for a business system it's the most important artifact on the entire list.

Tier 4:
Three ideas at the code level, and the notable thing is that you already did all three half-right without knowing their names. Knowing the names is what will let you keep them.

1. Dependency rule + ports & adapters (hexagonal architecture). The rule: dependencies always point inward. The core knows nothing about the outside world; everything that touches the outside — disk, network, LLM, the config file — is an adapter at the edge, and the core talks to them through ports (abstract interfaces).

Your Retriever Protocol is a port, and you invented it in exactly the right place. Meanwhile retriever.py importing config.py is a dependency-rule violation — that arrow points from inside to outside. This is the precise name for the bug I pointed out, and it gives you a general rule instead of a one-off fix.

2. Functional core, imperative shell. The core is pure functions — same input always gives the same output, no reading or writing anything external. The shell is where all side effects live.

You're already close: rrf_fuse, metadata_filter, make_chunk, first_relevant_rank, _rates — all pure. Side effects are concentrated in loader (reads disk), pipeline (writes files), generator (calls Ollama). This is exactly why your core is easy to test and your shell has no tests at all. Name it, write it as one ADR line, and you won't accidentally slip a print or a file read into the middle of the core.

3. Parse, don't validate / make illegal states unrepresentable. The principle: at the system edge accept broad, untrusted types (str, dict), convert immediately into precise domain types, and inside the core work only with those precise types. Invalid states become impossible to write down, rather than something you must remember to check.

Your list[tuple[str, float]] has its own industry name: primitive obsession — using str/float for a concept that has its own meaning and constraints. Meanwhile Chunk with frozen=True, slots=True already has the right spirit. This is the theoretical argument for the Scored NamedTuple I proposed — it's not about typing less, it's about pushing errors from runtime back to writing time.

## A real loop, when do I decide:
    highest risk
    ↓
    spike / walking skeleton   ← THIS IS CODE, and it comes before the documents
    ↓
    measure (golden set)
    ↓
    decision + ADR             ← only recorded once the risk has been paid off with data
    ↓
    build one vertical slice
    ↓
    fitness function           ← lock the decision in, by machine
    ↓
    next highest risk

Decide:
- One-way doors / two-way doors (Amazon): two-way doors get decided fast, fix it if wrong. One-way doors get decided slowly, carefully, with a written record.
- Last responsible moment (from lean/agile): defer a decision until the latest point at which the cost of deferring further exceeds the cost of deciding now. Not deferring forever — "responsible" is the operative word.

Put together: decide one-way doors as late as you can while still being able to act, and write the ADR at that moment. This is the precise answer to what you asked two turns ago — why only 3 ADRs on day 0 rather than 8. The other five haven't reached their last responsible moment yet.

## What to skip
    ┌───────────────────┬────────────────────────────────────────────────────────────┐
    │       Thing       │                Why skip it for your project                │
    ├───────────────────┼────────────────────────────────────────────────────────────┤
    │                   │ Built for large / regulated systems. But it's excellent as │
    │ Full 12-chapter   │  a self-interrogation checklist — read the 12 chapter      │
    │ arc42             │ titles; any chapter you can't answer is a gap. Just don't  │
    │                   │ fill it in                                                 │
    ├───────────────────┼────────────────────────────────────────────────────────────┤
    │ ATAM / Quality    │ These are multi-stakeholder workshop methods. You are all  │
    │ Attribute         │ the stakeholders. Take the scenario with a response        │
    │ Workshop          │ measure, drop the process                                  │
    ├───────────────────┼────────────────────────────────────────────────────────────┤
    │ UML class         │ Out of date within the first week. Code is the only        │
    │ diagrams, C4      │ document that's always correct at that level               │
    │ level 4 (code)    │                                                            │
    ├───────────────────┼────────────────────────────────────────────────────────────┤
    │ Event storming,   │ Needs multiple people and a complex business domain.       │
    │ bounded contexts  │ hybrid-rag has no business domain                          │
    ├───────────────────┼────────────────────────────────────────────────────────────┤
    │ Choosing every    │ Only lock in what shapes the data (embedding model → 384   │
    │ library upfront   │ dimensions, 512-token limit: one-way door). The rest are   │
    │                   │ two-way doors                                              │
    ├───────────────────┼────────────────────────────────────────────────────────────┤
    │ Abstracting       │ Your Retriever Protocol is right because you already had   │
    │ before 2 real use │ two retrievers. Written when you only had one, it would    │
    │  cases            │ have had the wrong shape                                   │
    └───────────────────┴────────────────────────────────────────────────────────────┘
## The ritual to run on future project:
A. RISK (30 minutes)
   □ One-paragraph scope + the "what this is NOT" part
   □ 1-2 driving characteristics, each with 1 scenario WITH NUMBERS
   □ List risks, each with (probability, impact), then rank them
   □ For each risk: look up the Part 1 table → pick a technique.
     No risk means no technique.

B. PAY OFF RISK WITH CODE (2-3 hours)
   □ Golden set of ~20 questions (including unanswerable ones)
   □ Walking skeleton — every part the cheapest possible fake, running end-to-end
   □ Run the golden set through the skeleton → BASELINE NUMBER
   □ Spike the remaining risks, time-boxed, delete the code, keep the mechanism notes

C. LOCK THE CONTRACT (1 hour)
   □ types.py — derived from the REAL data shapes in the skeleton
   □ C4 level 2-3 diagram, data shape written on EVERY arrow
   □ One-way dependency rule (config, disk, network, LLM = adapters at the edge)
   □ 3 day-0 ADRs: repo layout + single source of truth / config / on-disk format
   □ 1 fitness function per ADR wherever it's machine-checkable

D. BUILD IN VERTICAL SLICES
   □ Each slice: replace one fake with the real thing → run golden set → compare tobaseline
   □ ADRs (a Y-Statement is enough) written AT the moment of decision, not at the end
   □ Trigger: does this decision constrain >1 file, or is it expensive to change later?