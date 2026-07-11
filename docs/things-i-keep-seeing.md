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

---

### July 9, 2026

#### Agent / Model Deployment Strategies

- **Batch** — prioritises throughput over latency; large volumes processed together when latency isn't a concern (e.g. nightly scoring runs)
- **Stream** — continuously processes data as it arrives (e.g. Kafka pipeline); no explicit request/response boundary
- **Real-time** — model sits behind an API (REST or gRPC); a client sends a request and waits for a response. *Question: how is this different from stream?* — stream is push-based and stateless per event; real-time adds a synchronous request/response contract, load balancing, auth, rate limiting, versioning — the API layer is what makes it "real-time" as a service rather than a pipeline stage
- **Edge** — model runs on device (phone, browser, embedded); no round-trip to a server; good for privacy and low-latency scenarios

---

### July 10, 2026

#### Reinforcement Learning / LLM Training

**Core loop**

- state → action → reward → environment
- Reward is the hard part — may need separate training on human preferences

**Concepts spotted**

- GRPO (Group Relative Policy Optimization) — DeepSeek's approach
- GRPO loss calculator
- Environment as the barrier still standing — frontier labs guard this as a proprietary asset

**Othello env as a skeleton for RL**

- *Question*: the LLM isn't supposed to play Othello directly — so are we converting whatever the LLM outputs into an Othello move, then labelling good move = good job, bad move = bad job? And doesn't that converter also need training?
- Once you strip out the Othello specifics, the same `MultiTurnEnv` becomes a skeleton for any turn-based task
- Minimax: opponent looks `depth=d` moves ahead and sets traps; randomness param controls how often it ignores strategy

**Reward function (4 parts)**

1. Win result
2. Piece advantage
3. Format compliance
4. Invalid move penalty

**Data generation**

- Have your strongest model play a few hundred games and save the results
- Filter to wins and draws before training — don't teach the model its strongest player's mistakes alongside its good format
- *Question*: is this leakage prevention, or just quality filtering?

---

### July 11, 2026

#### [A Crash Course on Building RAG Systems Part 1](https://www.dailydoseofds.com/a-crash-course-on-building-rag-systems-part-1-with-implementations/)

**Why RAG?**

- Need to look up new info without retraining the model
- You could just stuff it in the prompt — but context window is the constraint
- Vector DBs store unstructured embeddings; not just RAG — Google Photos probably uses one too

**Tool stack spotted**

- LlamaIndex
- Qdrant — OSS alternative to Pinecone
- Ollama
- asyncio

**ANN vs exact search**

- Vector DBs use Approximate Nearest Neighbor (ANN) — trading perfect recall for speed at scale

**Chunking strategies**

- Fixed size
- Semantic chunking — keep adding to a chunk until similarity score drops
- Document structure based (headers, paragraphs)
- LLM based

- Bi-encoders for chunk → embedding generation (flagged as important)

**RAG challenges**

- *Lost in the middle*: LLMs don't perceive order as we do — documents in the middle of a retrieved list tend to get dismissed. Put important context at the beginning and end.
- *Questions not semantically similar to answers*: embed the question and search for answers — but the question and the answer live in different embedding spaces
    - Fix: **HyDE (Hypothetical Document Embeddings)** — use the LLM to generate a hypothetical answer, embed that, and use it to query the vector DB instead
- *Chunk dilution*: large chunks contain multiple unrelated ideas, so similarity search returns irrelevant documents. Keep chunks to a few paragraphs max so each chunk has a single "concept"
