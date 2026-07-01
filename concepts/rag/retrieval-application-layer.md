# Retrieval Application Layer

## Concept

Name: Retrieval Application Layer

Category: RAG

Related roadmap week: Week 01 — Foundations

Status:

- Interview-ready

## One-Sentence Definition

A retrieval application layer is the set of explicit boundaries that turn authorised source documents into a small, traceable evidence set for a specific question, then produce an answer whose claims do not exceed that evidence.

## Technical Definition

A reliable retrieval application separates ingestion, document normalisation, chunking, indexing, request authentication and scope resolution, candidate retrieval, reranking, context assembly, grounded generation, output validation, and evaluation tracing. Each boundary should retain enough metadata to explain where required evidence disappeared, whether the student was allowed to use it, whether it reached the final model context, and whether it supported the final answer.

## Plain-English Explanation

A student assistant should not throw every PDF at an LLM and hope for the best. It should find the correct small part of the active course material, show that evidence to the model, show the student where it came from, and say when the material does not answer the question.

## Why It Matters In Production AI

A student-facing answer can be wrong even when the model is functioning as expected. The failure may have happened because:

- a scanned PDF was extracted incorrectly;
- a condition was split during chunking;
- the correct chunk was never retrieved;
- the correct chunk ranked too low;
- context packing dropped it;
- the model overstated incomplete evidence;
- the answer came from a stale, unauthorised, or wrong-course source.

Treating the system as an evidence pipeline turns vague “hallucination” reports into diagnosable engineering failures.

## Minimal Example

```text
Student question:
"Can I submit Assignment 2 late if I have an extension?"

Active, authorised evidence:
[E1] Written lecturer approval is required before the deadline for submission after the deadline.
[E2] Late submissions lose 5% per day for up to five days.

Safe answer:
"The active guide confirms that written lecturer approval is required before submitting after the deadline and that late submissions normally lose 5% per day for up to five days. It does not confirm whether written approval removes the penalty. Check the approval or confirm with the lecturer."
```

## Counterexample

The system returns:

> “Yes. An approved extension means there is no late penalty.”

The answer may sound reasonable and even include a real page reference, but it is ungrounded if neither source states that the penalty is removed.

## Implementation Pattern

```text
documents
→ parse and normalise
→ metadata-rich chunks
→ index
→ authenticate and resolve authorised course scope
→ retrieve eligible candidates
→ rerank
→ assemble sufficient context within a token budget
→ generate typed grounded answer
→ validate citations and support state
→ store privacy-safe trace
```

A chunk should retain at least:

```text
chunk_id
document_id
document_version
institution_id
course_id
source_page_or_slide
section_heading
access_scope
document_status
token_count
source_span
```

## Common Failure Modes

| Failure mode | Symptom | Likely cause | Fix |
|---|---|---|---|
| Parsing error | Correct-looking answer contains corrupt source text | OCR, broken reading order, table extraction loss | Validate extraction with representative source fixtures; retain source provenance. |
| Evidence broken during chunking | A rule and its exception are separated | Fixed-size splitting ignores semantic/structural boundaries | Use structure-aware, token-bounded chunks; measure evidence preservation. |
| Scope violation | Student receives another module’s rule | Scope filters are absent or applied too late | Apply identity, enrolment, version, and permissions before retrieval. |
| Retrieval miss | Required evidence exists but never enters candidates | Query mismatch, weak candidate depth, incorrect index | Inspect query, filters, index update state, and Recall@k. |
| Ranking failure | Correct chunk is present but too low | Reranker weak or candidate set ambiguous | Compare rank before/after reranking; evaluate NDCG@k and MRR. |
| Context exclusion | Correct evidence was retrieved but model never saw it | Prompt tax, duplicates, weak packing policy | Trace selected/dropped chunks, token costs, and drop reasons. |
| Grounding failure | Answer exceeds evidence | No support-status policy or weak answer contract | Use structured support states, citation validation, and claim-support review. |
| Freshness failure | Old guide beats active guidance | Version/status metadata ignored | Prefer active authoritative documents and test stale-source rate. |

## Debugging Questions

1. Did the required evidence survive parsing and chunking as a complete unit?
2. Was the query scoped to the correct student, institution, course, document version, and permission boundary before search?
3. Did the gold evidence enter the candidate set? At what rank before and after reranking?
4. Was the evidence selected for final context, or dropped? Why?
5. Does every meaningful answer claim have a supporting selected evidence ID?
6. Did the answer use the current authoritative document rather than a stale or lower-authority source?
7. Did the system correctly state insufficient evidence instead of guessing?

## Eval / Measurement Angle

How would this concept be evaluated?

Use fixed diagnostic questions with known source references and a deliberate mix of supported, partially supported, insufficient-evidence, conflicting-source, stale-source, and scope-violation cases.

Relevant metrics:

- evidence-preservation rate after chunking;
- Recall@k;
- MRR and NDCG@k;
- rank movement after reranking;
- final-context evidence-inclusion rate;
- evidence-coverage rate for multi-part questions;
- duplicate-context token waste;
- supported-answer rate;
- claim-support precision;
- citation validity and citation coverage;
- unsupported-answer fallback correctness;
- scope-violation and stale-source rates;
- latency and error rate.

Relevant traces:

- trace ID;
- authorised scope identifiers;
- source document/version IDs;
- chunk IDs and source spans;
- candidate ranks and scores;
- selected and dropped chunk IDs with reasons;
- prompt/context token allocation;
- returned support status and citation IDs;
- validation results and failure label.

Regression risk:

- changing PDF parser/OCR;
- changing chunk budget, overlap, or boundary policy;
- changing embedding model or index;
- changing metadata filtering;
- changing candidate depth or reranker;
- changing prompt instructions, output schema, tool wrappers, or context budget;
- adding a new document version/freshness policy.

## Security / Privacy Angle

Risks:

- cross-course or cross-institution retrieval;
- staff-only material or private student uploads reaching a student;
- raw documents, prompts, or retrieval traces leaking through logs;
- privileged storage/database credentials exposed to the browser;
- outdated content being presented as current academic guidance.

Controls:

- authenticate users and enforce course/version/permission filters in backend retrieval queries;
- use least privilege and environment separation;
- store source provenance and active/superseded status;
- minimise final context to the authorised evidence needed for the question;
- store IDs, hashes, ranks, token counts, and decisions rather than raw private text where possible;
- configure retention/deletion policy for student and document data;
- validate citations against the selected evidence set.

What should not be logged:

- raw private course files by default;
- unredacted student questions containing personal data;
- staff-only material, marking memoranda, secrets, or credentials;
- full prompts and retrieved context unless explicitly approved for a controlled diagnostic purpose.

## Interview Answers

### 20-second answer

> A retrieval application is an evidence pipeline around an LLM. It turns authorised source material into searchable chunks, retrieves the right evidence for a specific question, fits that evidence into context, and returns an answer that is clearly supported or clearly unsupported.

### 90-second answer

> For a student-learning product, I would not treat RAG as a chatbot plus a vector database. Documents first need parsing and normalisation that preserve pages, headings, versions, and course scope. They are chunked into answerable evidence units and indexed with metadata. At question time, the backend authenticates the student and scopes search to that student’s allowed, active course material before retrieving candidates. Reranking improves the order of those candidates, while context assembly selects a small, non-duplicate evidence set within the model budget. The answer should carry support status and source references. If the material does not establish a rule, especially around deadlines or assessments, the correct behaviour is to say what is confirmed and direct the student to the official source or lecturer. I would evaluate the evidence path end to end so a bad answer can be traced to ingestion, chunking, retrieval, ranking, packing, or grounding.

### System-design answer

> I would make every model boundary explicit: typed request and response contracts, metadata-rich source records, authorised retrieval filters, document-version policy, selected-evidence IDs, citation validation, and privacy-safe traces. I would measure whether gold evidence survives chunking, is retrieved, ranks high enough, reaches final context, and supports the generated answer. This gives the team a regression gate for changes to parsing, chunking, retrieval, prompts, or models instead of relying on a few impressive examples.

## Compare / Contrast

| This concept | Commonly confused with | Difference |
|---|---|---|
| Retrieval application layer | “RAG” as a single feature | The application layer includes ingestion, scope, retrieval, packing, grounding, evaluation, and privacy controls. |
| Indexing | Retrieval | Indexing prepares content for search; retrieval is the live search decision for a question. |
| Retrieval | Reranking | Retrieval creates the candidate set; reranking reorders that set. |
| Reranking | Context assembly | Reranking scores order; assembly selects, deduplicates, and packages evidence under a budget. |
| Citation presence | Grounding | A citation may exist without supporting the claim; grounding requires actual claim support. |
| Authentication | Authorisation | Authentication identifies the student; authorisation decides which rows, files, and chunks they may access. |

## Related Concepts

- [Tokenization in RAG Reliability](tokenization.md)
- `concepts/rag/chunking.md` — future narrow concept page
- `concepts/rag/indexing-and-retrieval.md` — future narrow concept page
- `concepts/rag/reranking.md` — future narrow concept page
- `concepts/rag/context-assembly.md` — future narrow concept page
- `concepts/rag/evidence-grounding.md` — future narrow concept page
- [Supabase in Student AI Applications](../platform/supabase-in-student-ai-applications.md)

## Related Snippets

- `snippets/rag/scoped-retrieval-trace.py` — future
- `snippets/rag/context-packing-scorecard.py` — future
- `snippets/rag/grounded-answer-contract.py` — future

## Related Session Files

- `weeks/week-01-foundations/2026-06-23-bag-learning-retrieval-application-layer-session.md`
- `weeks/week-01-foundations/2026-06-22-tokenization-through-rag-failure-analysis-session.md`

## Current Gaps

- Build a synthetic, locally validated retrieval harness with fixed evidence cases.
- Split this system-level page into narrower concept pages after practical exercises expose which boundaries need deeper treatment.
- Add a claim-level grounding checker with deterministic citation-ID checks and human spot checks.
- Compare structure-aware chunking against a controlled baseline while holding retrieval settings constant.
- Test source-authority and freshness policy using controlled conflicting-version cases.

## Last Updated

Date: 2026-06-23
