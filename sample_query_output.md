# Sample Query & Output

This demonstrates the prompt running in a **tool-less session** — no live
database, RAG, or web search connected. This is intentionally the harder,
more common case: it shows the Capability Declaration and unverified-recall
labeling working as designed, rather than a best-case scenario with full
database access.

---

## User Query

> "I'm screening metal oxide materials for a VOC gas sensor targeting
> ethanol. Compare ZnO, SnO₂, and TiO₂ as candidates."

---

## Model Output

┌─ SESSION CAPABILITY DECLARATION ───────────────────────────────┐
│ Tools active        : None                                      │
│ RAG / retrieval      : NO                                        │
│ Web search           : NO                                        │
│ Database APIs        : NONE                                      │
│ Context window       : ~200K tokens (session estimate)           │
│ Confidence mode      : TRAINING-RECALL ONLY                      │
└─────────────────────────────────────────────────────────────────┘

**No retrieval tools are connected in this session.** Per the RAG Protocol
fallback, all property values below are labeled `RECALLED FROM MODEL
TRAINING — UNVERIFIED` rather than presented as retrieved/verified data, and
all confidence ratings are downgraded accordingly. Treat every number as a
starting point for literature/database verification, not a citable result.

### Problem Summary
Comparing ZnO, SnO₂, and TiO₂ as candidate metal-oxide sensing materials for
ethanol (VOC) detection, evaluating electronic structure suitability and
expected sensing behavior.

### Material / System Profile

| Material | Crystal System | Space Group (typical) | Source |
|---|---|---|---|
| ZnO | Hexagonal (wurtzite) | P6₃mc | RECALLED — UNVERIFIED |
| SnO₂ | Tetragonal (rutile) | P4₂/mnm | RECALLED — UNVERIFIED |
| TiO₂ (rutile) | Tetragonal | P4₂/mnm | RECALLED — UNVERIFIED |

*Not found in verified sources — material IDs and exact lattice parameters
require Materials Project / ICSD lookup, not performed in this session.*

### DFT & Electronic Structure Analysis

| Material | Band Gap (GGA-PBE, recalled) | Experimental band gap (recalled) | Type |
|---|---|---|---|
| ZnO | ~0.8 eV (typical GGA underestimate) | ~3.4 eV | Direct |
| SnO₂ | ~0.6–1.0 eV (typical GGA underestimate) | ~3.6 eV | Direct |
| TiO₂ (rutile) | ~2.06 eV (commonly cited GGA-PBE value) | ~3.0 eV | Indirect |

DFT functional assumed: GGA-PBE (most common default; known to underestimate
band gaps by ~30–50% relative to experiment — see Section 1 functional
table). **These specific numeric values are recalled from training data, not
retrieved from a live calculation or database in this session**, and should
be verified against Materials Project before use in any report or decision.

### Interaction Analysis (Ethanol / VOC)

Per the prompt's HOMO-LUMO vs. VBM/CBM distinction:
- **Solid material side:** described using band gap / VBM / CBM (above).
- **Target molecule (ethanol) side:** ethanol's HOMO-LUMO gap and orbital
  energies are not reliably recalled with citable precision here — flagged
  as `Data Unavailable` rather than estimated, per the Zero Hallucination
  rule (Section 4.1).
- Qualitative expectation only (not quantified, per Scientific Precision
  rule): n-type oxide semiconductors like ZnO and SnO₂ are commonly reported
  in the literature as ethanol-sensitive via surface oxygen-vacancy
  chemisorption mechanisms; this is a general field pattern, not a specific
  verified value for these exact materials.

### Candidate Comparison Table

| Material | Band Gap | Adsorption E | Sensitivity (qualitative) | Selectivity | Op. Temp | Stability | Source [TIER \| Rank \| Institution] |
|---|---|---|---|---|---|---|---|
| ZnO | ~3.4 eV (exp., recalled) | Data Unavailable | Reported as high in literature (general pattern) | Data Unavailable | Typically 200–400°C (recalled, unverified) | Generally stable | TIER 4 \| RECALLED — UNVERIFIED |
| SnO₂ | ~3.6 eV (exp., recalled) | Data Unavailable | Reported as high in literature (general pattern) | Data Unavailable | Typically 200–400°C (recalled, unverified) | Generally stable | TIER 4 \| RECALLED — UNVERIFIED |
| TiO₂ | ~3.0 eV (exp., recalled) | Data Unavailable | Lower than ZnO/SnO₂ in general literature pattern | Data Unavailable | Higher, often >400°C (recalled, unverified) | Highly stable | TIER 4 \| RECALLED — UNVERIFIED |

### Data Confidence Report

| Claim | Type | Source [TIER \| Rank \| Institution] | Confidence |
|---|---|---|---|
| ZnO/SnO₂/TiO₂ crystal systems | FACT (general chemistry) | TIER 4 \| RECALLED — UNVERIFIED | MEDIUM |
| Specific band gap values (eV) | ASSUMPTION (recalled, not retrieved) | TIER 4 \| RECALLED — UNVERIFIED | LOW |
| Ethanol sensitivity ranking | INFERENCE (general literature pattern) | TIER 4 \| RECALLED — UNVERIFIED | LOW |
| Adsorption energies, selectivity values | N/A | Data Unavailable | — |

### Actionable Next Steps
- Query Materials Project (`mp-api`) for verified ZnO, SnO₂, and TiO₂ band
  structures and formation energies before citing any numeric value.
- Run a literature search (Scopus/Web of Science) for ethanol-sensing
  studies on these three oxides, filtered to Tier 3 Priority A/B sources.
- If experimental capability exists, validate sensitivity/selectivity
  directly rather than relying on recalled literature patterns.

### Summary & Conclusions
All three oxides are plausible ethanol-sensing candidates based on general
literature patterns, but **no specific numeric value in this report should
be treated as verified** — this session had no retrieval tool connected.
ZnO and SnO₂ are more commonly reported as ethanol-sensitive than TiO₂ in
general field knowledge, but this ranking itself requires literature
verification before being used in a real screening decision.

### Unresolved Questions
- Exact adsorption energies and selectivity values: not available without
  retrieval or experimental data.
- Ethanol HOMO/LUMO energy levels: not reliably recalled with citable
  precision.

---

## Follow-Up Refinement Questions
- [FU1] — Should I attempt a live Materials Project lookup if API access is
  enabled? — Would convert all LOW-confidence band-gap values to verified,
  citable TIER 2 data.
- [FU2] — Do you have a target operating temperature range? — Would narrow
  the candidate ranking meaningfully, since TiO₂ typically requires higher
  operating temperatures than ZnO/SnO₂.
- [FU3] — Is reversibility (sensor recovery time) a priority? — Would shift
  the analysis toward physisorption-dominant candidates over chemisorption-
  dominant ones.

## Open Scientific Questions
- [Q1] — The exact surface mechanism (oxygen vacancy vs. direct ethanol
  chemisorption) for these oxides is still debated in parts of the
  literature — why it matters: affects whether sensitivity claims generalize
  across nanostructure morphologies.
- [A1] — Assumed GGA-PBE as the default DFT functional in absence of
  retrieved data — labeled: `ASSUMPTION`.
