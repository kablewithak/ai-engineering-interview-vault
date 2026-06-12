# Provider Boundaries and Typed Error Envelopes

## Metadata

- Date: 2026-06-13
- Roadmap week: Week 01 — Foundations
- Session title: Provider Boundaries and Typed Error Envelopes
- Source material: `ai-consultancy-lab` Week 1 Superlinked provider adapter work
- Current level: Intermediate, LLM-assisted implementation with architecture ownership
- Available time: Project-derived learning artifact
- Purpose: Interview readiness, production AI system design, and AI reliability consultancy transfer
- Related concepts:
  - `concepts/inference/provider-boundaries.md`
  - `concepts/structured-outputs/`
  - `concepts/observability/`
  - `concepts/privacy-security/`
- Related snippets:
  - No runnable snippet created in this slice
- Related interview-bank files:
  - `interview-bank/technical/provider-boundaries.md`

## 1. Learning Objectives

| Objective | Bloom level | Category | Why it matters |
|---|---|---|---|
| Define a provider boundary in a production AI system | Understand | Core concept | Provider calls are unstable and must not leak across the app unchecked |
| Explain why provider-specific SDKs should sit behind adapters | Analyze | Implementation skill | Prevents the rest of the system from becoming coupled to one vendor |
| Design typed success and failure contracts for inference calls | Create | Implementation skill | Makes downstream behavior deterministic and testable |
| Distinguish a provider health check from model readiness | Analyze | Evaluation/debugging skill | A server can be healthy while a specific model is still loading or unavailable |
| Map provider failures into safe, retry-aware error envelopes | Apply | Evaluation/debugging skill | Enables graceful failure handling without leaking raw exceptions |
| Explain provider-boundary design in interview and CTO language | Evaluate | Business/application insight | Shows system ownership beyond happy-path demos |

## 2. Pre-Test Before Teaching

This was a retroactive learning artifact created after implementation work, so no live pre-test was performed at the time.

Diagnostic questions that should be used for review:

1. What is a provider boundary?
2. Why should `sie_sdk` imports stay inside the Superlinked adapter?
3. What is the difference between a provider-neutral interface and a provider-specific implementation?
4. Why is returning a raw dictionary from an inference call risky?
5. What does a typed error envelope give you that an exception string does not?
6. Why can `/healthz` and `/readyz` pass while an embedding call still fails?
7. When should a provider failure be retryable?
8. What should not be included in trace logs when a provider call fails?
9. What is the difference between a model-loading failure and an unexpected response-shape failure?
10. Trick question: if a smoke test succeeds once, does that prove the provider integration is production-ready?

## 3. My Raw Attempt Summary

Public-safe summary:

The initial implementation successfully called a real local Superlinked Inference Engine through Docker and produced a valid embedding response. The first version proved the happy path, but it did not yet fully represent provider failures as typed application-level outcomes.

The real failure observed during local validation was useful: the SIE server could report healthy/ready while the model-specific encode call still returned a temporary provider failure during model loading.

That exposed the need for a typed provider error envelope.

Raw weak answers and private reflections belong in `.local/`, not in this public file.

## 4. Error Map

### What I knew

- The provider should be isolated behind an adapter.
- The repo should not become a Superlinked-only demo.
- Inference calls need traceability and tests.
- Logs must not expose secrets, raw prompts, or private payloads.

### What I partly knew

- I understood that provider failures needed handling, but the first pass focused on the happy path.
- I understood structured outputs as a general principle, but needed to apply the idea specifically to provider errors.

### What I confused

- A server-level readiness check can be mistaken for model-level readiness.
- A successful smoke test can be mistaken for broader reliability proof.
- Provider SDK success shape can be mistaken for a stable app contract.

### What I missed

- Temporary model-loading failure should be a first-class, retryable error state.
- Unexpected provider response shape should be non-retryable until investigated.
- The smoke script should handle typed failures gracefully instead of crashing or leaking raw exception text.

### False confidence detected

The main false-confidence risk was treating a working embedding call as proof that the provider boundary was reliable. The corrected view is narrower: it proved local live-provider connectivity, not full production readiness.

## 5. Corrected Mental Models

### Concept: Provider Boundary

Technical explanation:

A provider boundary is the seam where application-owned contracts meet an external provider or SDK. The application should expose typed, provider-neutral inputs and outputs while hiding provider-specific objects, exceptions, response shapes, and implementation details behind an adapter.

Plain-English explanation:

The rest of the app should not care whether embeddings come from Superlinked, OpenAI, a local model, or a fake test client. It should only care that it gets a valid response or a safe typed error.

Concrete example:

```text
EmbeddingRequest
-> InferenceClient protocol
-> SuperlinkedInferenceClient
-> SIE SDK/server
-> EmbeddingResponse or InferenceProviderError
```

Counterexample:

```text
retrieval_core imports sie_sdk directly
eval_harness parses raw provider exceptions
smoke script crashes with uncategorized provider errors
```

Production AI engineering example:

A RAG pipeline asks for embeddings. The provider is temporarily unavailable. Instead of throwing raw SDK exceptions across the app, the adapter returns:

```text
ProviderErrorCode.MODEL_LOADING
retryable=True
```

The caller can then retry, degrade gracefully, or record the failure as an eval/trace event.

### Concept: Typed Error Envelope

Technical explanation:

A typed error envelope is a structured failure object that carries stable fields such as `trace_id`, `provider`, `model`, `code`, `message`, `retryable`, and `status`. It converts provider failure into a deterministic application-level contract.

Plain-English explanation:

Instead of letting the system explode with messy error text, we turn failure into a known shape the rest of the app can understand.

Concrete example:

```text
InferenceProviderError(
  trace_id="...",
  provider="superlinked",
  model="sentence-transformers/all-MiniLM-L6-v2",
  code="model_loading",
  retryable=True,
  status="provider_error"
)
```

Counterexample:

```text
raise RuntimeError("503 Service Unavailable...")
```

Production AI engineering example:

During model loading, SIE returned a temporary `503`. The corrected app behavior is not to treat that as an unknown crash. It should map to a retryable `MODEL_LOADING` state with trace-safe metadata.

## 6. Code / Implementation Pattern

Minimal pattern:

```python
from enum import StrEnum
from typing import Literal

from pydantic import BaseModel


class InferenceStatus(StrEnum):
    SUCCESS = "success"
    PROVIDER_ERROR = "provider_error"


class ProviderErrorCode(StrEnum):
    MODEL_LOADING = "model_loading"
    PROVIDER_UNAVAILABLE = "provider_unavailable"
    TIMEOUT = "timeout"
    UNEXPECTED_RESPONSE = "unexpected_response"


class EmbeddingResponse(BaseModel):
    trace_id: str
    provider: str
    model: str
    dense_dimensions: int
    output_types: list[str]
    status: Literal[InferenceStatus.SUCCESS] = InferenceStatus.SUCCESS


class InferenceProviderError(BaseModel):
    trace_id: str
    provider: str
    model: str
    code: ProviderErrorCode
    message: str
    retryable: bool
    error_type: str | None = None
    status: Literal[InferenceStatus.PROVIDER_ERROR] = InferenceStatus.PROVIDER_ERROR


EmbeddingCallResult = EmbeddingResponse | InferenceProviderError
```

What this proves:

- Provider calls can return deterministic success or failure shapes.
- Downstream code can branch on typed status instead of parsing exceptions.
- The provider boundary becomes testable without requiring a live provider.

Failure it prevents:

- Raw SDK exceptions leaking through retrieval/eval code.
- Provider-specific response shapes becoming app-wide dependencies.
- Temporary provider errors being treated as permanent failures.
- Trace logs including unsafe raw exception payloads.

How to explain it in an interview:

> I treat the provider as unstable infrastructure. I keep provider SDKs behind an adapter and expose a typed contract to the rest of the app. That way success, temporary provider failures, timeouts, and unexpected response shapes are explicit, testable, and traceable.

## 7. Production Transfer

Capstone application:

- Dirty Data Retrieval Reliability System: embedding calls should return typed provider outcomes before retrieval scoring begins.

Client use case:

- A startup has a RAG chatbot that intermittently fails because the embedding service times out or returns inconsistent responses. The audit identifies provider-boundary failure handling as a reliability gap.

Architecture decision:

- Keep provider SDK usage inside `packages/inference_clients/`.
- Retrieval, eval, and reporting packages depend on provider-neutral contracts, not vendor SDK objects.

Eval/observability requirement:

- Trace every provider call with `trace_id`, provider name, model name, status, error code, retryability, and safe timing metadata.
- Do not log raw prompts, raw documents, API keys, or full exception payloads.

Security/privacy risk:

- Provider errors may contain URLs, payload fragments, headers, or sensitive operational details.
- Use sanitized error envelopes and trace-safe logging.

Implementation task:

- Add deterministic tests for success, model loading, timeout, provider unavailable, and unexpected response-shape cases.

## 8. Interview Bank Additions

20-second answer:

> A provider boundary is the seam between my system and an external AI provider. I keep the provider SDK behind an adapter and expose typed success or failure contracts so the rest of the system stays testable, portable, and safe.

90-second answer:

> In production AI systems, providers are unstable dependencies. Models can be loading, endpoints can time out, SDKs can change response shapes, and failures can contain unsafe details. I isolate that behind a provider adapter. The rest of the application works with provider-neutral Pydantic contracts like `EmbeddingRequest`, `EmbeddingResponse`, and `InferenceProviderError`. That lets me test failures deterministically, avoid vendor lock-in inside core logic, and preserve trace-safe logs.

System-design answer:

> I would design inference as a port-and-adapter layer. Core retrieval or eval code depends on an `InferenceClient` protocol. Each provider implements that protocol behind an adapter. The adapter maps provider-specific SDK responses and exceptions into typed application outcomes. I would add tests for success, timeout, temporary model loading, unavailable provider, and unexpected response shape. I would log only safe metadata with trace IDs and keep raw payloads or secrets out of logs.

## 9. Retrieval Pack

### Flashcards

1. What is a provider boundary?
2. Why should provider SDK imports stay inside an adapter?
3. What is a provider-neutral interface?
4. What is a provider-specific implementation?
5. Why are raw dictionaries risky at AI system boundaries?
6. What is a typed error envelope?
7. What fields should a provider error envelope include?
8. Why does retryability belong in provider errors?
9. What is the difference between `MODEL_LOADING` and `PROVIDER_UNAVAILABLE`?
10. Why might `/healthz` pass while an encode call fails?
11. What should not be logged when provider calls fail?
12. Why is a fake provider useful in tests?
13. What does a smoke test prove?
14. What does a smoke test not prove?
15. How does this design reduce vendor lock-in?

### Scenario questions

1. An embedding provider returns `503` during model warm-up. What error code should the adapter return and why?
2. A provider returns a response without a dense vector field. Should that be retryable?
3. Retrieval code imports the provider SDK directly. What design problem does that create?
4. A trace log includes the full provider exception text. What privacy/security risk exists?
5. The provider succeeds once in a smoke test. What claims are valid and invalid?

### Spot-the-bug questions

1. The RAG pipeline catches `Exception` and returns `"try again later"` as a string.
2. The eval harness checks for `"503"` inside raw exception strings.
3. `retrieval_core` constructs provider-specific SDK objects directly.
4. Provider errors do not include `trace_id`.
5. Timeouts and malformed responses use the same error code.

### Compare/contrast prompts

1. Provider boundary vs API endpoint boundary.
2. Error envelope vs exception string.
3. Provider health check vs model readiness.

### Teach-back prompts

1. Explain provider boundaries to a junior engineer.
2. Explain typed error envelopes to a CTO.
3. Explain why this matters in a RAG reliability audit.

### Design prompts

1. Design a provider-neutral embedding interface for two providers and one fake test client.
2. Design a trace schema for provider calls that avoids logging raw prompts or secrets.

### Brutal final exam question

A startup has a RAG system where user questions sometimes fail, sometimes return stale answers, and sometimes crash when the embedding provider restarts. Design the provider-boundary, error taxonomy, trace fields, and tests needed before you attempt to improve retrieval quality.

## 10. Spaced Repetition Schedule

### 1 day later

Retrieve:

- Definition of provider boundary
- Fields in a typed provider error envelope
- Difference between server health and model readiness

Solve:

- Classify three provider failures into error codes

Explain:

- 20-second provider-boundary answer

Apply/build:

- Sketch a fake provider client for tests

### 3 days later

Retrieve:

- Why provider SDKs should not leak into core packages
- What a smoke test proves and does not prove

Solve:

- Diagnose a broken design where retrieval imports a vendor SDK

Explain:

- 90-second interview answer

Apply/build:

- Write a small Pydantic error envelope from memory

### 7 days later

Retrieve:

- Retryable vs non-retryable provider errors
- Trace-safe metadata fields

Solve:

- Design tests for timeout, model loading, and unexpected response shape

Explain:

- CTO-level explanation of provider-boundary risk

Apply/build:

- Update or create a snippet for typed error envelopes

### 14 days later

Retrieve:

- Port-and-adapter relationship to provider boundaries
- Relationship between provider errors and eval traces

Solve:

- Compare two designs: provider SDK in retrieval core vs adapter layer

Explain:

- How this supports an AI System Evaluation Audit

Apply/build:

- Create an interview-bank system-design answer

### 30 days later

Retrieve:

- Full provider-boundary mental model without notes

Solve:

- Design provider boundary for a RAG system using two embedding providers

Explain:

- Trade-offs between portability, provider-specific features, and abstraction

Apply/build:

- Add a runnable snippet or test case showing typed provider failure handling

## 11. Concept Pages To Create or Update

- `concepts/inference/provider-boundaries.md`
- Later: `concepts/structured-outputs/typed-error-envelopes.md`
- Later: `concepts/observability/trace-safe-provider-logs.md`

## 12. Snippets To Create or Update

No runnable snippet created in this slice.

Possible future snippet:

- `snippets/inference/typed-provider-error-envelope.py`

## 13. Mastery Score

| Area | Score / 100 | Evidence |
|---|---:|---|
| Factual recall | 70 | Can name core concepts, but should practice retrieval without notes |
| Conceptual understanding | 80 | Understands provider boundary and typed error rationale |
| Application | 75 | Applied the pattern in `ai-consultancy-lab` with LLM assistance |
| Debugging/evaluation | 70 | Identified model-loading `503` as a typed failure mode |
| Transfer to real projects | 80 | Can connect this to RAG reliability and provider portability |
| Simple explanation | 75 | Has strong explanation, needs repeated spoken practice |

Next highest-leverage study action:

Practice explaining provider boundaries in 20 seconds, 90 seconds, and system-design depth without reading notes.

## 14. Public Non-Claims

This session does not prove:

- production readiness
- hosted provider readiness
- RAG reliability
- retrieval quality improvement
- customer-data readiness
- full mastery of structured outputs
- full mastery of observability or evals
