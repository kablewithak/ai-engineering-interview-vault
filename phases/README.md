# Phase-Based Study Chronology

## Status

- Active from: 2026-09-04
- Governing model: AI Engineer Roadmap Control V1
- Active chronology: `phases/`
- Historical chronology: `weeks/`

## Purpose

The roadmap now executes by **phase** rather than by week.

`phases/` is the active chronological learning and diagnostic trail. It records what happened while executing a roadmap phase: study sessions, diagnostics, remediation, ownership checks, interview practice, and gate evidence.

The roadmap and repository do not need to have identical internal structures. The roadmap defines capability order and promotion gates; this repository stores learning and interview evidence in the most maintainable shape for the Vault.

## Historical Week Sessions Are Preserved

The existing `weeks/` tree is historical evidence from the previous roadmap.

HARD rules:

- Do not delete existing week sessions because the roadmap changed.
- Do not rename or move historical week sessions into phase folders.
- Do not reinterpret `week-02` as `phase-02`, or any other week as a new-roadmap phase.
- Do not create new study sessions under `weeks/` after the phase cutover unless a historical repair specifically requires it.
- Historical week artifacts may be referenced as prior evidence by any later phase.

The old week number records when and under which earlier roadmap structure the learning happened. The new phase number records the capability dependency currently being executed. They are different identities.

## Active Phase Shape

Create phase folders only when the phase becomes active. Do not scaffold empty phase directories merely for neatness.

Preferred shape:

```text
phases/
  README.md
  phase-00-reconciliation-diagnostics/
    README.md
    YYYY-MM-DD-topic-session.md
  phase-01-python-foundations/
    README.md
    YYYY-MM-DD-topic-session.md
```

Each active phase directory should contain:

- a `README.md` with phase state, purpose, gates, boundaries, evidence destinations, and next gate;
- dated session files for the actual learning/diagnostic work performed during that phase.

## Session Metadata

New phase session files should record at minimum:

- date;
- roadmap phase;
- phase workstream or gate;
- session title;
- session type: study / diagnostic / debugging / code reading / defence / mixed assessment;
- source material;
- current evidence state;
- purpose;
- related concept, snippet, interview-bank, or Lab evidence where relevant.

The session's metadata links repository evidence back to the logical roadmap gate. The folder name alone is not the roadmap control system.

## Other Vault Surfaces Stay Capability-Oriented

Do not reorganize the rest of the Vault into phase folders.

```text
concepts/          = best current reusable understanding
interview-bank/    = spoken and technical assessment material
mistake-patterns/  = recurring public-safe failure patterns
review-schedules/  = delayed retrieval and retention work
snippets/          = runnable or near-runnable learning examples
templates/         = reusable artifact shapes
prompts/           = reusable learning workflows
weeks/             = historical chronological sessions
phases/            = active chronological sessions
```

A concept may be introduced in one phase, remediated in another, and assessed again much later. Topic-oriented canonical files prevent duplication.

## Relationship to `ai-consultancy-lab`

The two-repository boundary remains unchanged.

```text
ai-engineering-interview-vault
= Can I explain, reproduce, recall, debug, and defend it?

ai-consultancy-lab
= Can I build, test, measure, and prove it?
```

Do not add phase directories to `ai-consultancy-lab` merely for symmetry. A Vault phase session should reference the relevant Lab package, test, experiment, report, or evidence path when production-shaped transfer exists.

## Historical Evidence Mapping

When prior week work is relevant to the new roadmap, record the mapping in the active phase rather than moving the old artifact.

Example:

```text
Historical Vault evidence:
- weeks/week-02-structured-outputs/...

Lab evidence:
- packages/structured_outputs/
- tests/unit/structured_outputs/

Phase 0 reconciliation state:
- REUSE

Later capability use:
- Python engineering boundaries
- evaluation/reliability contracts
- model/tool output validation
```

## Control Rule

Before creating a new artifact, answer both questions:

1. **Roadmap:** What capability, workstream, or promotion gate is active?
2. **Repository:** Where does this kind of evidence naturally belong in the existing Vault architecture?

If an appropriate repository surface already exists, use it. Create the smallest new boundary necessary when it does not.
