# Agent Design Patterns

```
┌────┬──────────────────────┬────────────────────────────────────┬──────────────────────────────────┬──────────────────────────────────┬──────────────────────────────────┐
│ #  │       Pattern        │            Explanation             │           How it works           │               Pros               │               Cons               │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 1  │ Reactive / Reflex    │ Does not build a long plan or      │ Input comes in → agent matches   │ Fast, simple to build and        │ No real long-term planning,      │
│    │                      │ think much about future steps. It  │ it to a rule, pattern, or        │ understand, low token and        │ becomes weak when the task       │
│    │                      │ looks at the current input and     │ learned reaction → action        │ latency cost, good for           │ changes halfway, bad for multi-  │
│    │                      │ immediately returns the most       │ happens right away.              │ repetitive and simple tasks.     │ step reasoning.                  │
│    │                      │ likely action.                     │                                  │                                  │                                  │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 2  │ Planner-Executor     │ First creates a plan, then carries │ Goal comes in → planner breaks   │ Good for structured tasks,       │ Bad if the environment changes   │
│    │                      │ it out step by step. The planning  │ it into steps → executor         │ easier to control and debug than │ often, a wrong plan wastes time, │
│    │                      │ part decides the sequence of       │ performs those steps → if        │ fully dynamic systems.           │ replanning is expensive.         │
│    │                      │ actions, and the executor follows  │ something fails, the planner may │                                  │                                  │
│    │                      │ that sequence.                     │ replan.                          │                                  │                                  │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 3  │ ReAct                │ Alternates between reasoning and   │ Think → act with a tool or       │ Flexible, great for tool use,    │ Can be slower because it reasons │
│    │                      │ acting. It does not wait until the │ action → observe result → think  │ handles uncertainty well,        │ repeatedly, may overthink or     │
│    │                      │ end to decide everything. It       │ again → repeat.                  │ adjusts after observation.       │ loop, harder to predict than a   │
│    │                      │ thinks a little, takes a step,     │                                  │                                  │ fixed plan, more token-heavy     │
│    │                      │ observes what happened, then       │                                  │                                  │ than simple planning.            │
│    │                      │ thinks again.                      │                                  │                                  │                                  │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 4  │ Supervisor-Worker    │ One central controller, the        │ Supervisor receives task →       │ Good for complex tasks, strong   │ High coordination cost, higher   │
│    │                      │ supervisor, manages multiple       │ splits it into subtasks →        │ control and coordination, easy   │ latency, can fail if the         │
│    │                      │ worker agents. The supervisor      │ assigns workers → collects       │ to apply specialization,         │ supervisor is weak.              │
│    │                      │ decides who should do what, checks │ outputs → merges or reroutes.    │ supervisor can catch mistakes or │                                  │
│    │                      │ progress, and combines the         │                                  │ failures.                        │                                  │
│    │                      │ results.                           │                                  │                                  │                                  │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 5  │ Specialized          │ A team of agents where each agent  │ Task comes in → router or        │ Efficient use of expertise,      │ Requires good routing,           │
│    │ Collaborative        │ has a specific skill or role. One  │ coordinator sends it to the      │ better quality for specialized   │ specialists may disagree or      │
│    │                      │ might write, one might search, one │ right specialist → specialists   │ work, easier to scale by adding  │ overlap, coordination can still  │
│    │                      │ might code, one might verify.      │ produce parts → outputs are      │ new specialists, good when tasks │ be expensive, harder to manage   │
│    │                      │                                    │ combined.                        │ are clearly separated.           │ than one general agent.          │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 6  │ Hierarchical         │ Multiple layers of control.        │ Top layer sets the big goal →    │ Good for very large tasks,       │ Can become complex,              │
│    │                      │ Higher-level agents make broad     │ middle layers break it down →    │ scales better than one agent     │ communication between layers can │
│    │                      │ decisions, and lower-level agents  │ lower layers execute smaller     │ doing everything, easier to      │ be slow, more chance of mismatch │
│    │                      │ handle detailed execution.         │ tasks.                           │ divide labor, matches how real   │ between top and bottom layers,   │
│    │                      │                                    │                                  │ organizations work.              │ hard to debug across multiple    │
│    │                      │                                    │                                  │                                  │ levels.                          │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 7  │ Blackboard           │ Uses a shared workspace that all   │ One agent posts information on   │ Good for collaborative problem   │ Shared state can be messy, hard  │
│    │                      │ agents can read from and write to. │ the board → another agent        │ solving, agents do not need      │ to manage conflict, can be       │
│    │                      │                                    │ notices it and adds more →       │ direct communication with        │ inefficient if many agents write │
│    │                      │                                    │ others keep building on the      │ everyone, easy to share partial  │ at once, debugging can be        │
│    │                      │                                    │ shared state.                    │ results, flexible and modular.   │ difficult.                       │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 8  │ Swarm / Peer-to-Peer │ There is no single boss agent.     │ Each agent follows local rules   │ Flexible, no single point of     │ Hard to predict behavior, hard   │
│    │                      │ Agents interact directly with each │ and reacts to nearby agents or   │ failure, adapts dynamically,     │ to control and debug,            │
│    │                      │ other and coordination emerges     │ shared signals → group behavior  │ works well for decentralized     │ inefficient without strong       │
│    │                      │ from many local decisions.         │ emerges naturally.               │ systems.                         │ rules, quality varies.           │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 9  │ Debate / Critique /  │ Multiple agents check each other's │ Agent A produces output → Agent  │ Better quality and reliability,  │ Expensive in tokens and latency, │
│    │ Ensemble             │ work. One agent gives an answer,   │ B critiques or challenges it →   │ helps catch mistakes, good for   │ can become slow with many        │
│    │                      │ another critiques it, and          │ Agent A or another agent revises │ high-stakes reasoning,           │ rounds, agents may critique      │
│    │                      │ sometimes several answers are      │ → final answer is selected.      │ encourages stronger final        │ unhelpfully, not always worth    │
│    │                      │ compared before choosing the best  │                                  │ answers.                         │ the cost for simple tasks.       │
│    │                      │ one.                               │                                  │                                  │                                  │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 10 │ Self-Reflective /    │ The agent checks its own output    │ Agent produces draft → agent     │ Improves clarity and quality,    │ The agent may miss its own       │
│    │ Self-Correcting      │ and tries to improve it before     │ reviews draft → finds weak spots │ catches obvious mistakes, lower  │ mistakes, reflection can be      │
│    │                      │ delivering the final result.       │ → revises or improves.           │ overhead than multi-agent        │ repetitive, not as strong as     │
│    │                      │                                    │                                  │ debate.                          │ independent critique, extra      │
│    │                      │                                    │                                  │                                  │ reasoning increases cost.        │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 11 │ Memory-Augmented     │ Has access to memory, especially   │ Agent receives current task →    │ Better continuity over time,     │ Memory can be wrong or outdated, │
│    │                      │ long-term memory, so it can recall │ searches or reads memory → uses  │ supports personalization, good   │ retrieval may bring irrelevant   │
│    │                      │ previous facts, preferences,       │ relevant past information →      │ for long-running tasks, reduces  │ information, privacy and storage │
│    │                      │ tasks, or conversations.           │ continues with better context.   │ forgetfulness.                   │ become important, more system    │
│    │                      │                                    │                                  │                                  │ complexity.                      │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 12 │ Graph / Workflow /   │ Follows a defined path of states   │ State 1 → State 2 → State 3 →    │ Very predictable, easy to test   │ Less flexible, hard to handle    │
│    │ State-Machine        │ or nodes. Each step has a clear    │ depending on conditions, go to   │ and monitor, good for production │ unexpected cases, can become     │
│    │                      │ next step, like a flowchart.       │ another node or end.             │ workflows, easier to reason      │ large and complex, not ideal for │
│    │                      │                                    │                                  │ about than free-form agent       │ open-ended reasoning.            │
│    │                      │                                    │                                  │ behavior.                        │                                  │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 13 │ Event-Driven         │ Does nothing until a trigger       │ Event occurs → agent wakes up →  │ Efficient, great for automation  │ Depends on event quality, can    │
│    │                      │ happens. The trigger could be an   │ processes event → returns to     │ and monitoring, responds in real │ miss context if events are       │
│    │                      │ email, message, file upload,       │ waiting.                         │ time, good for systems that do   │ isolated, harder to manage many  │
│    │                      │ alert, or external signal.         │                                  │ not need constant attention.     │ event types, not ideal for deep  │
│    │                      │                                    │                                  │                                  │ reasoning.                       │
├────┼──────────────────────┼────────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┼──────────────────────────────────┤
│ 14 │ Tool-First / Router- │ Main job is to decide which tool,  │ Request comes in → router        │ Efficient when many tools exist, │ Depends heavily on routing       │
│    │ Based                │ system, or specialist should       │ classifies the task → sends it   │ good for enterprise setups,      │ quality, wrong routing causes    │
│    │                      │ handle the task. It may not do the │ to the best tool or agent →      │ reduces unnecessary reasoning,   │ bad results, can feel shallow if │
│    │                      │ core work itself.                  │ combines or returns the result.  │ easy to extend with new tools.   │ overused, needs strong tool      │
│    │                      │                                    │                                  │                                  │ design.                          │
└────┴──────────────────────┴────────────────────────────────────┴──────────────────────────────────┴──────────────────────────────────┴──────────────────────────────────┘
```