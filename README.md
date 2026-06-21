# CompuSci AI — A Guardrailed System Prompt for Computational Materials Science

A system prompt that turns a general-purpose LLM into a constrained scientific
reasoning agent for computational materials science — designed specifically to
stop the model from fabricating data, citations, or property values when it
answers DFT, band-gap, or materials-screening questions.

## The Problem

LLMs asked materials-science questions ("what's the band gap of TiO₂", "rank
sensing materials for VOC X") will confidently produce plausible-looking
numbers, citations, and material IDs that are partially or entirely invented.
This is especially dangerous in a research context because the output *looks*
rigorous — formatted, cited, quantified — while having no real grounding.

This prompt is an attempt to close that gap structurally, not just by asking
the model nicely to "not hallucinate."

## Design Principles

**1. Source tiering, not vibes.**
Every claim must be traced to one of four source tiers (user-provided data →
primary databases like Materials Project/ICSD → peer-reviewed literature,
ranked by SCImago SJR quartile or Impact Factor → computational predictions).
Vague labels like "high-impact journal" are explicitly banned in favor of a
quantified rank, e.g. `[TIER 3A | SJR Q1 | MIT]`.

**2. Capability honesty before content.**
Before answering anything, the model must output a *Capability Declaration
Block* stating what tools/retrieval/web search are *actually* connected in
that session — not what the prompt wishes were available. If no real
retrieval is possible, every value gets labeled
`RECALLED FROM MODEL TRAINING — UNVERIFIED` and confidence is downgraded
accordingly. This exists because a model can satisfy a prompt's *formatting*
requirements while having zero way to verify what it's reporting — and a
well-formatted hallucination is more dangerous than an obvious one.

**3. Domain-correct terminology enforcement.**
The prompt explicitly prevents a common error: applying HOMO/LUMO (a
molecular-orbital concept) to bulk solid-state materials, where the correct
descriptors are VBM/CBM (valence band maximum / conduction band minimum).
This distinction is stated multiple times across sections specifically
because it's an easy and common conflation.

**4. Adversarial self-review before responding.**
The model simulates six separate auditor "sub-agents" before finalizing any
answer — each checking a different failure mode (requirements, context,
sourcing, hallucination, scientific precision, output structure) rather than
one vague "check your work" pass.

**5. Epistemic labeling throughout.**
Every output is split into `FACT | ASSUMPTION | INFERENCE | RECOMMENDATION`,
and every report ends with a Data Confidence Report table mapping each claim
to its source tier and confidence level.

## Structure

| Section | Purpose |
|---|---|
| Capability Declaration | Forces honest disclosure of real session capabilities |
| 1 — Role & DFT Framework | Domain expertise + functional-selection guidance (LDA/GGA/HSE06) |
| 2 — Objective | The 8 invariants every response must satisfy |
| 3 — Source Hierarchy | 4-tier sourcing with quantified journal ranking |
| 4 — Anti-Hallucination & Skills | Hard guardrails + real tool registry (pymatgen, ASE, VASP, etc.) |
| 5 — Required Workflow | Step-by-step DFT/MD analysis procedure |
| 6 — Output Format | Mandatory structured report template |
| 7 — Adversarial Sub-Agent Review | 6-persona self-critique pass |
| 8 — Follow-Up Questions | User-directed next steps |
| 9 — Open Scientific Questions | Field-level unresolved gaps (distinct from Section 8) |
| 10 — Final Instruction | End-to-end execution checklist |

## Iteration Example

An earlier version of this prompt stated RAG retrieval as unconditionally
mandatory, with no instruction for sessions where no retrieval tool was
actually connected. In practice, this meant a tool-less deployment would
either violate the prompt (by not retrieving) or silently fabricate the
appearance of retrieval — exactly the failure mode the prompt was designed
to prevent.

The fix: RAG is now conditional on tools genuinely being available, with an
explicit, mandatory fallback (`RECALLED FROM MODEL TRAINING — UNVERIFIED`)
for when they're not — closing the gap between what the prompt claims and
what a given deployment can actually do.

## Usage

See [`prompt/system_prompt.md`](prompt/system_prompt.md) for the full prompt.
Drop it in as a system prompt for any LLM meeting the minimum requirements
(long context window, structured output, instruction-following). See
[`examples/sample_query_output.md`](examples/sample_query_output.md) for a
worked example.

## Background

Developed and iterated on while working on materials-science / synthetic
biology coursework and internship work, as a way to make LLM-assisted
literature and property review auditable rather than trust-by-default.
