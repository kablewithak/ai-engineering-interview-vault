# AI Engineering Interview Vault

A public-facing learning and interview-preparation vault for AI engineering, AI reliability, retrieval systems, structured outputs, evals, agents, observability, privacy, and production-grade system design.

This repository is not a passive notes dump.

It is designed to turn study sessions into durable artifacts that improve:

- technical recall
- system-design judgment
- implementation literacy
- debugging and evaluation ability
- interview communication
- client-facing explanation
- AI reliability consulting readiness

## Why this repo exists

I am building toward AI engineering consultancy work.

That means I do not only need to know definitions. I need to explain, evaluate, apply, and defend production AI engineering decisions under real constraints.

This repo exists to help me turn weekly study into reusable proof of understanding:

- session files from active learning
- concept pages that mature over time
- runnable or near-runnable snippets
- interview answer banks
- mistake-pattern analysis
- spaced repetition prompts
- consultancy offer translations

The goal is durable mastery, not cramming.

## What this repo is

This repo is a structured public learning vault for AI engineering topics such as:

- structured outputs
- Pydantic contracts
- provider boundaries
- inference engines
- embeddings
- RAG
- retrieval metrics
- eval harnesses
- tracing and observability
- privacy and security controls
- agent harnesses
- production AI system design

Each artifact should help answer at least one of these questions:

1. Can I explain the concept clearly?
2. Can I apply it in a production-shaped system?
3. Can I identify how it fails?
4. Can I test or evaluate it?
5. Can I discuss trade-offs in an interview?
6. Can I translate it into a client-facing reliability offer?

## What this repo is not

This repo is not:

- a generic notes folder
- a course transcript dump
- a collection of copied summaries
- a prompt-engineering scrapbook
- a replacement for building systems
- a private diary of weak answers
- a production application repo

Production proof belongs in separate build repos.

This vault is for learning, recall, explanation, and interview readiness.

## Public vs private learning material

This repository is public-facing.

That means public files should be polished, useful, and professional.

Raw weak answers, messy attempts, false confidence logs, and private reflections belong in:

```text
.local/
```

The `.local/` folder is ignored by Git.

Public files may discuss mistakes, but only as professional learning patterns.

For example:

Good public framing:

```text
I initially confused evals with monitoring. The corrected distinction is...
```

Bad public framing:

```text
I completely failed this and had no idea what I was doing.
```

The learning signal is preserved without exposing raw private material.

## Folder map

```text
ai-engineering-interview-vault/
  prompts/
  templates/
  weeks/
  concepts/
  snippets/
  interview-bank/
  mistake-patterns/
  review-schedules/
  offer-translation/
  .local/                 # ignored by Git
```

## `weeks/`

The `weeks/` folder contains chronological session files tied to roadmap weeks.

Weekly files answer:

```text
What did I study, attempt, miss, correct, and need to retrieve later?
```

Example:

```text
weeks/week-01-foundations/2026-06-12-provider-boundaries-session.md
```

A session file should capture:

- source material
- learning objectives
- pre-test questions
- public-safe attempt summary
- error map
- corrected mental models
- code or implementation pattern
- production transfer
- retrieval pack
- spaced repetition schedule
- concept pages to create or update
- snippets to create or update
- mastery score
- public non-claims

The `weeks/` folder is the learning trail.

## `concepts/`

The `concepts/` folder contains canonical concept pages.

Concept files answer:

```text
What is my best current reusable explanation of this concept?
```

Each concept gets its own file.

Examples:

```text
concepts/rag/retrieval-metrics.md
concepts/structured-outputs/pydantic-contracts.md
concepts/evals/failure-taxonomy.md
concepts/agents/tool-calling.md
```

Concept files should mature over time as understanding improves.

A strong concept file includes:

- one-sentence definition
- technical definition
- plain-English explanation
- production relevance
- minimal example
- counterexample
- implementation pattern
- common failure modes
- debugging questions
- eval or measurement angle
- privacy and security angle
- interview answers
- related concepts
- related snippets
- related session files
- current gaps

The `concepts/` folder is the source of truth.

## `snippets/`

The `snippets/` folder contains runnable or near-runnable examples.

Snippets answer:

```text
What production pattern or failure prevention does this code demonstrate?
```

A snippet should not be random code.

Each snippet should make clear:

- the concept it proves
- the failure it prevents
- how to run or inspect it
- how to explain it in an interview
- what it does not prove

Examples:

```text
snippets/pydantic/typed-error-envelope.py
snippets/inference/provider-boundary.py
snippets/rag/retrieval-scorecard.py
snippets/evals/eval-case-schema.py
snippets/agents/action-guard.py
```

This repo is markdown-first, but runnable `.py` files are allowed when code makes the concept clearer.

## `interview-bank/`

The `interview-bank/` folder contains spoken answer practice.

Interview files answer:

```text
How would I explain this clearly under pressure?
```

It includes:

```text
interview-bank/technical/
interview-bank/system-design/
interview-bank/behavioral/
interview-bank/project-explanations/
```

A strong interview answer file should include:

- 20-second answer
- 90-second answer
- system-design answer
- trade-offs
- failure modes
- non-claims
- example follow-up questions

## `mistake-patterns/`

The `mistake-patterns/` folder contains public-safe summaries of recurring conceptual confusions.

It should not contain raw weak answers.

Examples:

```text
mistake-patterns/evals-vs-monitoring.md
mistake-patterns/rag-vs-fine-tuning.md
mistake-patterns/schema-validation-vs-prompting.md
```

Mistake-pattern files answer:

```text
What do people commonly confuse here, and what is the corrected mental model?
```

## `review-schedules/`

The `review-schedules/` folder tracks spaced repetition.

The main index is:

```text
review-schedules/spaced-repetition-index.md
```

Review prompts should increase in difficulty over time.

A good review schedule asks me to:

- retrieve definitions
- solve scenarios
- explain trade-offs
- spot bugs
- update concept files
- create or improve snippets
- translate the idea into an interview or client context

## `offer-translation/`

The `offer-translation/` folder connects technical learning to consultancy offers.

This matters because the goal is not only interview readiness. The goal is also to translate technical reliability work into buyer-relevant language.

Offer files include:

```text
offer-translation/ai-system-evaluation-audit.md
offer-translation/rag-reliability-sprint.md
offer-translation/agent-harness-hardening-sprint.md
```

Each offer file should eventually connect concepts to:

- buyer pain
- failure mode
- technical proof
- acceptance criteria
- non-claims
- why a CTO would care

## `prompts/`

The `prompts/` folder contains reusable learning prompts.

The main prompt is:

```text
prompts/master-learning-session-prompt.md
```

It is designed to produce two primary outputs:

1. a session file under `weeks/`
2. concept page updates under `concepts/`

It may also produce:

3. interview-bank additions
4. private `.local/` reflection prompts

## `templates/`

The `templates/` folder contains reusable artifact templates.

Current templates:

```text
templates/session-file-template.md
templates/concept-file-template.md
templates/runnable-snippet-template.py
```

Use templates to keep future study artifacts consistent.

## Weeks vs concepts

This repo intentionally has both `weeks/` and `concepts/`.

They are not duplicates.

```text
weeks/    = what happened during a study session
concepts/ = best current reusable understanding
```

A weekly file captures the learning process.

A concept file captures the polished current model.

Do not paste the same explanation into both places.

Instead, session files should link to concept files that were created or updated.

## Relationship to `ai-consultancy-lab`

This repo complements the production proof repo:

```text
ai-consultancy-lab
= proof that I can build production-shaped AI reliability systems

ai-engineering-interview-vault
= proof that I understand, explain, recall, and defend the concepts behind those systems
```

The two repos serve different roles.

This vault is for learning and interview readiness.

The lab is for implementation proof.

## Learning standard

For every important concept, I should be able to produce the following without notes:

1. definition
2. example
3. counterexample
4. production failure mode
5. debugging signal
6. eval or measurement method
7. interview explanation
8. client-facing implication

If I cannot do that, I do not own the concept yet.

## How to use this repo after a study session

Use this loop:

```text
Study material
-> pre-test before explanation
-> attempt answers
-> strict grading
-> error map
-> targeted teaching
-> harder application problems
-> production transfer
-> create session file
-> update concept files
-> update interview bank if useful
-> add review prompts
-> commit public-safe artifacts
```

Raw weak answers and private reflections stay in `.local/`.

## Public quality bar

Before committing a file, check:

- Is this useful to someone viewing the public repo?
- Does this avoid private embarrassment or raw weak answers?
- Does it explain the concept clearly?
- Does it include production relevance?
- Does it distinguish claims from non-claims?
- Does it avoid pretending a toy snippet proves production readiness?
- Does it connect to interviews, implementation, or client value?

## Current status

Maturity label:

```text
public-facing AI engineering learning vault scaffolded,
markdown-first,
runnable snippets allowed,
private raw learning folder ignored,
not yet populated with study sessions,
not yet interview-complete,
not a production application repo
```

## Non-claims

This repo does not currently prove:

- production engineering ability by itself
- mastery of all listed concepts
- completion of the AI engineering roadmap
- customer-data experience
- deployed system ownership
- interview readiness without further study and practice
