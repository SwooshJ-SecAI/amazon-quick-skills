# Study Package Pipeline — Drop-in Agent Prompt

> Paste this block into any learning agent's instructions to give it the same study-package
> production capability used for CompTIA Security+ SY0-701. It is subject-agnostic: the agent
> supplies its own knowledge base and objectives/blueprint; this prompt supplies the method.
> Companion reference: `STUDY_PACKAGE_FRAMEWORK.md` (full standard) and
> `Universal_Reasoning_Framework_V2.md` (reasoning engine).

---

## Capability: Produce a Complete Study Package

When the learner asks for study material, notes, a quiz, a practice exam, architectural maps,
or a full study package for your subject, produce a cohesive package of up to four deliverables.
Default to all four unless the learner asks for a subset. Each builds on the last:
**learn -> visualize -> test -> simulate.**

1. **Detailed Domain Notes (PDF)** — every domain, each concept in the 3-layer model.
2. **Architectural Maps (HTML)** — 4-6 causal-chain diagrams of the trickiest concepts.
3. **Quiz Set (PDF)** — ~30 questions, blueprint-weighted, tiered + PBQ, full answer key.
4. **Full Practice Exam (PDF)** — full length (match the real exam count), full answer key.

## Mandatory content pattern: the 3-layer model

Never deliver flat definitions. Every concept gets:
- **What it is** (definition / parts) — Recall.
- **Why it works** (causal mechanism: inputs -> mechanism -> outcome) — Application.
- **Applied** (realistic scenario / PBQ) — Analysis.
- **Fails when** (invalidation condition) — add for high-stakes concepts.

Tag every concept and question with the official **objective/blueprint code**, a **difficulty
tier** (Recall/Application/Analysis), and (on notes) a **confidence level** (HIGH/MODERATE;
`[VERSION-SENSITIVE]` where currency matters).

## Mandatory weighting rule

Read your objectives/blueprint document, get the domain weights, and distribute notes depth and
question counts **proportionally to those weights** — never evenly. Build the heaviest domain
first. Include at least one performance-based question (PBQ) per operations/architecture-style
topic. Build only original questions — never reproduce real exam items.

## Method (follow in order)

1. **Scope & source.** Read your knowledge base and objectives/blueprint first; ground everything
   there. Use web search only to fill gaps or verify current exam facts. Extract domains, weights,
   sub-objective codes, and exam logistics (question count, time, passing score, version). State
   assumptions rather than interrogating; ask one focused question only if truly blocking.
2. **Reason before packaging.** Run each core concept through the Universal Reasoning Framework
   pipeline (UNDERSTAND -> REASON -> APPLY, extending to QUANTIFY/TEST/INVALIDATE for applied or
   contested concepts). This pass fills the 3-layer model and the "Fails when" lines.
3. **Notes PDF** — load `canvas_pdf`; cover + "how to use" + one section per domain in weight order.
4. **Architectural Maps HTML** — load `html_design`; follow the
   `architecture_design_system/ARCHITECTURE_MAP_DESIGN.md` standard (dark theme, card-based,
   plain-English labels, decision points, failure/risk nodes). Legend + per-map code/tier + a
   "Why it works" and "Fails when" callout.
5. **Quiz PDF** — ~30 questions as a data list so the answer key auto-generates; explanations
   cover the causal *why* and why each distractor is wrong.
6. **Exam PDF** — full length, blueprint-weighted, quick-reference answer grid + detailed
   explanations + exam-day framing (count/time/passing/pacing).
7. **Persist, scrub, publish (gated).** Copy deliverables to `agent_files/artifacts/<subject_slug>/`.
   Run the `github-scrub-pipeline`, normalize PDF metadata to a generic personal attribution, and
   verify zero sensitive patterns (text + metadata) before writing `SCRUB_VERIFICATION.md` and
   `README.md`. **Never push to a public repo without explicit user confirmation.**

## PDF build notes (ReportLab)

- Use ReportLab; weasyprint is NOT available.
- Inline font colors must be `#`-prefixed: `<font color="#1D7874">`.
- Don't re-add built-in style names via `styles.add()`; use unique custom names.
- On a ReportLab `LayoutError` ("flowable too large") at a page boundary: catch it in the save
  routine, insert a `PageBreak()` before the failing flowable's owning heading, and retry in a
  loop until it renders.
- Keep table `colWidths` within the usable page width (letter, 0.7" margins ≈ 7.1"); leave headroom
  for frame padding.
- Build incrementally with a live session-tab preview; switch to `live=False` at the end.

## Quality gate (self-check before delivering)

Every domain covered with weight-proportional depth; every concept in What/Why/Applied (+ "Fails
when" for high-stakes); everything tagged with code + tier; question counts match blueprint
proportions; >=1 PBQ per ops/architecture topic; answer keys explain right and wrong; original
content only; deliverables persisted, scrubbed, and verified clean before any gated publish.
