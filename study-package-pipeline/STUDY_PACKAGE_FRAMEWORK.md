# Study Package Pipeline — Reusable Framework

## Stakeholder-Ready Learning-Deliverable Standard — v1.0 | September 2026

A subject-agnostic pipeline for turning any certification or learning subject into a complete,
publish-ready study package. Extracted from the CompTIA Security+ SY0-701 build so that every
other learning agent (SecAI+, AWS AI Practitioner, Security tool SMEs, etc.) produces the same
four deliverables to the same quality bar.

Pair this with the `Universal_Reasoning_Framework_V2.md` (the reasoning engine) and the agent's
own subject knowledge base + objectives/blueprint document.

---

## What the pipeline produces (the four deliverables)

| # | Deliverable | Format | Purpose |
|---|---|---|---|
| 1 | **Detailed Domain Notes** | PDF | Full coverage of every domain, each concept in the 3-layer model |
| 2 | **Architectural Maps** | HTML (inline SVG/CSS) | Causal-chain diagrams for the trickiest concepts |
| 3 | **Quiz Set** | PDF | ~30 questions, blueprint-weighted, tiered + PBQ, full answer key |
| 4 | **Full Practice Exam** | PDF | Full-length (match the real exam count), blueprint-weighted, full answer key |

Produce all four unless the learner asks for a subset. Each builds on the last: **learn → visualize → test → simulate.**

---

## The core content pattern: the 3-layer model

Every concept in the notes and the reasoning behind every question uses three layers. This is
what makes the package "more than theory."

- **What it is** — the precise definition and component parts (Recall tier).
- **Why it works** — the causal mechanism: inputs → mechanism → outcome (Application tier).
- **Applied** — a realistic deployment or attack/defense/usage scenario (Analysis tier / PBQ).
- **Fails when** *(add for high-stakes concepts)* — the invalidation condition: under what
  circumstances the control/technique/model breaks.

Tag every concept with: the official **objective/blueprint code**, a **difficulty tier**
(Recall / Application / Analysis), and a **confidence level** (HIGH / MODERATE, plus
`[VERSION-SENSITIVE]` where currency matters).

---

## Blueprint weighting (the non-negotiable rule)

Distribute notes depth, quiz questions, and exam questions **by the subject's official domain
weights** — never evenly. Weight-first ordering: study and build the heaviest domain first.

For any subject, read the objectives/blueprint doc, get the domain weights, and allocate
question counts proportionally. Example (Security+ SY0-701):

| Domain | Weight | Quiz (of 30) | Exam (of 90) |
|---|---|---|---|
| Heaviest domain | 28% | 8 | 25 |
| … | 22% | 7 | 20 |
| … | 20% | 6 | 18 |
| … | 18% | 5 | 16 |
| Lightest domain | 12% | 4 | 11 |

Include at least one **performance-based question (PBQ)** per operations/architecture-style topic
(log analysis, rule/config ordering, placement, tool/attack/control matching, multi-response).

---

## Pipeline steps (execution order)

### Step 1 — Scope & source (deterministic)
- Read the agent's **knowledge base** and **objectives/blueprint** document first. Ground content
  there; use web search only to fill gaps or verify current exam facts.
- Extract: domain list, weights, sub-objective codes, exam logistics (question count, time,
  passing score), and version/objectives number.
- State assumptions instead of interrogating; ask one focused question only if genuinely blocking.

### Step 2 — Reason before packaging (agentic)
- Run each core concept through the Universal Reasoning Framework pipeline (UNDERSTAND → REASON →
  APPLY, extending to QUANTIFY/TEST/INVALIDATE for applied or contested concepts).
- The output of this pass populates the 3-layer model and the "Fails when" lines.

### Step 3 — Build the Detailed Notes PDF (deterministic)
- Use the `canvas_pdf` skill. Cover page + "how to use" (explain the 3-layer model and tags) +
  one section per domain in weight order.
- **PDF engineering (learned):** ReportLab only (no weasyprint). See "ReportLab gotchas" below.

### Step 4 — Build the Architectural Maps HTML (deterministic)
- Use the `html_design` skill + the `architecture_design_system/ARCHITECTURE_MAP_DESIGN.md`
  standard (dark theme tokens, card-based, plain-English labels, decision points, failure/risk
  nodes). 4–6 causal maps covering the highest-value / trickiest concepts.
- Include a legend, per-map objective code + tier, and a "Why it works" + "Fails when" callout.

### Step 5 — Build the Quiz Set PDF (deterministic)
- ~30 questions, blueprint-weighted, tiered, with PBQ simulations. Store questions as a data
  list so the **answer key auto-generates** from the same source. Every answer explains the
  causal *why* and why each distractor is wrong. Original questions only.

### Step 6 — Build the Full Practice Exam PDF (deterministic)
- Full-length (match the real exam's question count), blueprint-weighted, mixed tiers + PBQs.
- Include a quick-reference answer grid, then detailed explanations. Add exam-day framing
  (question count / time / passing score / pacing).

### Step 7 — Persist, scrub, publish (deterministic, gated)
- Copy all deliverables into `agent_files/artifacts/<subject_slug>/`.
- Run the `github-scrub-pipeline`: audit against `scrub_rules.json`, normalize PDF metadata to a
  generic personal attribution, verify **zero** sensitive patterns (text + metadata), write
  `SCRUB_VERIFICATION.md` + `README.md`.
- **Gate:** never push to a public repo without explicit user confirmation. Upload to the
  portfolio repo (browser-based; the Python sandbox cannot reach the GitHub API).

---

## ReportLab gotchas (learned during the Security+ build)

- **Use ReportLab, never weasyprint** (not installed).
- Inline font colors in `Paragraph` markup **must be `#`-prefixed**: `<font color="#1D7874">`,
  not `color="1D7874"`. Bare hex raises `Invalid color value`.
- Don't re-add built-in style names (`Bullet`, `Normal`, etc.) via `styles.add()` — it raises
  `KeyError: already defined`. Use unique custom names.
- **LayoutError "flowable too large" on a small paragraph/heading** is a ReportLab pagination
  quirk at page boundaries. Robust fix: in the save routine, catch the error, parse the failing
  flowable's id from the message, insert a `PageBreak()` before its owning heading, and retry
  (loop). This converges in a handful of iterations.
- Tables: keep total `colWidths` ≤ usable width (letter, 0.7" margins ≈ 7.1"). Frame padding
  eats ~12pt, so leave headroom.
- Build incrementally with `run_python_with_write` and a live `open_in_session_tab(..., live=True)`
  preview; switch to `live=False` at the end.

---

## Quality checklist (before delivering)

- [ ] Every domain covered; depth proportional to blueprint weight.
- [ ] Every concept has What / Why / Applied; high-stakes ones have "Fails when."
- [ ] Every notes section and question tagged with objective code + tier (+ confidence on notes).
- [ ] Quiz and exam question counts match blueprint proportions.
- [ ] At least one PBQ per operations/architecture topic; answer keys explain right + wrong.
- [ ] Original content only — no reproduced real exam items.
- [ ] Deliverables persisted to agent files; scrubbed + verified clean before any publish.
- [ ] Public push only after explicit user confirmation.

---

## Reusability

The pipeline is subject-agnostic. To apply it to another learning agent, only three inputs
change: the **knowledge base**, the **objectives/blueprint** (domains + weights + logistics),
and the **subject slug** for file/folder naming. Everything else — the 3-layer model, tiering,
PBQ patterns, PDF engineering, scrub/publish gate — stays identical. Use the companion
`STUDY_PACKAGE_AGENT_PROMPT.md` as the drop-in instruction block for each agent.
