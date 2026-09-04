---
name: learning-agents-framework
display_name: Learning Agents Framework V2
description: "Activate when the user wants to learn a subject, build a study package, create learning materials, study for a certification, generate notes/quizzes/notecards, or spin off a subject-specific learning agent. Use when the user asks to 'learn', 'study', 'teach me', 'build a learning package', 'create study material', 'generate quizzes', 'make notecards', or names a certification to prepare for."
icon: "📖"
trigger: learn study certification learning package notes quizzes notecards study guide teach flashcards
depends-on: [universal-reasoning-framework]
inputs:
  - name: subject
    description: "The subject, topic, or certification to build learning materials for"
    type: string
    required: true
  - name: level
    description: "Target audience level -- beginner, intermediate, or advanced"
    type: string
    default: intermediate
  - name: certification
    description: "Certification or standard name if applicable (e.g., CompTIA Security+, AWS SAA-C03)"
    type: string
    required: false
tools: [file_read, run_python, file_write, open_in_session_tab, web_search, url_fetch]
id: bac8c8c7d5b24c2f8b6be847a319fbb4
---

## Overview

The Learning Agents Framework V2 turns any subject, topic, or certification into a structured learning package consisting of five outputs: Detailed Notes, Reasoning Layer, Architectural Maps, Notecards, and Quizzes. V2 integrates the Universal Reasoning Framework as its analytical backbone -- every concept is run through a first-principles reasoning pipeline before being packaged into learning material. The result: learners don't just learn WHAT, they learn WHY, under what conditions it holds, and when it fails.

This skill also serves as the reusable base from which subject- or certification-specific learning agents are built.

## Workflow

### Step 1: Intake
- **Mode**: `agentic`
- **Input**: `{{subject}}`, `{{level}}`, `{{certification}}`
- **Output**: Scoped subject definition, audience calibration, goal, and certification alignment (if applicable)
- **Validate**: Subject is clearly defined; level is established; certification objectives are mapped if relevant
- **On failure**: Infer sensible defaults and state assumptions; ask a single focused question only when genuinely blocking

Establish the subject/topic, certification or standard (if any), level/audience, scope, and goal. Determine what the user already knows from conversation context to calibrate depth and anchor the Knowledge Connection Engine.

### Step 2: Reason Through Concepts
- **Mode**: `agentic`
- **Input**: Core concepts identified in intake
- **Output**: Reasoning pass for each concept -- causal chains, key variables, confidence levels, invalidation conditions, cross-concept connections
- **Validate**: Every core concept has a causal chain (Inputs -> Mechanism -> Interaction -> Outcome), a confidence tag, and invalidation conditions where applicable
- **On failure**: Tag gaps with appropriate confidence level; note where evidence is insufficient

Before producing any output, run each core concept through the Universal Reasoning Framework pipeline. Calibrate pipeline depth by concept complexity:

FOUNDATIONAL CONCEPT (well-established, low controversy):
  Stages: UNDERSTAND -> REASON -> APPLY
  Confidence tag: HIGH (unless contradictory evidence exists)

APPLIED CONCEPT (requires domain judgment, multiple variables):
  Stages: UNDERSTAND -> REASON -> APPLY -> QUANTIFY -> TEST
  Confidence tag: based on evidence weight from Evidence Hierarchy

CONTESTED OR EVOLVING CONCEPT (active debate, version-sensitive, conflicting sources):
  Stages: Full pipeline through INVALIDATE -> OPTIMIZE
  Confidence tag: based on Signal/Noise classification and Confidence Model

The reasoning pass is the single source of truth for all five outputs.

### Step 3: Produce the Five Outputs
- **Mode**: `agentic`
- **Input**: Reasoning pass from Step 2
- **Output**: Five learning deliverables per the specifications below
- **Validate**: All five outputs are consistent with each other and with the reasoning pass; format matches the Learning Output Specification
- **On failure**: Regenerate the non-conforming output to match spec

#### 3a. Detailed Notes
Comprehensive, written in complete well-formed sentences, organized around the causal chains from the reasoning pass. Each section exposes: what the concept is, why it works (first-principles mechanism), what controls it (key variables), where it connects. Begin with the smallest causal unit and build complexity by adding components. Tag version-sensitive or low-confidence claims inline.

#### 3b. Reasoning Layer
First-principles reasoning output from the pipeline. For each concept: the causal chain (Inputs -> Mechanism -> Interaction -> Outcome with each link explained), distinction between correlation/causation and necessary/sufficient conditions, invalidation conditions, confidence level with rationale, and cross-concept connections (NEW CONCEPT -> KNOWN CONCEPT -> SHARED MECHANISM -> DIFFERENCE -> APPLICATION). This layer teaches the learner how to THINK about the subject. Deconstruct until the underlying logic becomes intuitive.

#### 3c. Architectural Maps
Mermaid or SVG concept maps reflecting causal chains and variable relationships from the reasoning pass. Include directional arrows showing causal flow, decision diamonds for conditional relationships, feedback loops, labeled relationships stating the mechanism, and subgraph boundaries showing system layers. Represent invalidation conditions and failure modes as conditional branches or annotated risk nodes.

#### 3d. Notecards
Three types drawn from different reasoning stages:
- RECALL cards (from UNDERSTAND): definition and fact retrieval
- REASONING cards (from REASON): causal mechanism and "why" questions
- INVALIDATION cards (from INVALIDATE): boundary conditions and failure modes
Label each card with its type. Offer import-ready CSV when useful.

#### 3e. Quizzes
Mixed-format, tiered difficulty mapped to reasoning depth:
- RECALL tier (UNDERSTAND stage): "What is X?" / definitional
- APPLICATION tier (REASON + APPLY stages): scenario-based, requires understanding the causal mechanism
- ANALYSIS tier (INVALIDATE + OPTIMIZE stages): invalidation, confounders, optimization judgment
Always include full answer key and explanation for every answer including why distractors are wrong. Analysis-tier distractors should be plausible -- drawn from common misconceptions or correlation-causation confusions identified in the reasoning pass.

### Step 4: Deliver
- **Mode**: `agentic`
- **Input**: Five completed outputs
- **Output**: Formatted deliverable(s) -- single document or separate files
- **Validate**: All outputs are mutually consistent; certification objectives mapped where applicable
- **On failure**: Cross-reference against reasoning pass (single source of truth) and correct inconsistencies

Ask or infer delivery format. Default to single document for small subjects, separate files for large certification curricula.

## Output

A structured learning package containing: Detailed Notes, Reasoning Layer, Architectural Maps, Notecards (RECALL/REASONING/INVALIDATION), and tiered Quizzes (Recall/Application/Analysis) -- all grounded in first-principles reasoning with confidence tagging and invalidation conditions.

## Lessons Learned

### Do
- Run the reasoning pass BEFORE generating any output -- it's the foundation everything else builds on
- Calibrate pipeline depth to concept complexity -- don't run full invalidation on well-established definitions
- Keep all five outputs consistent with the same reasoning pass
- Tag confidence levels inline in notes and reasoning layer
- Structure notes around causal chains, not just topic hierarchies
- Make Analysis-tier quiz distractors plausible by drawing from real misconceptions

### Don't
- Don't package concepts without reasoning through them first (V1 behavior)
- Don't present low-confidence claims as established fact
- Don't strip complexity from notes -- deconstruct by exposing structure
- Don't write notecards that only test recall -- include REASONING and INVALIDATION types
- Don't write quiz explanations that just restate the answer -- explain the causal mechanism

### Common Failures
- Generating notes in topic-subtopic hierarchy without causal chain structure (V1 pattern)
- Reasoning Layer that only says "why this matters" without exposing the actual mechanism (V1 pattern)
- Architectural maps that show topic groupings instead of causal flows
- Quiz Analysis-tier questions with obviously wrong distractors instead of plausible misconceptions
- Inconsistency between outputs because they were generated independently instead of from the same reasoning pass

### When to Ask the User
- When the subject scope is too broad or too narrow to produce useful material
- When the target level is ambiguous (e.g., "intermediate" means different things in different domains)
- When a certification has multiple versions and the user hasn't specified which
- When delivery format preference isn't clear for large curricula

## Certification Alignment

When a certification or standard is named, map content to its official objective domains, label sections/questions with relevant objective codes when known, and mirror the exam's question style. Never fabricate objective codes, exam details, citations, or statistics. Explain WHY the correct answer is correct (causal mechanism), not just THAT it is correct (authority citation). When vendor specifications define a standard, teach the standard AND the reasoning behind it.

## Spinning Off Specialized Agents

When asked to build a subject- or certification-specific agent from this framework: keep the Learning Output Specification, Subject Intake and Adaptation Guide, and Universal Reasoning Framework V2 unchanged (they guarantee consistent quality and analytical rigor), add the certification's objective domains and source material as new reference documents, and fix the subject/level in that agent's instructions.
