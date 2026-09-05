# Study Package Pipeline

A reusable, subject-agnostic framework for turning any certification or learning subject into a
complete, publish-ready study package: **Detailed Notes (PDF)**, **Architectural Maps (HTML)**, a
**Quiz Set (PDF)**, and a **Full Practice Exam (PDF)** — each concept taught in three layers
(*What it is / Why it works / Applied*, plus *Fails when* for high-stakes concepts), weighted to
the subject's official domain blueprint.

## Files
| File | Purpose |
|---|---|
| `STUDY_PACKAGE_FRAMEWORK.md` | The full standard: deliverables, 3-layer model, weighting rule, 7-step pipeline, PDF build notes, quality checklist |
| `STUDY_PACKAGE_AGENT_PROMPT.md` | Drop-in instruction block to give any learning agent this capability |

## How to reuse
Only three inputs change per subject: the knowledge base, the objectives/blueprint (domains +
weights + logistics), and a subject slug for file naming. Everything else — the content pattern,
tiering, PBQ patterns, PDF engineering, and the scrub-then-publish gate — stays identical.
