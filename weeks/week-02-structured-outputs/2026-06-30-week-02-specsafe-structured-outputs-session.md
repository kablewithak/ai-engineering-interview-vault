# SpecSafe / DSpark-Inspired Verification Policy Lab — Week 02 Study Session

## Metadata

- Date: 2026-06-30
- Roadmap week: Week 02 — Structured Outputs, Pydantic Validation, and Error Taxonomy
- Session title: SpecSafe / DSpark-Inspired Verification Policy Lab — Structured Output and Reliability Closeout
- Source material:
  - SpecSafe Phase 3 calibration closeout handover
  - SpecSafe PRD
  - frozen calibrator contract
  - held-out calibration fitness and promotion gate
- Current level: Pre-study baseline not yet assessed
- Available time: To be recorded during the session
- Purpose: Convert Week 02 structured-output doctrine into applied understanding through the separate SpecSafe / DSpark-inspired verification-policy implementation: strict Pydantic contracts, exact-type boundaries, machine-readable errors, frozen evidence artifacts, held-out gates, and explicit non-promotion.
- Related concepts:
  - Structured outputs as deterministic system contracts
  - Pydantic v2 strict validation and immutable artifacts
  - Typed error taxonomy and exact-type boundary enforcement
  - Causal decision-time information boundaries
  - Held-out evaluation and split leakage
  - Negative results and promotion gates
  - Evidence maturity and bounded claims
- Related snippets:
  - None at pre-study
- Related interview-bank files:
  - None at pre-study

## Week 02 Alignment

This study session belongs in **Week 02** even though SpecSafe is a separate repository and a separate research-grade implementation project.

Week 02 doctrine becomes concrete in SpecSafe through:

| Week 02 focus | SpecSafe implementation evidence |
|---|---|
| Pydantic v2 as a deterministic contract boundary | Strict immutable contracts for runtime context, trace fixtures, calibrator artifacts, and held-out reports |
| Structured outputs | Typed decisions, artifact schemas, metric records, and promotion decisions rather than vague dictionaries |
| Validation and explicit refusal/fallback states | Exact-type rejection, strict unknown-field validation, machine-readable errors, blocked promotion, and runtime-control ineligibility |
| Error taxonomy | Causal-safety violations, invalid fixture/artifact errors, split/fixture mismatches, and deterministic persistence failures |
| Retry/fallback discipline | Calibration does not silently retry or retune after final evaluation; held-out regression becomes an explicit stop decision |
| Production-shaped system behavior | Inputs, outputs, validators, test cases, evidence artifacts, traces, non-claims, and maintenance boundaries are inspectable |

The learning claim is not that SpecSafe is a Week 02 replacement. The correct claim is that it is an additional project that **applies and stress-tests Week 02 engineering doctrine**.

## 1. Learning Objectives

| Objective | Bloom level | Category | Why it matters |
|---|---|---|---|
| Explain why final-evaluation evidence cannot be used to tune a calibrator after results are observed. | Understand / Evaluate | Evaluation/debugging skill | Prevents split leakage and fake improvement claims. |
| Distinguish causal validity, calibration fitness, and policy utility. | Analyze | Core concept | Prevents one gate from being mistaken for evidence of another. |
| Defend a negative held-out result as valid reliability evidence. | Evaluate | Evaluation/debugging skill | Production systems need trustworthy stop decisions, not only positive metrics. |
| Identify information available to a runtime scheduler versus evaluation-only information. | Apply | Implementation skill | Protects the causal information boundary. |
| Explain why unsafe retrospective controls are retained but never promoted. | Analyze | Evaluation/debugging skill | Demonstrates why apparent utility cannot override causal validity. |
| State current SpecSafe claims and non-claims accurately. | Understand / Apply | Business/application insight | Prevents overclaiming in interviews, portfolios, and client conversations. |
| Recommend the next legitimate engineering response after a held-out calibration regression. | Create / Evaluate | Production transfer | Keeps future work governed rather than tuned to a single held-out fixture. |

Categories:

- Core concept
- Implementation skill
- Evaluation/debugging skill
- Business/application insight

## 2. Pre-Test Before Teaching

These seven questions are locked for this study session. Do not revise the wording after answers are observed.

1. **Held-out integrity**  
   We fitted the frozen calibrator on the `calibration` split, then assessed it on `final_evaluation`. Why would changing the calibrator’s bin count, fallback rule, or fitting thresholds after seeing the held-out regression invalidate the result, even if the revised calibrator performed better on that same final-evaluation fixture?

2. **Three different gates**  
   Explain the difference between:
   - causal validity;
   - calibration fitness;
   - policy utility.

   Why is it unsafe to treat success at one of these gates as proof of the others?

3. **Negative result discipline**  
   The held-out assessment did not promote the current calibrator. Why is that a useful outcome for a reliability project rather than simply a failed implementation?

4. **Runtime versus evaluation information**  
   What information may a valid runtime scheduler receive through `CausalSchedulerContext`, and what evaluation-only information must remain unavailable to it? Give at least two examples of forbidden information.

5. **Unsafe retrospective control**  
   Why does the project retain an intentionally unsafe retrospective look-ahead policy? What would make any apparently strong result from that policy invalid for a real policy comparison?

6. **Evidence and claim boundary**  
   What can SpecSafe truthfully claim at the current merged state, and name at least three claims it still cannot make.

7. **Next-decision discipline**  
   The current calibrator regressed on held-out evidence and adaptive scheduling remains blocked. What is the legitimate next engineering decision, and what shortcut would violate the project’s reliability rules?

## 3. My Raw Attempt Summary

Complete after answering all seven questions without notes.

- Answer quality summary:
- Concepts explained clearly:
- Concepts explained partially:
- Concepts I could not yet explain:
- Confidence level before grading:
- Evidence used for self-assessment:

Public-safe summary only. Raw or private answers belong in `.local/`.

## 4. Error Map

### What I knew

- To be completed after grading.

### What I partly knew

- To be completed after grading.

### What I confused

- To be completed after grading.

### What I missed

- To be completed after grading.

### False confidence detected

- To be completed after grading.

## 5. Corrected Mental Models

### Concept: Held-out evaluation is a decision boundary, not a tuning resource

Technical explanation:

- To be completed after teaching.

Plain-English explanation:

- To be completed after teaching.

Concrete example:

- To be completed after teaching.

Counterexample:

- To be completed after teaching.

Production AI engineering example:

- To be completed after teaching.

### Concept: Causal validity, calibration fitness, and policy utility are independent gates

Technical explanation:

- To be completed after teaching.

Plain-English explanation:

- To be completed after teaching.

Concrete example:

- To be completed after teaching.

Counterexample:

- To be completed after teaching.

Production AI engineering example:

- To be completed after teaching.

### Concept: A retained negative result is a reliability asset

Technical explanation:

- To be completed after teaching.

Plain-English explanation:

- To be completed after teaching.

Concrete example:

- To be completed after teaching.

Counterexample:

- To be completed after teaching.

Production AI engineering example:

- To be completed after teaching.

## 6. Code / Implementation Pattern

```python
# Study after the pre-test:
#
# calibration_split
#     -> fit and freeze calibrator artifact
#     -> final_evaluation split
#     -> assess raw versus calibrated probabilities
#     -> retain typed promotion decision
#
# Final-evaluation results must never re-enter fitting or threshold selection.
```

What this proves:

- To be completed after teaching.

Failure it prevents:

- To be completed after teaching.

How to explain it in an interview:

- To be completed after teaching.

## 7. Production Transfer

Capstone application:

- A runtime decision policy should be gated by causal validity, evidence fitness, and outcome utility separately.

Client use case:

- To be completed after teaching.

Architecture decision:

- Keep runtime-visible inputs, post-hoc labels, calibration artifacts, and promotion decisions in separate typed boundaries.

Eval/observability requirement:

- Retain split identity, artifact provenance, metrics, promotion status, and reasons for blocking promotion.

Security/privacy risk:

- Logging or passing post-hoc labels into runtime logic can create leakage, unsafe behavior, and misleading evidence.

Implementation task:

- To be completed after teaching.

## 8. Interview Bank Additions

20-second answer:

- To be completed after teaching.

90-second answer:

- To be completed after teaching.

System-design answer:

- To be completed after teaching.

## 9. Retrieval Pack

### Flashcards

1. What is held-out evaluation?
2. Why may a held-out split assess a frozen artifact but not tune it?
3. What is split leakage?
4. What does causal validity test?
5. What does calibration fitness test?
6. What does policy utility test?
7. Why can a policy have valid causal inputs but still be unfit for runtime control?
8. Why can a calibrated confidence signal still fail to improve policy utility?
9. What is a negative control?
10. Why retain an unsafe retrospective policy?
11. Name two forbidden inputs for a valid runtime scheduler.
12. What is a promotion gate?
13. What does “not promoted” mean in a reliability harness?
14. What is the difference between synthetic evidence and production evidence?
15. What must happen before adaptive scheduling research can resume in SpecSafe?

### Scenario questions

1. A team changes a calibration threshold after seeing poor final-evaluation results. What failure has occurred and what should happen next?
2. A scheduler uses a future target acceptance outcome and produces better apparent utility. Is the result valid? Why?
3. A calibrator improves Brier score but the scheduling policy performs worse under the same capacity profile. What does this show?
4. A held-out report says “not promoted.” How would you explain this to a CTO as evidence of engineering quality rather than project failure?
5. A developer proposes using the final-evaluation fixture to choose a new bin count “just once.” What boundary does this violate?

### Spot-the-bug questions

1. A runtime policy receives `observed_acceptance` with its scheduling context. What is wrong?
2. A report says “faster policy” but does not state its causal-safety result. What is missing?
3. A calibrator artifact records no fixture-set version. What reproducibility risk does this create?
4. A team replaces a failed calibrator but reuses the same final-evaluation case to select its parameters. What is invalid?
5. A retrospective oracle appears first on a valid-policy leaderboard. What classification error occurred?

### Compare/contrast prompts

1. Calibration split versus final-evaluation split.
2. Causal-safety gate versus calibration-fitness gate.
3. Negative control versus deployable policy.

### Teach-back prompts

1. Explain held-out integrity to a junior engineer in 60 seconds.
2. Explain why a negative calibration result can be valuable to a non-technical stakeholder.
3. Explain why an unsafe retrospective policy can look strong but still be invalid.

### Design prompts

1. Design a typed promotion gate that prevents a new calibrator from reaching policy research until held-out evidence is recorded.
2. Design a trace field boundary that prevents post-hoc outcomes from reaching a runtime scheduler.

### Brutal final exam question

1. A team has a causally valid scheduler that uses only decision-time inputs. Its raw confidence is poorly calibrated, its frozen calibration artifact regresses on held-out data, and an unsafe retrospective control reports the best utility. Explain exactly what the team may claim, what it must block, what evidence it should retain, and the next valid experiment.

## 10. Spaced Repetition Schedule

### 1 day later

Retrieve:

- Answer all seven locked questions without notes.

Solve:

- Explain why re-fitting after held-out regression is leakage.

Explain:

- Teach the three gates to a junior engineer.

Apply/build:

- Inspect the SpecSafe held-out calibration report and identify the promotion decision.

### 3 days later

Retrieve:

- Define causal validity, calibration fitness, and policy utility in one sentence each.

Solve:

- Classify three hypothetical failures as causal leakage, calibration failure, or utility failure.

Explain:

- Explain why a negative result can be a successful control outcome.

Apply/build:

- Draft a typed status enum for a promotion gate.

### 7 days later

Retrieve:

- Re-answer Questions 2, 4, and 7 without notes.

Solve:

- Identify the invalid step in a short final-evaluation tuning scenario.

Explain:

- Give a 90-second interview answer on held-out evaluation in AI systems.

Apply/build:

- Review whether a new policy proposal respects SpecSafe’s evidence boundary.

### 14 days later

Retrieve:

- Reconstruct the Phase 3 state of SpecSafe from memory.

Solve:

- Compare a valid baseline, a candidate adaptive policy, and an unsafe retrospective control.

Explain:

- Explain evidence maturity and non-claims to a hiring manager.

Apply/build:

- Update one concept page if any definition remains weak.

### 30 days later

Retrieve:

- Answer all seven locked questions in sequence without notes.

Solve:

- Design an independent calibration-and-held-out-evaluation plan for a retrieval confidence feature.

Explain:

- Give a complete system-design explanation that distinguishes runtime and evaluation information.

Apply/build:

- Create or improve a runnable snippet only if it clarifies a real boundary.

## 11. Concept Pages To Create or Update

- `concepts/evals/held-out-evaluation-and-split-leakage.md` — planned; create only after the session proves the concept is understood.
- `concepts/evals/calibration-fitness-and-promotion-gates.md` — planned; create only after the session proves the concept is understood.
- `concepts/inference/causal-runtime-information-boundary.md` — planned; create or update only if the current explanation needs a distinct reusable page.
- `concepts/evals/negative-results-and-evidence-maturity.md` — planned; create only if it avoids duplicating the pages above.

## 12. Snippets To Create or Update

- `snippets/evals/heldout-promotion-gate.py` — planned; create only if a small typed example improves understanding.
- `snippets/inference/causal-context-guard.py` — planned; create only if it is distinct from existing SpecSafe source evidence.

## 13. Mastery Score

| Area | Score / 100 | Evidence |
|---|---:|---|
| Factual recall |  | Pre-test and delayed retrieval |
| Conceptual understanding |  | Distinguishes the three gates accurately |
| Application |  | Can classify split-leakage and runtime-boundary scenarios |
| Debugging/evaluation |  | Can identify why a result is invalid or non-promotable |
| Transfer to real projects |  | Can map the gates to another AI system |
| Simple explanation |  | Can explain the result without jargon |

Next highest-leverage study action:

- Complete the seven-question pre-test, receive strict grading, then focus teaching on the weakest gate distinction.

## 14. Public Non-Claims

This session does not prove:

- production-serving experience;
- live-traffic throughput or latency gains;
- universal calibration or scheduling competence;
- that a passing study answer equals production system ownership;
- that the current SpecSafe calibrator is promotable;
- that an unsafe retrospective policy is deployable.
