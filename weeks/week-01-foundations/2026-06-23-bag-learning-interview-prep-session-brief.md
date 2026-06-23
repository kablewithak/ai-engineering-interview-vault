# SESSION_BRIEF — Bag Learning Software Development Intern Interview Prep

## Session metadata

- **Date:** 2026-06-23
- **Track:** AI Engineering Interview Vault
- **Session type:** Targeted interview preparation
- **Target company:** Bag Learning
- **Target role:** Software Development Intern
- **Interview shape:** 45-minute, theory-led conversation
- **Primary emphasis:** Retrieval application layer
- **Secondary emphasis:** Fast orientation to the requested product stack
- **Current level:** Strong conceptual foundation in RAG reliability and tokenization; limited hands-on familiarity with the requested frontend/backend stack
- **Source evidence supplied:** role screenshot listing Next.js, React, FastAPI, Supabase, Tailwind, Git-driven delivery, testing/documentation, shipped-feature maintenance, and AI-assisted development with code ownership.

## North star

Leave the interview sounding like a junior engineer who can contribute safely to a student-facing AI product because he understands that retrieval systems are **evidence pipelines**, not just chatbots with a vector database.

The interview performance target is not to pretend to be a senior full-stack engineer or to claim knowledge of Bag Learning’s private architecture. It is to show that I can:

1. reason precisely about how an academic-content retrieval application should work;
2. identify where an answer can fail before the model generates it;
3. explain the requested stack as one coherent request path;
4. use AI-assisted development responsibly while owning tests, trade-offs, and shipped code;
5. ask sharp questions that show product, user, reliability, and maintainability awareness.

## Evidence boundary

Treat the following as confirmed only where supplied in the role material:

- The stack named in the role: **Next.js, React, FastAPI, Supabase, Tailwind**.
- The role includes shipping user-facing features, tests, documentation, code review, Git-driven workflow, backlog work, maintenance, AI-assisted development, and ownership of shipped work.

Treat the following as informed hypotheses, not facts to state as Bag Learning’s internal implementation:

- The student companion uses retrieval-augmented generation.
- It ingests university PDFs, lecture slides, module guides, and similar academic content.
- It uses semantic retrieval, embeddings, vector search, reranking, or a specific LLM provider.

Use phrases such as: **“For a student-learning retrieval system, I would expect…”**, **“A sensible design would be…”**, and **“I would validate this against your current product constraints.”**

## Interview positioning

### Core positioning statement

> I think about retrieval applications as evidence-reliability systems. A weak answer may be a model problem, but it can also be an ingestion, chunking, retrieval, ranking, or context-assembly failure. I would make those boundaries inspectable, measure them with fixed diagnostic cases, and give the student a clear unsupported-answer path when the evidence is missing or weak.

### What I should signal

- I understand product-facing AI, not just model APIs.
- I know retrieval is scoped: a student should receive evidence from the right university, module, document version, and access boundary.
- I value typed contracts, tests, traceability, clean Git workflow, and maintainable boundaries.
- I can learn a stack quickly when I understand the request flow and the contracts between layers.
- AI coding tools accelerate implementation, but they do not replace code review, tests, ownership, or reasoning.

### What I should not do

- Do not lead with model names, cloud providers, vector-database brands, or framework hype.
- Do not call a retrieval system “accurate” without explaining how it is evaluated.
- Do not imply that a reranker can recover evidence that was destroyed during chunking or never retrieved.
- Do not claim personal production ownership that has not been earned.
- Do not give a long framework tutorial when asked a theory question.

## Primary knowledge target — the retrieval application layer

### The end-to-end mental model

```text
academic documents
→ parse and normalize
→ create metadata-rich chunks
→ index chunks for retrieval
→ student asks a question
→ authenticate and scope the request
→ retrieve eligible candidates
→ rerank / filter candidates
→ assemble context within a token budget
→ generate a grounded answer
→ show evidence or source references
→ record privacy-safe trace and evaluation signals
```

### The answer I need to be able to give

A student-facing retrieval application should not search every document blindly. The query needs the correct scope first: the student, institution, course, module, access rights, document version, and academic period. Then the system should retrieve candidate chunks with enough metadata to prove where they came from. A second ranking or filtering step can promote the most useful candidates, and context assembly must fit evidence alongside instructions and output space. The answer layer should use only the supplied evidence, expose a clear fallback when evidence is insufficient, and return source references that allow the student to verify the claim.

### High-value design boundaries

| Boundary | What it owns | Failure to name in an interview |
|---|---|---|
| Ingestion | Parse, clean, classify, version, and preserve source provenance | “The PDF is in the database” is not a reliable ingestion design. |
| Chunking | Turn source text into retrievable evidence units | Important clauses can be split so no usable evidence survives. |
| Metadata and scope | University, module, document, version, source page, permissions | Correct text can be retrieved for the wrong student or course. |
| Retrieval | Produce a candidate set likely to contain relevant evidence | Relevant evidence may never enter the candidate set. |
| Ranking | Order candidates by usefulness for the question | Correct evidence may be present but too low to reach the model. |
| Context assembly | Select deduplicated, useful evidence within a token budget | Good candidates can be dropped due to prompt tax, duplicates, or reserve policy. |
| Generation | Answer only from evidence and use an unsupported fallback | The system can invent a polished but unsupported answer. |
| Evaluation and traces | Make every boundary inspectable and measurable | Teams tune prompts without knowing where the evidence disappeared. |

### Reliability questions to prepare for

1. What is RAG and why would a student product use it?
2. How would you prevent the assistant from answering from the wrong course or university?
3. What happens when the information is not in the course material?
4. Why do chunking and tokenization matter?
5. What can a reranker fix, and what can it not fix?
6. How would you know whether retrieval is actually improving the product?
7. What would you log, and what should not be logged?
8. How would you handle outdated lecture slides or updated module guides?
9. How would you explain citations or source references to a student?
10. How would you make an AI answer useful without making it falsely authoritative?

### Core failure taxonomy

| Failure label | Meaning | First investigation |
|---|---|---|
| Source absent | The supplied corpus does not contain the answer | Check source coverage and ingestion status. |
| Evidence broken | Chunking split the relevant clause or table badly | Inspect source spans, chunk boundaries, and parser output. |
| Retrieval miss | Valid evidence exists but was not retrieved | Inspect query normalization, filters, retrieval method, and candidate depth. |
| Ranking failure | Evidence was retrieved but ranked too low | Compare rank before and after reranking. |
| Context exclusion | Evidence was retrieved and ranked but not packed | Inspect token budget, duplicate content, prompt tax, and reserve policy. |
| Grounding failure | Evidence reached the model but the answer ignored or distorted it | Inspect answer-to-evidence support and citation alignment. |
| Scope/privacy failure | Evidence came from the wrong tenant, institution, module, or permission boundary | Audit metadata filters and authorization enforcement. |
| Freshness failure | The response uses superseded academic content | Check document versioning, publication date, and active-status filters. |

### Evaluation standard

For a meaningful retrieval change, do not claim improvement from one good answer or a single quality metric.

Use fixed diagnostic cases with known evidence references. Record:

- whether the gold evidence survived chunking;
- whether it entered the candidate set;
- rank before and after reranking;
- whether it reached final context;
- whether the answer is supported by that evidence;
- whether the fallback was correct when support was absent;
- latency and token use where operationally relevant.

Useful evaluation metrics:

- Recall@k;
- MRR or rank movement;
- evidence-inclusion rate;
- supported-answer rate;
- unsupported-answer fallback correctness;
- scope-violation rate;
- stale-document retrieval rate;
- response latency and error rate.

Privacy-safe traces should prefer IDs, hashes, source spans, ranks, token counts, failure labels, and trace IDs. Do not routinely log raw student questions, raw course documents, full prompts, retrieved context, secrets, or protected academic data.

## Secondary knowledge target — requested stack orientation

### One coherent request path

```text
Student browser
→ Next.js / React user interface
→ FastAPI backend endpoint
→ authentication, validation, retrieval orchestration, and response contract
→ Supabase services such as PostgreSQL, authentication, and file storage
→ structured response
→ React interface renders answer, evidence references, loading state, and errors
→ Tailwind applies the visual system and responsive styling
```

### Stack explained without framework cosplay

| Technology | Simple explanation | Likely responsibility in a student AI product | What I need to say confidently |
|---|---|---|---|
| React | A library for building interactive user interfaces from reusable components and state. | Question box, chat history, citations, loading/error states, document views. | “React owns how the interface responds to user actions and renders changing data.” |
| Next.js | A React application framework that organizes routes and production web-app behavior. | Student-facing web app, pages, routing, browser/server boundary, deployment-facing app structure. | “Next.js gives the React frontend an application structure for routes and user-facing delivery.” |
| FastAPI | A Python framework for typed HTTP APIs. | Request validation, orchestration, retrieval calls, response schemas, backend business rules. | “FastAPI is a clean place to make the AI boundary explicit through typed request and response models.” |
| Supabase | A backend platform built around PostgreSQL, commonly used for data, authentication, storage, and related app services. | Student accounts, institution/module records, document metadata, object storage, application data. | “Supabase can centralize application data and access controls, while the retrieval layer must still enforce scope deliberately.” |
| Tailwind | Utility-first CSS tooling for building consistent interfaces quickly. | Layout, responsive design, accessible visual states, design-system consistency. | “Tailwind affects presentation speed and consistency; it should not be confused with application logic.” |
| Git and code review | Shared change-control workflow. | Small reviewable changes, tests, documentation, rollback safety. | “I would ship small, understandable changes with tests, clear commits, and a PR description that explains behavior and risk.” |
| AI-assisted development | Use of an AI coding tool to accelerate work. | Drafting, explanation, test ideas, refactoring support, documentation assistance. | “The tool can speed me up, but I still own the architecture, security, tests, diff review, and every line we ship.” |

### Minimal contract-first FastAPI example to understand

```python
from pydantic import BaseModel, Field


class AskCourseQuestionRequest(BaseModel):
    course_id: str = Field(min_length=1)
    question: str = Field(min_length=3, max_length=2_000)


class EvidenceReference(BaseModel):
    document_id: str
    source_label: str
    page_number: int | None = None


class AskCourseQuestionResponse(BaseModel):
    answer: str
    supported: bool
    evidence: list[EvidenceReference]
    trace_id: str
```

What this demonstrates:

- The frontend and backend agree on a deterministic request/response contract.
- The API can reject malformed input early.
- The answer carries a machine-readable support state instead of making every answer look equally certain.
- Evidence references and a trace ID make debugging and user trust easier.

### Minimal full-stack explanation

> A student types a question into a React component inside the Next.js app. The UI sends a structured request to a FastAPI endpoint. FastAPI validates the request, verifies the user and course scope, coordinates the retrieval flow, and returns a typed answer object with evidence references and a support status. Supabase can hold user, course, and document metadata, plus storage and authentication services. React then renders the answer and sources, while Tailwind handles the responsive visual layer. The important thing is that each boundary has a clear contract and does not leak responsibility into every other layer.

## Interview-answer bank

### 20-second answer — What is RAG?

> Retrieval-augmented generation is a pattern where the system retrieves relevant source material before asking the model to answer. For a student product, the reliability goal is not just “find similar text”; it is to retrieve evidence from the right course and document version, fit it into context, and make the final answer clearly supported or clearly unsupported.

### 60-second answer — How would you design a student assistant?

> I would separate the system into ingestion, retrieval, answer generation, and evaluation boundaries. Academic documents would be parsed into metadata-rich chunks with institution, module, document version, source page, and access information. When a student asks a question, the backend would first scope it to what that student is allowed to access, retrieve candidates, rank them, and assemble a small evidence set within a token budget. The model would answer from those sources and include references. If the material does not support an answer, the product should say so and guide the student to the relevant document or lecturer rather than inventing certainty. I would measure the evidence path end to end so we can see whether failures come from parsing, chunking, retrieval, ranking, context packing, or generation.

### 60-second answer — How would you prevent hallucinations?

> I would not describe hallucination prevention as one prompt. First, give the model only scoped, traceable evidence. Second, require an explicit unsupported state when evidence is absent or insufficient. Third, return source references so the student can verify the answer. Finally, evaluate fixed questions with known evidence and track where a wrong answer came from. A model can still fail, but the system becomes inspectable and the user is not given false confidence.

### 45-second answer — How do you use Claude or another coding assistant?

> I use it as an accelerator, not an authority. I can use it to explore a codebase, draft a narrow implementation, generate test cases, or explain an unfamiliar framework. But before I ship anything, I inspect the diff, understand the data flow, run the relevant tests, check edge cases and security boundaries, and make sure I can explain why the design is the simplest maintainable choice. The responsibility stays with me.

### 30-second answer — How do the requested tools fit together?

> React and Next.js handle the student-facing web experience. FastAPI provides a typed Python backend for application logic and AI orchestration. Supabase can provide the app’s database, authentication, and file-storage services. Tailwind is the styling layer. I would keep the interfaces between those layers explicit so a retrieval feature is testable and maintainable instead of becoming one large, coupled chat flow.

## Mock interview protocol for this session

The assistant running this study session must:

1. Begin with a diagnostic pre-test rather than teaching immediately.
2. Ask one theory question at a time and wait for my answer.
3. Correct only the specific gap, then make me re-answer it more cleanly.
4. Use a student-learning product example throughout.
5. Push on trade-offs and failure modes, not vocabulary alone.
6. Keep stack explanations tied to the request flow, not framework trivia.
7. Challenge unsupported confidence and prevent invented claims about Bag Learning’s internal architecture.
8. End with a compact 45-minute interview simulation and an answer-quality scorecard.

## Diagnostic pre-test questions

1. Explain RAG in a way that a Head of Development would not find superficial.
2. Where can the right evidence disappear before a model produces an answer?
3. Why is tenant/course/module scoping a reliability issue, not only a security issue?
4. What should happen when the course material does not support the answer?
5. Explain the difference between retrieval, reranking, and context packing.
6. What would you measure before claiming a retrieval improvement?
7. Explain the Next.js → React → FastAPI → Supabase → Tailwind request path.
8. How would you use an AI coding assistant without becoming dependent on it?
9. What tests would you add before shipping a student-facing retrieval feature?
10. What question would you ask Bag Learning to understand their retrieval architecture without sounding performative?

## Study-session outputs required

By the end of the preparation session, produce:

- a corrected mental model of the retrieval application layer;
- polished 20-second, 60-second, and 90-second answers for the highest-probability questions;
- a one-page stack map in plain English;
- a product-specific failure taxonomy;
- five high-quality questions for Bag Learning;
- a final 45-minute interview strategy;
- a public-safe session file using the established Interview Vault template;
- concept-page updates only where a gap is genuinely exposed.

## Questions to ask Bag Learning

1. What kinds of student questions are highest value today: explaining course concepts, navigating module material, study support, or something else?
2. How do you currently handle versioning when lecturers update slides, module guides, or other source material?
3. What does a high-quality answer mean in your product: source coverage, student usefulness, factual support, speed, or a combination?
4. Which reliability problem is most difficult right now: ingestion quality, retrieval relevance, answer grounding, latency, or something else?
5. For this internship, where would you expect the person to contribute first: frontend experience, FastAPI feature work, data/product workflows, or AI-application reliability?

## Public non-claims

This preparation session does not prove:

- that I know Bag Learning’s internal RAG, data, model, or deployment architecture;
- that I have shipped this exact stack in production;
- that any proposed retrieval design is correct without their product constraints, user data, and evaluation evidence;
- that one retrieval metric alone proves student learning outcomes or answer reliability.

## Session operating instruction

Run this as an interview-performance session, not a broad course.

Prioritize precise explanations, clean mental models, trade-offs, failure diagnosis, and honest ownership. Do not over-teach framework syntax. Do not introduce cloud infrastructure, agent frameworks, or vector-database brand comparisons unless they directly clarify a question. Keep every explanation connected to the student-learning retrieval product and the responsibilities in the role.
