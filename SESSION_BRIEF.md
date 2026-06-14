# SESSION_BRIEF.md — Week 01 Remaining Study Blocks

## Metadata

- Created: 2026-06-13
- Timezone: Africa/Johannesburg
- Active ladder: Ladder 1 — Capability and Proof Factory
- Active stage: Stage 1 — Foundations, repo operating system, structured outputs, and baseline RAG
- Active roadmap week: Week 01 — LLM Systems Map and Engineering Operating System
- Session mode: Study planning + guided learning
- Primary repo: `ai-engineering-interview-vault`
- Related proof repo: `ai-consultancy-lab`
- Current maturity label: learning artifact; not production proof
- Current completed concept: Inference / Provider Boundaries
- Current completed session file: `weeks/week-01-foundations/2026-06-13-provider-boundaries-and-error-envelopes-session.md`
- Current completed concept file: `concepts/inference/provider-boundaries.md`

## Source Anchors

- `AI_Consultancy_Roadmap_Ladder_1_Ladder_2_v3_integrated.pdf`
- `SPRINT_CONTEXT.md`
- `session-file-template.md`
- `concept-file-template.md`
- `runnable-snippet-template.py`
- `2026-06-13-provider-boundaries-and-error-envelopes-session.md`
- `provider-boundaries.md`

## What Week 01 Is Actually About

Week 01 is not just inference.

Week 01 is the foundation layer for understanding AI systems as engineered systems. The roadmap says the Week 01 learning target is:

- LLM product stack
- tokens
- embeddings
- retrieval
- prompting
- evaluation
- serving
- governance
- provider-boundary basics
- why the LLM is only one component inside a wider application system
- ADR discipline with compliance-risk notes and future-change trade-offs

The first session already covered:

- provider boundaries
- inference clients
- provider adapters
- timeout/retryability basics
- health vs model readiness
- trace-safe provider logs
- typed provider error envelopes

Therefore, the remaining Week 01 study work should cover the rest of the systems map without jumping ahead into Week 2 implementation.

## What Must Not Be Done

- Do not move into Week 2 structured outputs yet as the main topic.
- Do not start RAG implementation.
- Do not create production-proof claims from study artifacts.
- Do not treat course completion as progress.
- Do not create raw weak-answer logs in public files.
- Do not commit `.local/` material.
- Do not over-study passively without retrieval, teach-back, or production transfer.
- Do not turn this into a generic AI theory course.

## Overall Week 01 Remaining Objective

Build a strong mental map of the AI product stack so later implementation choices make sense.

By the end of the six remaining study blocks, the learner should be able to explain:

1. What happens to text before, during, and after an LLM call.
2. How tokens shape cost, latency, context, retrieval, and output behavior.
3. What embeddings represent and why they matter for retrieval.
4. Why retrieval is an engineered subsystem, not just “add a vector database.”
5. How prompting fits inside a harness rather than replacing the harness.
6. Why evaluation, tracing, serving, governance, logging, and ADRs are part of the AI system.
7. How all Week 01 concepts connect to the consultancy offer ladder.

## Six Remaining Study Blocks

### Block 1 — LLM Product Stack Map

- Default date: 2026-06-13
- Default time: 13:00–14:15 SAST
- Duration: 75 minutes
- Status: Not started
- Output type: session update + concept notes
- Concept category: Core system map

#### Purpose

Build the top-level mental model of an AI application as a system, not a model call.

#### Study focus

- User input
- preprocessing
- tokenization
- prompt assembly
- provider call
- retrieval/tool context where relevant
- structured output or plain text output
- validation
- logging/tracing
- eval feedback
- user-facing response
- governance controls

#### Core question

Where does the LLM sit inside the application, and what surrounds it?

#### Expected end state

The learner can draw and explain a simple AI system pipeline without pretending the model is the whole product.

#### Artifact update after session

- Update week session file with corrected system map.
- Possible concept page: `concepts/systems/llm-product-stack.md`

---

### Block 2 — Tokens, Context Windows, Latency, and Cost

- Default date: 2026-06-14
- Default time: 10:00–11:15 SAST
- Duration: 75 minutes
- Status: Not started
- Output type: session update + retrieval pack
- Concept category: Core concept + production trade-off

#### Purpose

Understand tokens as the unit that affects context length, cost, latency, truncation risk, retrieval design, and output limits.

#### Study focus

- tokens vs words
- input tokens vs output tokens
- context window
- prompt budget
- retrieval context budget
- tool output budget
- latency/cost implications
- context dilution

#### Core question

Why is “just add more context” usually a weak engineering answer?

#### Expected end state

The learner can explain why token budgets force architecture decisions.

#### Artifact update after session

- Possible concept page: `concepts/foundations/tokens-and-context-windows.md`
- Possible snippet: no snippet required unless useful later

---

### Block 3 — Embeddings and Similarity Search

- Default date: 2026-06-15
- Default time: 18:30–19:45 SAST
- Duration: 75 minutes
- Status: Not started
- Output type: session update + concept page draft
- Concept category: Core concept + retrieval prerequisite

#### Purpose

Understand embeddings as vector representations used for semantic similarity, not magic understanding.

#### Study focus

- embedding vectors
- semantic similarity
- dense representation
- cosine similarity at a high level
- embedding models
- query embedding vs document embedding
- why provider choice matters
- where provider boundaries return in embedding calls

#### Core question

What does an embedding let the system compare, and what does it not guarantee?

#### Expected end state

The learner can explain embeddings in production terms and identify false claims.

#### Artifact update after session

- Possible concept page: `concepts/retrieval/embeddings.md`
- Possible snippet later: simple fake embedding similarity demo

---

### Block 4 — Retrieval as a Subsystem

- Default date: 2026-06-16
- Default time: 18:30–19:45 SAST
- Duration: 75 minutes
- Status: Not started
- Output type: session update + compare/contrast notes
- Concept category: Implementation architecture prerequisite

#### Purpose

Understand retrieval before building RAG.

#### Study focus

- document ingestion
- chunking
- metadata
- indexing
- query processing
- top-k retrieval
- ranking
- provenance
- citations
- stale or wrong sources
- retrieval failure vs generation failure

#### Core question

Why is RAG quality usually won or lost before generation?

#### Expected end state

The learner can explain the retrieval pipeline and name common failure modes.

#### Artifact update after session

- Possible concept page: `concepts/retrieval/retrieval-pipeline.md`
- Possible future snippet: chunking example, not during this planning session

---

### Block 5 — Prompting, Harnesses, and Evaluation Basics

- Default date: 2026-06-17
- Default time: 18:30–19:45 SAST
- Duration: 75 minutes
- Status: Not started
- Output type: session update + failure map
- Concept category: Harness thinking + eval prerequisite

#### Purpose

Put prompting in its correct place: useful, but not a substitute for schemas, evals, traces, validators, or tests.

#### Study focus

- prompting as instruction design
- system/developer/user messages conceptually
- prompt fragility
- prompt-only anti-pattern
- fixed eval cases
- baseline vs intervention
- scoring basics
- failure labels
- regression thinking

#### Core question

When is a prompt change not enough?

#### Expected end state

The learner can explain why reliable AI systems need harnesses and evals, not just better prompts.

#### Artifact update after session

- Possible concept page: `concepts/evals/eval-baseline-intervention.md`
- Possible concept page: `concepts/harnesses/code-as-harness.md`

---

### Block 6 — Serving, Observability, Governance, and ADR Discipline

- Default date: 2026-06-18
- Default time: 18:30–19:45 SAST
- Duration: 75 minutes
- Status: Not started
- Output type: session update + Week 01 synthesis
- Concept category: Production AI operating system

#### Purpose

Close Week 01 by connecting system design to operations, governance, privacy, traceability, and architectural decisions.

#### Study focus

- serving basics
- API boundary vs provider boundary
- logs vs traces
- trace IDs
- JSON structured logs
- privacy-safe observability
- governance controls
- ADRs
- compliance-risk notes
- future-change trade-offs

#### Core question

What makes an AI system inspectable, governable, and maintainable?

#### Expected end state

The learner can explain why Week 01 is the operating foundation for all capstones.

#### Artifact update after session

- Possible concept page: `concepts/observability/trace-ids-and-json-logs.md`
- Possible concept page: `concepts/architecture/adrs-for-ai-systems.md`
- Week 01 synthesis checkpoint

## After-Each-Session Update Protocol

After every study block, update the session artifact with:

1. Date and actual study time.
2. What was studied.
3. Raw attempt summary, public-safe only.
4. Error map:
   - what I knew
   - what I partly knew
   - what I confused
   - what I missed
   - false confidence detected
5. Corrected mental models.
6. Production transfer:
   - capstone application
   - client use case
   - architecture decision
   - eval/observability requirement
   - security/privacy risk
7. Interview bank additions:
   - 20-second answer
   - 90-second answer
   - system-design answer
8. Retrieval pack:
   - flashcards
   - scenario questions
   - spot-the-bug questions
   - compare/contrast prompts
   - teach-back prompts
9. Spaced repetition schedule.
10. Mastery score.
11. Public non-claims.
12. Next highest-leverage study action.

## Acceptance Criteria For This Week 01 Study Sequence

The sequence is complete only when:

- Six remaining blocks have been studied.
- Each block has a public-safe session update.
- At least three concept pages are created or planned.
- Weak/private answers stay out of public files.
- The learner can explain the AI product stack orally.
- The learner can connect each concept to production AI reliability.
- The learner can connect Week 01 to the commercial offer ladder.
- No claim is made that Week 01 proves implementation mastery or production readiness.

## Commercial Translation

Week 01 supports the future AI System Evaluation Audit by building the language needed to inspect a client AI system.

Buyer pain this supports:

- “We built an AI feature, but nobody can clearly explain why it fails.”
- “The model works sometimes, but failures are hard to trace.”
- “We do not know whether the issue is prompt, retrieval, provider, logging, or architecture.”
- “We need a technical map before we can fix anything.”

Offer supported:

- AI System Evaluation Audit

Non-claim:

This Week 01 study sequence does not yet prove the ability to improve a real client system. It builds the conceptual map required before reliable implementation, evaluation, and commercial proof.
