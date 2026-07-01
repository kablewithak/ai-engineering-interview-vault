# Bag Learning Retrieval Active Recall — Study Session

## Metadata

- **Date:** 2026-07-02
- **Roadmap week:** Week 01 — Foundations
- **Session title:** Bag Learning Retrieval Active Recall and Interview Drill
- **Source material:** Prior Bag Learning retrieval-application study session; tokenization concept page; Bag Learning role material already captured in the interview-prep brief.
- **Current level:** Passive exposure completed; active recall, failure diagnosis, and spoken interview delivery now need strengthening.
- **Available time:** Session-driven; begin with short, high-signal drills and expand only where reasoning is weak.
- **Purpose:** Convert prior passive exposure to the retrieval application layer into fast, defensible interview reasoning.
- **Related concepts:**
  - `concepts/rag/tokenization.md`
  - `concepts/rag/retrieval-application-layer.md`
  - `concepts/platform/supabase-in-student-ai-applications.md`
- **Related session files:**
  - `weeks/week-01-foundations/2026-06-23-bag-learning-interview-prep-session-brief.md`
  - `weeks/week-01-foundations/2026-06-23-bag-learning-retrieval-application-layer-session.md`

## 1. Session Objective

Build confident, concise explanation and diagnosis of a student-facing retrieval application:

```text
academic documents
→ parse and normalize
→ chunk with provenance and scope
→ index searchable evidence
→ authenticate and scope the request
→ retrieve candidates
→ rerank where justified
→ assemble sufficient context
→ generate a grounded response
→ return citations or source references
→ capture privacy-safe traces and evaluation signals
```

The goal is not framework trivia or a generic RAG definition. The goal is to reason from evidence boundaries, failure modes, evaluation signals, and student-safety constraints.

## 2. Evidence Boundary

Confirmed from the Bag Learning role material already reviewed:

- The requested stack includes Next.js, React, FastAPI, Supabase, and Tailwind.
- The role expects user-facing feature delivery, Git and code review workflow, testing/documentation, maintenance, and responsible AI-assisted development.

Do **not** claim that Bag Learning privately uses a specific vector database, LLM provider, embeddings model, reranker, OCR tool, indexing strategy, Supabase feature, or deployment architecture.

Use conditional language where necessary:

- “For a student retrieval application, I would expect…”
- “A sensible default would be…”
- “I would validate that against the current product constraints and corpus.”

## 3. Learning Objectives

| Objective | Bloom level | Category | Why it matters |
|---|---|---|---|
| Explain the end-to-end retrieval pipeline without notes | Understand | Core concept | Shows system-level understanding rather than chatbot vocabulary. |
| Diagnose where evidence disappeared using trace evidence | Analyze | Evaluation/debugging skill | Separates parsing, chunking, retrieval, ranking, packing, and grounding failures. |
| Explain scope and metadata as reliability controls | Analyze | Core concept | Correct content from the wrong course, version, or student boundary is still wrong. |
| Explain grounding and unsupported-answer behavior | Apply | Production AI engineering | Student-facing academic answers must not manufacture certainty. |
| Explain the Next.js/React/FastAPI/Supabase/Tailwind request path | Understand | Implementation skill | Lets the user discuss the role stack without pretending deep seniority. |
| Explain AI-assisted development with ownership | Evaluate | Business/application insight | Matches the role’s expectation that AI helps but does not replace responsible engineering. |

## 4. Current Baseline

### What has already been covered

- Parsing and normalization
- Tokenization and chunking
- Indexing and scoped retrieval
- Reranking
- Context assembly and evidence sufficiency
- Answer generation, grounding, citations, and unsupported fallback
- Supabase as Postgres, authentication, storage, and access-control platform alongside FastAPI

### Current constraint

The material was studied mostly passively and under time pressure. Treat recall, sequencing, and precise failure terminology as unproven until demonstrated aloud.

## 5. Active Recall Protocol

For each question:

1. Answer without notes in 45–90 seconds.
2. State the pipeline boundary first.
3. State what did **not** fail when evidence supports that conclusion.
4. Name the first traces, inputs, or metadata to inspect.
5. Propose the smallest maintainable intervention.
6. State the measurement required before claiming improvement.

Avoid generic answers such as:

- “The AI hallucinated.”
- “I would improve the prompt.”
- “I would use a vector database.”
- “I would make the chunks better.”

Replace them with evidence-based diagnosis.

## 6. Core Drill Bank

### Pipeline and failure location

1. A document was parsed correctly, but an assignment deadline is wrong in the final answer. Name the possible boundaries and the order you would inspect.
2. The gold chunk exists in the database but is absent from the top 20 candidates. What failed, what did not fail, and what do you inspect?
3. The correct chunk is retrieved at rank 2 but does not appear in the LLM prompt. Which boundary failed?
4. The model cites a real page but the cited paragraph does not support the claim. Is this citation presence, citation validity, or grounding?
5. Two documents disagree about a deadline. What answer state should the API return and which source should it prefer?

### Scope and privacy

6. Why is course/module/version scope a reliability requirement as well as a security requirement?
7. What must be enforced in the backend before semantic search?
8. What should not be included in standard retrieval traces or model context?
9. Why is hiding a document in React not sufficient access control?

### Retrieval design

10. Explain why a reranker cannot repair evidence that never survived chunking.
11. When might a small structured corpus not need model-based reranking?
12. Explain hybrid retrieval and when exact keyword matching is useful.
13. What does a complete chunk need besides text?
14. Explain evidence-preservation rate, Recall@k, MRR, and final-context inclusion in one flow.

### Generation and grounding

15. What does a `PARTIALLY_SUPPORTED` answer look like?
16. Why is “add citations” not sufficient grounding policy?
17. Give an example of a safe unsupported-answer fallback for an academic rule.
18. What deterministic post-generation checks can run before rendering an answer?

### Stack orientation

19. Explain the request path from a student question in Next.js/React to a grounded response from FastAPI.
20. What belongs in Supabase versus FastAPI?
21. What is the difference between authentication and authorization?
22. What is Row Level Security, and what does it not replace?
23. How should a junior developer use Claude or another coding assistant responsibly?

## 7. High-Leverage Interview Answers To Rehearse

### RAG in a student product

> A student-facing retrieval application should retrieve authoritative, scoped course evidence before generation rather than relying on general model knowledge. The backend needs to enforce institution, module, document-version, and permission scope first, then retrieve and rank eligible chunks, assemble a sufficient evidence set within a token budget, and return an answer that is explicitly supported, partially supported, or unsupported. The important point is that a wrong answer can originate at ingestion, chunking, retrieval, ranking, context packing, or grounding, so I would trace those boundaries rather than blame the model immediately.

### How to prevent hallucination

> I would not frame hallucination prevention as a prompt alone. I would use authorised, traceable evidence; structured support states; citation IDs bound to selected evidence; deterministic validation that citations exist; and fixed evaluation cases that test supported, unsupported, conflicting, and cross-course questions. When evidence is missing, the correct product behavior is to say what the active material confirms and what it does not confirm, then give the student an appropriate next action.

### Stack request path

> React renders the interactive interface inside a Next.js application. The frontend sends a typed request to FastAPI. FastAPI validates it, resolves the authenticated student’s scope, orchestrates retrieval and answer generation, and returns a structured response. Supabase can provide Postgres, authentication, file storage, and database-level access control. Tailwind is the visual layer. The important architectural boundary is that the frontend does not decide what evidence the student may retrieve; that is enforced server-side before retrieval and before model context is created.

## 8. Session Output Expectations

By the end of this session, produce:

- a public-safe session summary;
- updates to only the concept pages materially improved by demonstrated understanding;
- a compact interview-answer scorecard;
- a spaced-repetition plan based on observed weak points;
- no claims of Bag Learning production knowledge or system ownership.

## 9. Mastery Scorecard

| Area | Score / 100 | Evidence |
|---|---:|---|
| End-to-end retrieval explanation |  |  |
| Failure-boundary diagnosis |  |  |
| Scope, privacy, and freshness reasoning |  |  |
| Grounding and unsupported-answer handling |  |  |
| Retrieval metrics and evaluation design |  |  |
| Stack request-path explanation |  |  |
| Responsible AI-assisted delivery |  |  |
| Spoken clarity under pressure |  |  |

## 10. Public Non-Claims

This session does not prove:

- production ownership of Bag Learning’s system;
- access to Bag Learning’s private codebase, data, architecture, evaluation suite, or deployment configuration;
- universal RAG reliability;
- production readiness of a concept page or interview answer;
- deployment, customer-data experience, or operational ownership.
