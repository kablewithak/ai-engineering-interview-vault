# Bag Learning Retrieval Application Layer — Study Session

## Metadata

- **Date:** 2026-06-23
- **Roadmap week:** Week 01 — Foundations
- **Session title:** Retrieval Application Layer for a Student AI Product
- **Source material:** Bag Learning Software Development Intern role material; existing `concepts/rag/tokenization.md`; interview-preparation discussion
- **Current level:** Foundation strengthened through a full evidence-path walkthrough
- **Available time:** Interview-led preparation
- **Purpose:** Build a theory-level explanation of a production-shaped retrieval application and a practical orientation to Supabase in the requested stack.
- **Related concepts:**
  - `concepts/rag/retrieval-application-layer.md`
  - `concepts/rag/tokenization.md`
  - `concepts/platform/supabase-in-student-ai-applications.md`
- **Related snippets:** No runnable snippet added in this documentation slice.
- **Related interview-bank files:** No separate interview-bank file added; reusable spoken answers are included below.

## 1. Learning Objectives

| Objective | Bloom level | Category | Why it matters |
|---|---|---|---|
| Explain RAG as an evidence pipeline, not a chatbot-plus-vector-store | Understand | Core concept | Prevents superficial system-design answers. |
| Trace a student question through ingestion, retrieval, context assembly, and grounding | Analyze | Evaluation/debugging skill | Makes failures diagnosable before blaming the model. |
| Explain why scope, source version, and permission metadata matter | Apply | Implementation skill | A relevant answer from the wrong course is still a product failure. |
| Explain retrieval, reranking, and context assembly without conflating them | Analyze | Core concept | Shows reliable mental models under interview pressure. |
| Explain Supabase as a data and identity platform within a full-stack request path | Understand | Implementation skill | Supports a credible answer about the listed stack without framework cosplay. |
| State an appropriate unsupported-answer path | Apply | Business/application insight | Academic products should not convert missing evidence into false certainty. |

## 2. Pre-Test Before Teaching

Questions used to establish the starting point:

1. What is RAG in a student-learning product?
2. Why is a retrieval application more than an LLM call over uploaded files?
3. Where can correct evidence disappear before an answer is generated?
4. Why must retrieval be scoped by course, document version, and access rights?
5. What is the difference between retrieval and reranking?
6. What does context assembly decide?
7. What does grounded generation require beyond showing a citation?
8. What is Supabase commonly used for in a full-stack application?

## 3. Public-Safe Attempt Summary

The initial explanation correctly identified that RAG combines LLM capability with private or course-specific material and that reliability depends on more than the LLM call. It also named parsing, chunking, retrieval, context assembly, grounding, and user-friendly output as relevant concerns.

The main correction was to make the live evidence path explicit: documents are indexed ahead of time, then relevant and authorised chunks are retrieved for a specific student question. The initial statement that tokens may be “lost” during context assembly was refined into a more precise failure: relevant evidence can be excluded because prompt overhead, duplicates, or output reserve consume the available context budget.

## 4. Error Map

### What I knew

- RAG can use course-specific material that general models may not know.
- A useful student answer should be grounded, clear, and easy to use.
- Failures can happen in chunking, retrieval, and context assembly rather than only at the model.
- The frontend/backend stack should be understood as connected layers rather than isolated technologies.

### What I partly knew

- The difference between preparing data for search and retrieving evidence at question time.
- Why reranking is a separate precision layer.
- Why a high-quality answer requires evidence sufficiency, not merely a relevant chunk.
- How Supabase, FastAPI, Next.js, React, and Tailwind can divide responsibilities.

### What I confused

- “Parsed and chunked material is fed to the LLM” versus “indexed chunks are selectively retrieved and assembled for each question.”
- Vague token loss versus a concrete context-exclusion mechanism.
- Citation presence versus claim-level evidence support.

### What I missed

- Course, institution, document-version, and permission scope are reliability boundaries as well as security boundaries.
- A high reranking score indicates relevance, not that a source is current, complete, or authoritative.
- A useful system needs explicit states for supported, partially supported, insufficient-evidence, conflicting-evidence, and clarification-needed answers.

### False confidence detected

- None recorded. The session stayed within an explicit evidence boundary and did not claim knowledge of Bag Learning’s private implementation.

## 5. Corrected Mental Models

### Concept: Retrieval applications are evidence pipelines

**Technical explanation:** A retrieval application turns source documents into searchable, metadata-rich evidence units, retrieves an authorised candidate set for a question, selects sufficient evidence within a context budget, then generates an answer whose claims are tied to that evidence.

**Plain-English explanation:** The assistant should not read every course file every time. It should find the small, correct part of the active course material, show it to the model, and admit when the material does not answer the question.

**Concrete example:** A question about a late Assignment 2 submission should retrieve the current course’s late-policy clause and any extension condition, not unrelated assessment material or another university’s rules.

**Counterexample:** Sending every uploaded PDF to the model and calling any polished answer “grounded.”

**Production AI engineering example:** Preserve the chain `source → chunk → candidate → reranked candidate → selected context → claim → citation` using IDs, source spans, document versions, and privacy-safe traces.

### Concept: Ingestion begins with parsing and normalisation

**Technical explanation:** Parsing extracts text, page/slide boundaries, headings, tables, and source metadata. Normalisation makes the extracted representation consistent enough to search while preserving meaning and provenance.

**Plain-English explanation:** Academic PDFs can be messy. Before search works, the system must turn a slide deck or module guide into clear, traceable content without rewriting the lecturer’s rules.

**Concrete example:** Keep an assessment deadline, weighting, and page number associated with the correct course and document version.

**Counterexample:** OCR changes “14 September” to “14 November”; later retrieval may be technically perfect yet still return a wrong answer.

**Production AI engineering example:** Store `document_id`, `document_version`, `source_page`, `section_heading`, `access_scope`, and ingestion status alongside normalised source text.

### Concept: Chunking preserves answerable evidence units

**Technical explanation:** Chunking creates retrieval units under a token budget. Structure-aware chunking should preserve the heading, rule, qualifier, and source span required to answer likely questions safely.

**Plain-English explanation:** Do not cut an important rule in half. Keep the assignment rule together with the condition that changes it.

**Concrete example:** “Late submissions lose 5% per day, up to five days” should remain associated with the relevant assignment and any extension condition.

**Counterexample:** A reranker cannot repair a rule that was split into two incomplete chunks or never retrieved.

**Production AI engineering example:** Evaluate evidence-preservation rate, boundary-failure rate, Recall@k, final-context inclusion, and supported-answer rate.

### Concept: Scope first, relevance second

**Technical explanation:** Retrieval must apply identity, enrolment, institution, course, active-document, version, and permission constraints before semantic or hybrid search.

**Plain-English explanation:** A correct-looking answer from another course is still wrong. The system must search the right student’s allowed material first.

**Concrete example:** The same phrase “late submission” may exist in two courses with different penalties; only the enrolled course’s active guide is eligible.

**Counterexample:** Hiding other courses in the UI while allowing the backend retrieval query to search them.

**Production AI engineering example:** Enforce scope in backend queries and test scope-violation and stale-source rates as explicit failure categories.

### Concept: Retrieval, reranking, and context assembly are separate decisions

**Technical explanation:** Retrieval returns a broad candidate set quickly. Reranking improves the order of candidates for the exact question. Context assembly selects a diverse, authoritative, sufficient evidence set that fits the final model budget.

**Plain-English explanation:** Search brings possible pages into the room, reranking puts the best pages on top, and context assembly decides which pages the model can actually read.

**Concrete example:** A late-submission question may retrieve assessment weighting, a deadline, the late penalty, and an extension condition. Reranking promotes the direct rule; context assembly includes both the penalty and the governing extension condition.

**Counterexample:** Sending the top five chunks blindly, including three duplicates and excluding the exception clause.

**Production AI engineering example:** Record candidate IDs, rerank positions, selected and dropped chunk IDs, token cost, source version, and drop reason.

### Concept: Grounded answers require explicit support states

**Technical explanation:** The answer boundary should return a typed support status, citations that reference selected evidence, an uncertainty note, and a recommended next action where support is incomplete.

**Plain-English explanation:** The assistant should say what the active material confirms and say when the material does not settle the question.

**Concrete example:** If the guide states that written approval is required for an extension and separately states a late penalty, but does not explain whether an extension removes the penalty, the answer must not invent that conclusion.

**Counterexample:** “An approved extension means there is no late penalty” when the source never says this.

**Production AI engineering example:** Validate citation IDs against selected evidence and test claim support, citation validity, fallback correctness, and missing-qualifier rate.

### Concept: Supabase is a data and identity platform, not the whole application design

**Technical explanation:** Supabase commonly provides PostgreSQL, authentication/session identity, file storage, access policies, and related application services. An application-specific FastAPI boundary can still own validation, retrieval orchestration, typed contracts, evaluation traces, and LLM integration.

**Plain-English explanation:** Supabase can hold the app’s users, courses, files, and permissions. FastAPI can decide how a question moves through the retrieval system.

**Concrete example:** Store student enrolments and document metadata in Postgres, source PDFs in storage, authenticate the student, then have FastAPI retrieve only eligible active chunks.

**Counterexample:** Putting a privileged service credential in the browser or treating UI visibility as authorisation.

**Production AI engineering example:** Keep privileged keys server-side, use least privilege, and enforce user/course scope in the database and retrieval layer.

## 6. Code / Implementation Pattern

```python
from enum import StrEnum

from pydantic import BaseModel, Field


class SupportStatus(StrEnum):
    SUPPORTED = "supported"
    PARTIALLY_SUPPORTED = "partially_supported"
    INSUFFICIENT_EVIDENCE = "insufficient_evidence"
    CONFLICTING_EVIDENCE = "conflicting_evidence"
    CLARIFICATION_NEEDED = "clarification_needed"


class Citation(BaseModel):
    evidence_id: str
    document_id: str
    source_label: str
    page_number: int | None = None


class GroundedAnswer(BaseModel):
    answer: str = Field(min_length=1, max_length=3_000)
    support_status: SupportStatus
    citations: list[Citation]
    uncertainty_note: str | None = None
    recommended_next_action: str | None = None
    trace_id: str
```

**What this proves:** The LLM boundary can return a deterministic contract that the frontend renders deliberately.

**Failure it prevents:** Every answer appearing equally certain, fabricated free-text source references, and unstructured data passing between backend and UI.

**How to explain it in an interview:** “I would make support state and evidence references part of the response contract, so the UI does not have to infer trust from prose.”

## 7. Production Transfer

### Capstone application

A student-learning retrieval application that answers questions from authorised, current course material with source references and a clear insufficient-evidence fallback.

### Client use case

A university-support tool that helps students find course-specific guidance while avoiding unsupported interpretations of deadlines, assessment rules, or lecturer policy.

### Architecture decision

Use source-aware ingestion and metadata-rich chunks. Apply identity and enrolment scope before retrieval. Keep retrieval, reranking, context assembly, generation, and evaluation as inspectable boundaries.

### Eval/observability requirement

For fixed cases, capture evidence survival, candidate presence, rank movement, selected context, citation validity, supported-answer result, source version, scope outcome, latency, and failure label.

### Security/privacy risk

Course documents, private student uploads, staff-only materials, personal data, and access credentials can leak through retrieval or logs.

### Implementation task

Build a small synthetic-data harness that models document ingestion, scoped retrieval, context packing, and typed grounded-answer output before integrating a model provider.

## 8. Interview Bank Additions

### 20-second answer

> RAG is a way to answer using specific source material rather than relying only on a model’s general knowledge. For a student product, the hard part is not just finding similar text. It is retrieving authorised, current evidence from the right course, fitting the necessary evidence into context, and returning an answer that is clearly supported or clearly unsupported.

### 90-second answer

> I think of a student-facing RAG system as an evidence pipeline. Academic files first need to be parsed and normalised without losing source pages, headings, tables, document versions, or course scope. They are then chunked into answerable evidence units and indexed with metadata. At question time, the backend should authenticate the student and scope retrieval to that student’s institution, enrolled module, active document version, and permissions before performing semantic or hybrid search. Retrieval gives a broad candidate set, reranking improves the order, and context assembly selects a small, non-duplicate evidence set that is sufficient for the question and fits the model budget. The model should return a typed answer with support state and source references. If the material does not establish the answer, the system should say so and direct the student to the official source or lecturer. I would evaluate each boundary so a failure can be traced to ingestion, chunking, retrieval, ranking, context packing, or grounding rather than blamed vaguely on the model.

### System-design answer

> I would keep the request path explicit: React and Next.js provide the student interface; FastAPI validates requests and owns retrieval orchestration; Supabase can provide PostgreSQL, authentication, storage, and access controls; and the model receives only a small, authorised evidence set with source IDs. I would enforce course scope before retrieval, preserve document version and source-page metadata, validate structured answer output, and use fixed diagnostic cases to measure evidence survival, Recall@k, rank movement, context inclusion, citation validity, and unsupported-answer behaviour. AI coding tools can accelerate the work, but I would still own the contract, security boundary, tests, diff review, and operational explanation.

## 9. Retrieval Pack

### Flashcards

1. **Q:** What is the difference between indexing and retrieval?  
   **A:** Indexing prepares chunks for search before a question arrives; retrieval searches eligible chunks at question time.

2. **Q:** What does parsing do?  
   **A:** Extracts usable text, structure, pages, tables, and source metadata from documents.

3. **Q:** What does normalisation do?  
   **A:** Makes parsed content consistent and searchable while preserving original meaning and provenance.

4. **Q:** What is the main goal of chunking?  
   **A:** Preserve complete, answerable evidence units that can be retrieved precisely.

5. **Q:** What cannot reranking fix?  
   **A:** Evidence that was never indexed, was broken during chunking, or never entered the candidate set.

6. **Q:** What does “scope first, relevance second” mean?  
   **A:** Apply identity, course, version, and permission rules before semantic search.

7. **Q:** What does context assembly decide?  
   **A:** Which selected evidence reaches the model, in what order, within the token budget.

8. **Q:** What is prompt tax?  
   **A:** Context capacity consumed by instructions, schemas, tool definitions, and wrappers rather than evidence.

9. **Q:** Why is citation presence weaker than grounding?  
   **A:** A citation may exist without actually supporting the claim being made.

10. **Q:** What is a supported-answer state?  
    **A:** The selected evidence directly supports the answer.

11. **Q:** What should happen with insufficient evidence?  
    **A:** State the limitation clearly and provide an appropriate next action rather than inventing certainty.

12. **Q:** What is Supabase commonly used for?  
    **A:** PostgreSQL data, authentication, storage, access controls, and related app services.

13. **Q:** Does authentication equal authorisation?  
    **A:** No. Authentication identifies a user; authorisation determines what that user may access.

14. **Q:** Why keep FastAPI with Supabase?  
    **A:** FastAPI can own application-specific validation, retrieval orchestration, typed contracts, and model integration.

15. **Q:** What should privacy-safe RAG traces store by default?  
    **A:** IDs, hashes, ranks, token counts, source spans, document version, decision labels, and trace IDs rather than raw private text.

### Scenario questions

1. A student receives an accurate-looking rule from another module. Which boundary failed, and what would you inspect first?
2. The correct chunk was retrieved at rank two but the answer ignored it. Which trace fields tell you whether the issue is reranking, context assembly, or grounding?
3. A scanned PDF says “14 November” but the original says “14 September.” Why can later retrieval metrics look good while the product remains wrong?
4. A question needs both an extension condition and late penalty, but only the penalty reaches the model. What is the failure label and the safest answer behaviour?
5. A current module guide conflicts with last year’s slide. What source-governance rule should decide what enters final context?

### Spot-the-bug questions

1. A retrieval query filters course ID only after semantic search returns top results.
2. A frontend hides staff-only documents but the backend API returns any document ID requested by the browser.
3. The prompt says “cite sources,” but the model can type page numbers it never received.
4. The context assembler packs the top five chunks even when three are near-duplicates.
5. An answer with `SUPPORTED` status is permitted even when its citation list is empty.

### Compare/contrast prompts

1. Parsing versus normalisation.
2. Retrieval versus reranking.
3. Citation validity versus claim-level grounding.

### Teach-back prompts

1. Explain chunking to a non-technical lecturer using a module-guide example.
2. Explain why “scope first, relevance second” protects both reliability and privacy.
3. Explain why a RAG system should sometimes say “I cannot confirm that from the active course material.”

### Design prompts

1. Design a minimal schema for documents, versions, chunks, enrolments, and answer traces.
2. Design a fixed evaluation set for late-submission and extension questions without using private course material.

### Brutal final exam question

1. A student asks whether a written extension removes a late penalty. The system retrieves the penalty policy, ranks it first, and returns “Yes, extensions remove the penalty” with a real citation. Walk through every possible boundary failure and design the smallest maintainable correction.

## 10. Spaced Repetition Schedule

### 1 day later

**Retrieve:** State the full evidence path from PDF upload to grounded answer without notes.  
**Solve:** Explain why reranking cannot repair bad chunking.  
**Explain:** Give the 20-second RAG answer aloud.  
**Apply/build:** Sketch a metadata schema for a course document and chunk.

### 3 days later

**Retrieve:** Define scope first, relevance second.  
**Solve:** Diagnose a retrieval versus context-exclusion failure.  
**Explain:** Explain Supabase’s role alongside FastAPI in 45 seconds.  
**Apply/build:** Draft one supported and one insufficient-evidence response object.

### 7 days later

**Retrieve:** List five context-assembly drop reasons.  
**Solve:** Propose a chunking policy for assignment rules and tables.  
**Explain:** Explain citation presence versus claim support.  
**Apply/build:** Create ten synthetic evaluation cases with gold evidence IDs.

### 14 days later

**Retrieve:** State the main metrics for each RAG boundary.  
**Solve:** Design a source-authority policy for current guides versus old slides.  
**Explain:** Answer the 90-second RAG system-design response aloud.  
**Apply/build:** Write a small typed FastAPI response model and validators.

### 30 days later

**Retrieve:** Reconstruct the full system model from memory.  
**Solve:** Perform an evidence-path autopsy for a wrong answer.  
**Explain:** Present a 5-minute student-assistant system design.  
**Apply/build:** Add a runnable synthetic retrieval-evaluation snippet.

## 11. Concept Pages Created or Updated

- `concepts/rag/retrieval-application-layer.md` — created
- `concepts/rag/tokenization.md` — updated with current related concepts and this session
- `concepts/rag/README.md` — updated with retrieval application layer entry
- `concepts/platform/README.md` — created
- `concepts/platform/supabase-in-student-ai-applications.md` — created

## 12. Snippets To Create or Update

- `snippets/rag/scoped-retrieval-trace.py` — future
- `snippets/rag/context-packing-scorecard.py` — future
- `snippets/rag/grounded-answer-contract.py` — future

## 13. Mastery Score

| Area | Score / 100 | Evidence |
|---|---:|---|
| Factual recall | 78 | Can name major RAG stages and the requested stack responsibilities. |
| Conceptual understanding | 82 | Corrected model now separates ingestion, indexing, retrieval, reranking, packing, and grounding. |
| Application | 76 | Can apply the model to a student-learning scenario; practical build evidence remains future work. |
| Debugging/evaluation | 76 | Can trace likely failure boundaries and name relevant metrics; needs hands-on evaluation exercises. |
| Transfer to real projects | 80 | Strong transfer to course-material assistants and reliability consulting narratives. |
| Simple explanation | 84 | Explains RAG as an evidence pipeline with clear student-product examples. |

**Next highest-leverage study action:** Run a timed mock interview that alternates between the retrieval application layer and the requested technology stack, then create a narrow synthetic implementation exercise.

## 14. Public Non-Claims

This session does not prove:

- knowledge of Bag Learning’s private architecture, data sources, or model choices;
- production deployment, load performance, or customer-data validation;
- implementation mastery of Next.js, React, FastAPI, Supabase, or Tailwind;
- that a generic RAG design is sufficient for every university or academic workflow;
- that citations alone prove every answer is grounded.
