# Provider Boundaries

## Interview Target

Use this file to answer technical and system-design questions about provider boundaries in AI systems.

## Core Message

Provider boundaries keep unstable external AI dependencies behind stable application-owned contracts.

## 20-Second Answer

A provider boundary is the seam between my application and an external AI provider. I keep provider SDKs behind adapters and expose typed success or failure contracts so the rest of the system stays portable, testable, and safe.

## 90-Second Answer

In production AI systems, providers are unstable dependencies. Models can be loading, endpoints can time out, SDKs can change response shapes, and failures can contain sensitive operational details.

I isolate those risks behind a provider boundary. Core application code depends on a provider-neutral interface, while provider-specific SDK calls live inside adapters.

The adapter maps successful responses and failures into typed application contracts, such as an embedding response or a provider error envelope. That makes failures testable, logs safer, and provider swaps less expensive.

In my AI reliability lab, the Superlinked adapter sits behind an `InferenceClient` contract. A temporary `503` during model loading is mapped into a retryable `MODEL_LOADING` error instead of leaking as a raw SDK exception.

## System-Design Answer

I would design inference as a port-and-adapter layer.

Core packages such as retrieval, evals, and agents should depend on an `InferenceClient` protocol, not on a provider SDK.

The provider adapter should handle:

- SDK initialization
- request conversion
- response validation
- exception mapping
- retryability classification
- trace-safe failure metadata

The app-owned contract should include:

- request schema
- success response schema
- provider error envelope
- error code taxonomy
- trace ID
- provider name
- model name
- retryability flag

I would add deterministic unit tests for:

- success
- timeout
- temporary model loading
- unavailable provider
- unexpected response shape

I would also run a live smoke test, but I would be careful with the claim. A smoke test proves basic connectivity, not production readiness.

## Concrete Project Example

In `ai-consultancy-lab`, the first provider adapter used Superlinked's local Inference Engine through Docker.

The happy path proved that a local SIE endpoint could return embeddings.

But the important reliability lesson came from a failure:

```text
health endpoint: ok
ready endpoint: ok
encode call: 503 Service Unavailable
cause: model still downloading/loading
```

That became a typed provider error:

```text
ProviderErrorCode.MODEL_LOADING
retryable=True
```

This made the provider failure inspectable, testable, and safer to log.

## Trade-Offs

### Benefit: portability

A provider-neutral interface makes it easier to add or swap providers later.

### Cost: abstraction overhead

The adapter requires extra schemas and tests.

### Benefit: safer failure handling

Typed errors avoid raw exception leakage.

### Cost: provider-specific details may be hidden

Some provider capabilities may require carefully designed extension points.

### Benefit: better tests

Fake clients can simulate success and failure without live infrastructure.

### Cost: fake tests do not replace live validation

Live smoke tests are still needed for integration evidence.

## Common Follow-Up Questions

### Why not just call the provider SDK directly?

Because direct SDK usage couples core logic to one provider, makes tests harder, and spreads provider-specific failure handling across the codebase.

### Does this mean you ignore provider-specific features?

No. Provider-specific features can exist inside the adapter or behind explicit extension methods. The point is not to hide useful capability. The point is to stop provider details from leaking everywhere.

### Is this over-engineering for a small project?

Not if the system will become a reliability lab. The boundary is small, but it prevents future coupling when retrieval, evals, agents, and reporting are added.

### What does this have to do with evals?

Provider failures affect eval results. If failures are typed, the eval harness can distinguish model-loading failure, timeout, malformed response, and retrieval failure instead of treating everything as a generic crash.

### What does this have to do with privacy?

Provider exception strings can contain sensitive operational details. A typed error envelope lets the system log safe metadata instead of raw provider payloads.

## Failure Modes To Mention

- Provider SDK imported directly in retrieval code
- Raw exceptions leak across boundaries
- No distinction between timeout and malformed response
- Health check mistaken for model readiness
- Smoke test overclaimed as production validation
- Logs include raw prompts, documents, or exception payloads
- No fake provider for deterministic tests
- No retryability metadata

## Non-Claims

This design does not prove:

- production readiness
- provider superiority
- hosted deployment readiness
- retrieval quality
- RAG correctness
- customer-data readiness

It proves a narrower but important thing:

```text
The system has an inspectable provider boundary with typed success and failure outcomes.
```

## Strong Closing Line

I do not treat provider calls as trusted internal functions. I treat them as unstable external dependencies and put a typed, traceable, testable boundary around them.
