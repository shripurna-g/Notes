# Things I Keep Seeing

A parking lot for concepts that keep showing up in [Daily Dose of Data Science](https://www.dailydoseofds.com). If something appears four or five times, it's worth studying. Not before. Frequency is a good signal that the concept matters.

## Concept Tracker

| Concept | Seen | Study? |
|---|---|---|
| LoRA | □ □ □ □ □ | |
| Calibration | □ □ □ □ □ | |
| Triton | □ □ □ □ □ | |
| ONNX | □ □ □ □ □ | |
| Feature Store | □ □ □ □ □ | |
| Knowledge Distillation | □ □ □ □ □ | |
| RAG Evaluation | □ □ □ □ □ | |
| Beam Search | □ □ □ □ □ | |
| FAISS | □ □ □ □ □ | |
| CUDA Graphs | □ □ □ □ □ | |

## How to Use This

**Read every post. Deeply investigate only the ones that are:**

- Directly relevant to your current work
- Appearing repeatedly over time (4–5+ times)
- Genuinely sparking curiosity

Treat most posts as exposure, not study material.

## Anchor to Your Work (Fraud Detection)

When reading a post, ask: *"Could this matter for fraud detection?"*

| Concept | Why It Matters |
|---|---|
| Data drift | Fraud patterns change over time |
| Calibration | Risk scores should correspond to actual probabilities |
| Inference latency | Transactions need decisions within milliseconds |
| Feature leakage | Future information accidentally used during training |

The goal is for isolated concepts to form a coherent picture over months — from disconnected buzzwords to an integrated mental model.

---

## Sightings Log

### July 4, 2026

#### [The Anatomy of an Agent Harness](https://www.dailydoseofds.com/p/the-anatomy-of-an-agent-harness/)

**Core distinction**

- **Harness** = complete software infrastructure wrapping an LLM: orchestration loop, tools, memory, context management, state persistence, error handling, guardrails
- **Agent** = the emergent behavior; harness is the machinery producing it

**Key concepts spotted**

- ReAct loop
- TAO cycle
- Gather-Act-Verify cycle
- "Ralph Loop" pattern (Anthropic)
- 11 components of a production harness
- 6 categories of Claude Code tools

**Memory & context**

- Claude Code 3-tier memory hierarchy
- Model performance degrades 30%+ when key content falls in mid-window positions ("Lost in the Middle") — important context goes at beginning and end of prompt
- Claude Code preserves architectural decisions and unresolved bugs while discarding redundant tool outputs
- JetBrains' Junie hides old tool outputs while keeping tool calls visible
- Claude Code uses grep, glob, head, tail rather than loading full files

**Permissions & guardrails**

- Input, output, and tool guardrails
- Claude Code gates ~40 discrete tool capabilities independently: trust establishment at project load → permission check before each tool call → explicit user confirmation for high-risk operations

**Execution models (Claude Code)**

- Fork: byte-identical copy of parent context
- Teammate: separate terminal pane with file-based mailbox communication
- Worktree: own git worktree, isolated branch per agent

**Multi-agent frameworks spotted**

- OpenAI Agent SDK
- Claude Agent SDK
- LangGraph — subagents as nested state graphs; `runner` class; async/sync/streamed; graph DSL?; checkpointing and super-step boundaries; four error types: transient (retry with backoff), LLM-recoverable (ToolMessage), user-fixable (interrupt), unexpected (bubble up)
- LangGraph reducer
- CrewAI
- AutoGen
- Manus

**Memory strategies**

- OpenAI: application memory / SDK sessions / server-side Conversations API / lightweight `previous_response_id` chaining (four mutually exclusive strategies)

**Priority stacks**

- OpenAI Codex: server system message → tool definitions → developer instructions → user instructions (cascading AGENTS.md, 32 KiB limit) → conversation history

**Protocols**

- Bidirectional JSON-RPC API
- `tool_calls` objects

**Meta-observation**: Models are now post-trained with specific harnesses in the loop. Claude Code's model learned to use the specific harness it was trained with. As models improve, harness complexity should decrease.

---

#### [LLMOps Crash Course Part 6](https://www.dailydoseofds.com/llmops-crash-course-part-6/)

**Concepts spotted**

- In-context learning
- Majority voting over chain-of-thought
- Verbalized sampling — generate N responses with corresponding probabilities
- Mode collapse and LLM diversity
- Prompt repetition in non-reasoning LLMs
- Token ordering matters: `<CONTEXT> <QUESTION>` vs `<QUESTION> <CONTEXT>` can materially affect performance (tokens only attend to past tokens)
- Capping probabilities to discourage model from placing all mass on a single "best" response

**Prompt security**

- Prompt attack types:
    - Prompt extraction
    - Jailbreaking and prompt injection: roleplay ("pretend you're…"), formatting tricks ("write a poem about…"), obfuscation
- Rule: write your system prompt assuming it could become public — never place secrets (API keys, credentials, private URLs) inside prompts
- Model-level defenses are most robust: instruction hierarchy where system > user > tool outputs
- Metrics: violation rate, false refusal rate

**Tooling spotted**

- Prompt versioning with Langfuse

---

### July 5, 2026

#### [A Crash Course on Model Interpretability Part 1](https://www.dailydoseofds.com/a-crash-course-on-model-interpretability-part-1/)

**Plots & visualizations spotted**

- Partial Dependence Plots (PDPs)
- Individual Conditional Expectation (ICE)
- SHAP plot
- KS plot
- ROC curve (TPR vs FPR)
- PR plot
- QQ plot
- Cumulative explained variance
- Elbow curve
- Silhouette curve

**Concepts spotted**

- SHAP values vs contributions?
- Conformal predictions
- Gini impurity vs entropy
- Bias-variance tradeoff

---

#### [Loop Engineering Clearly Explained](https://www.dailydoseofds.com/p/loop-engineering-clearly-explained/)

**What loop engineering is**

- Outermost engineering layer — decides what the agent works on and when it's done
- The model and the loop are the parts you don't write
- Context engineering = everything the model sees on a turn, not just your instructions
- Harness engineering = code around the model that runs tools, tracks state, and recovers from errors

**Loop termination — don't trust the model to stop**

Three ways a loop ends:

1. Run out of money or time
2. No-progress detection
3. Real completion check — some condition passed

**Failure modes**

- Loop rot and doom loops fuel each other
- LangChain has middleware to detect doom loops

**Managing context in long loops**

- Compaction
- Offloading
- Subagents

**Design rules**

- Adding more tools makes selection harder — reducing improves odds
- Writes have to be safe to repeat, since the loop retries a failed step
- Error messages have to tell the agent what to do next, not just what went wrong — in a loop, that message becomes the input to the next turn
- Whatever decides if the work is good can't be the same model that produced it
- Don't tell it what to do — give it success criteria and watch it go

---

### July 7, 2026

#### [A Crash Course on Model Interpretability Part 1](https://www.dailydoseofds.com/a-crash-course-on-model-interpretability-part-1/) *(revisit)*

- PDP shows the average effect of a feature
- PDP assumes no correlation between features
- Rug plots with PDP / ICE — overlay feature distribution to show where data actually lives

**LLM text generation strategies**

- Greedy — pick the token with max probability
- Beam search — maximise the probability of the whole sequence
- Contrastive — penalise repetition
- Multinomial sampling — don't always pick the top probability

---

### July 8, 2026

#### Model Routing (Coinbase / Brian Armstrong)

**Core idea**: humans shouldn't be choosing which model answers a request — model selection is a job for AI. Coinbase built a routing layer in front of every request.

**Model routing strategies**

- User hardcodes a model
- User specifies a friendly name (e.g. "fast", "smart")
- Nothing specified — domain is inferred and model chosen automatically

**Model affinity**

- Pin the first model selected and route every subsequent call with the same task ID to that same model, regardless of how it gets classified
- Why: model on call 4 has no memory of decisions made on call 1, and every model switch invalidates the cache — you pay full token rate again from scratch
- Affinity pin lasts 10 minutes by default; when the task changes, generate a new ID and routing starts fresh
- Redis keeps the pin shared across services
