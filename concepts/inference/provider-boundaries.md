# Provider Boundaries

## Concept

Name: Provider Boundaries

Category: Inference

Related roadmap week: Week 01 — Foundations

Status:

- Updated
- Needs review before interview-ready

## One-Sentence Definition

A provider boundary is the controlled seam where application-owned contracts meet an external AI provider, SDK, model server, or inference service.

## Technical Definition

A provider boundary isolates provider-specific implementation details behind a stable application interface.

The application should expose typed, provider-neutral contracts such as:

- request schemas
- success response schemas
- failure/error envelopes
- retryability metadata
- trace-safe logging fields

Provider-specific SDK imports, response shapes, exception classes, authentication details, and endpoint quirks should remain inside the adapter.

## Plain-English Explanation

The rest of the system should not care which provider is used.

It should not know whether embeddings come from Superlinked, OpenAI, a local model, or a fake test client.

It should only know:

```text
I sent a valid request.
I received either a valid response or a safe typed error.
```

## Why It Matters In Production AI

AI providers are unstable dependencies.

They can fail because of:

- model loading
- network issues
- timeouts
- endpoint changes
- response-shape drift
- rate limits
- authentication problems
- provider outages

Without a provider boundary, those failures leak into retrieval, eval, agent, or application logic.

That creates brittle systems that are hard to test, hard to debug, hard to replace, and unsafe to log.

A provider boundary gives the system:

- portability
- testability
- failure isolation
- safer logs
- clearer retry behavior
- vendor-change resilience
- better eval and trace evidence

## Minimal Example

```python
class InferenceClient(Protocol):
    def encode_text(self, request: EmbeddingRequest) -> EmbeddingCallResult:
        ...
```

The caller depends on `InferenceClient`.

The implementation can be:

```text
SuperlinkedInferenceClient
OpenAIInferenceClient
LocalEmbeddingClient
FakeInferenceClient
```

The caller should not depend on the provider SDK.

## Counterexample

Bad design:

```python
from sie_sdk import SIEClient

def retrieve(query: str) -> list[str]:
    client = SIEClient(...)
    embedding = client.encode(...)
    ...
```

Why this is bad:

- retrieval code is coupled to Superlinked
- tests require the real SDK or heavy mocking
- provider exceptions leak into retrieval logic
- swapping providers becomes expensive
- failure handling becomes scattered

Better design:

```text
retrieval_core
-> InferenceClient protocol
-> SuperlinkedInferenceClient adapter
-> SIE SDK/server
```

## Implementation Pattern

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


class EmbeddingRequest(BaseModel):
    trace_id: str
    text: str
    model: str


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
    status: Literal[InferenceStatus.PROVIDER_ERROR] = InferenceStatus.PROVIDER_ERROR


EmbeddingCallResult = EmbeddingResponse | InferenceProviderError
```

## Common Failure Modes

| Failure mode | Symptom | Likely cause | Fix |
|---|---|---|---|
| SDK leakage | Core code imports provider SDK directly | No adapter boundary | Introduce provider-neutral interface |
| Raw exception leakage | User-facing crash or unsafe logs | Exceptions cross boundary untyped | Map exceptions into error envelopes |
| Model-loading confusion | Health checks pass but encode fails | Model not loaded yet | Add `MODEL_LOADING` retryable error code |
| Response-shape drift | Code crashes on missing fields | Provider changed/returned unexpected format | Validate and map to `UNEXPECTED_RESPONSE` |
| Unclear retry behavior | System retries permanent failures or gives up too early | No retryability field | Add `retryable` metadata |
| Weak tests | Tests require live provider for all cases | No fake provider/client injection | Add fake clients and deterministic unit tests |

## Debugging Questions

1. Which package imports the provider SDK?
2. Can the core app run tests without a live provider?
3. Are provider failures typed or thrown as raw exceptions?
4. Do provider errors include trace IDs?
5. Are timeouts, model loading, and malformed responses distinct?
6. Are raw prompts, documents, secrets, or full exception strings logged?
7. Can another provider be added without rewriting retrieval or eval logic?
8. Does a smoke test prove only connectivity, or broader reliability?
9. Is retryability explicit?
10. Are provider-specific response shapes validated before downstream use?

## Eval / Measurement Angle

Provider-boundary behavior can be evaluated with fixed diagnostic cases:

- success response returns expected schema
- model-loading failure maps to retryable `MODEL_LOADING`
- timeout maps to retryable `TIMEOUT`
- unavailable provider maps to retryable `PROVIDER_UNAVAILABLE`
- malformed response maps to non-retryable `UNEXPECTED_RESPONSE`
- trace events include safe metadata only
- no raw provider exception text leaks into public logs

Relevant metrics:

- failure classification accuracy
- retryable vs non-retryable correctness
- test coverage of provider outcomes
- trace completeness
- unsafe logging incidents

Relevant traces:

- `trace_id`
- provider
- model
- request type
- status
- error code
- retryable
- duration
- safe failure message

Regression risk:

A future provider integration may bypass the interface and reintroduce SDK coupling.

## Security / Privacy Angle

Risks:

- raw exception strings may contain URLs, payload fragments, headers, or operational details
- logs may accidentally include raw prompts or documents
- provider SDK objects may expose sensitive configuration
- failed calls may leak API keys if errors are logged carelessly

Controls:

- sanitize provider errors
- log trace-safe metadata only
- keep API keys in environment variables
- keep provider-specific details inside adapters
- test that `.env` and private payloads are not committed or logged

What should not be logged:

- API keys
- authorization headers
- raw prompts
- raw customer documents
- full provider exception payloads
- secrets
- personally identifiable information

## Interview Answers

### 20-second answer

A provider boundary is the seam between my application and an external AI provider. I keep provider SDKs behind adapters and expose typed success or failure contracts so the rest of the system stays portable, testable, and safe.

### 90-second answer

In production AI systems, providers are unstable dependencies. Models can be loading, endpoints can time out, SDKs can change response shapes, and failures can contain sensitive operational details. I isolate those risks behind a provider boundary. Core application code depends on a provider-neutral interface, while provider-specific SDK calls live inside adapters. The adapter maps successful responses and failures into typed application contracts, such as an embedding response or a provider error envelope. That makes failures testable, logs safer, and provider swaps less expensive.

### System-design answer

I would design inference as a port-and-adapter layer. Retrieval, eval, and agent code depend on an `InferenceClient` protocol, not a vendor SDK. Each provider implements that interface behind an adapter. The adapter validates responses, maps exceptions to typed error envelopes, includes retryability metadata, and emits trace-safe events. I would add deterministic unit tests for success, timeout, temporary model loading, unavailable provider, and unexpected response shape. I would treat a live smoke test as connectivity proof, not production readiness.

## Compare / Contrast

| This concept | Commonly confused with | Difference |
|---|---|---|
| Provider boundary | API endpoint boundary | Provider boundary controls external AI dependency behavior; API endpoint boundary controls client-facing app behavior |
| Provider health check | Model readiness | Server can be healthy while a specific model is still loading |
| Error envelope | Exception string | Error envelope is typed and safe for downstream logic; exception string is unstable and may leak details |
| Provider-neutral interface | Provider SDK | Interface is app-owned; SDK is vendor-owned |
| Smoke test | Reliability eval | Smoke test proves basic connectivity; eval tests behavior across fixed cases |

## Related Concepts

- Structured outputs
- Typed error envelopes
- Ports and adapters
- Trace-safe logging
- Retrieval reliability
- Eval harnesses
- Retryability
- Vendor lock-in

## Related Snippets

Future snippet:

- `snippets/inference/typed-provider-error-envelope.py`

## Related Session Files

- `weeks/week-01-foundations/2026-06-13-provider-boundaries-and-error-envelopes-session.md`

## Current Gaps

- Add a runnable snippet that demonstrates success and provider failure mapping.
- Add a separate concept page for typed error envelopes.
- Add a separate concept page for trace-safe provider logging.
- Practice explaining this without reading notes.

## Last Updated

Date: 2026-06-13
