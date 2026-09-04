---
name: skill-authoring-mastery
display_name: Skill Authoring Mastery
description: "Activate when the user wants to learn how to author Amazon Quick skills, formalize agent workflows into reusable SKILL.md files, understand skill structure (frontmatter, deterministic/agentic steps, triggers, inputs), extract procedures from agents, build and run evals, or iterate through the test-improve loop. Also activate when asked to assess which agents are good skill candidates, or to produce a learning package about skill authoring."
icon: "--"
trigger: skill authoring mastery agent workflow formalize reusable tested shareable SKILL.md extract procedure eval plugin qplugin trigger preloading
depends-on: [learning-agents-framework]
inputs:
  - name: subject
    description: "The specific skill authoring topic or scope -- e.g., 'full skill authoring lifecycle', 'trigger design', 'eval generation', 'agent-to-skill extraction'"
    type: string
    default: "Amazon Quick skill authoring -- full lifecycle"
  - name: level
    description: "Target audience level -- beginner, intermediate, or advanced"
    type: string
    default: advanced
  - name: target_agents
    description: "Optional comma-separated list of the user's existing agent names to analyze for skill extraction candidates"
    type: string
    required: false
tools: [file_write, file_read, open_in_session_tab, run_python, web_search, url_fetch, fdfind, kg_search]
id: 78a267c0e1ca496987ac8b4b9f3ca036
---

## Overview

This skill produces a structured learning package about Amazon Quick skill authoring using the Learning Agents Framework V2 pattern. It covers the full skill lifecycle -- from identifying extractable procedures in existing agents, through SKILL.md authoring (frontmatter, deterministic/agentic steps, triggers, inputs), to eval generation, the test-improve loop, and plugin packaging. When target agents are provided, the skill also performs an extraction readiness assessment.

This skill depends on the Learning Agents Framework V2 and produces all five V2 output types: Detailed Notes, Reasoning Layer, Architectural Maps, Notecards (RECALL/REASONING/INVALIDATION), and Quizzes (Recall/Application/Analysis).

## Workflow

### Step 1: Intake and Scope Calibration
- **Mode**: `agentic`
- **Input**: `{{subject}}`, `{{level}}`, `{{target_agents}}`
- **Output**: Scoped subject definition, audience calibration, list of target agents to assess (if provided)
- **Validate**: Subject scope is specific enough to produce useful material; level calibration matches user's experience
- **On failure**: Default to full lifecycle at advanced level; skip agent assessment if no agents provided

Establish the scope of the learning package. If `{{target_agents}}` is provided, read the user's agent roster from `~/.quickwork/profiles/*/chat_agents.json` to gather system prompts and capabilities for extraction analysis.

Determine what the user already knows from conversation context. At advanced level, assume familiarity with agent design, tool usage, and workflow patterns -- focus on the formalization gap (why structure matters, how to classify steps, how to design triggers).

### Step 2: Agent Extraction Assessment (Conditional)
- **Mode**: `agentic`
- **Tool**: `run_python` (to read agent configs), `kg_search` (to check for related entities)
- **Input**: Agent names from `{{target_agents}}`, agent roster data
- **Output**: Extraction readiness matrix -- for each agent: skill-worthy (yes/no), extractable procedure name, complexity rating, recommended input parameters
- **Validate**: Each agent is evaluated against three extraction gates: complexity (3+ steps, 2+ tools), repetition (performed 2+ times), variability (inputs change between runs)
- **On failure**: Skip assessment and note that agent analysis was not possible; proceed with general learning package

Apply the extraction decision tree to each named agent. Classify agents into: Extract as Skill, Keep as Agent Only, Queue for Later, or Convert to Scheduled Task.

### Step 3: Reason Through Skill Authoring Concepts
- **Mode**: `agentic`
- **Input**: Scoped subject from Step 1, extraction results from Step 2 (if available)
- **Output**: Reasoning pass for each core concept -- causal chains, key variables, confidence levels, invalidation conditions, cross-concept connections
- **Validate**: Every core concept has a causal chain (Inputs -> Mechanism -> Interaction -> Outcome), a confidence tag, and invalidation conditions
- **On failure**: Tag gaps with appropriate confidence level; proceed with available reasoning

Run each skill authoring concept through the Universal Reasoning Framework V2 pipeline. Core concepts to reason through:
- SKILL.md structural anatomy (FOUNDATIONAL -- UNDERSTAND -> REASON -> APPLY)
- Deterministic vs. agentic classification (APPLIED -- through QUANTIFY -> TEST)
- Trigger/preloading system (APPLIED -- through QUANTIFY -> TEST)
- Input parameterization (FOUNDATIONAL)
- Agent-skill relationship (APPLIED)
- Test-improve loop (APPLIED)
- Plugin packaging (FOUNDATIONAL)

Calibrate reasoning depth to concept complexity. Anchor new concepts to the user's existing knowledge (agent design, scheduled tasks, framework architecture).

### Step 4: Produce the Five Learning Outputs
- **Mode**: `agentic`
- **Tool**: `file_write`, `open_in_session_tab`
- **Input**: Reasoning pass from Step 3, extraction assessment from Step 2
- **Output**: Complete learning package with all five V2 outputs in a single Markdown document
- **Validate**: All five outputs are mutually consistent; reasoning pass is the single source of truth; format matches the Learning Output Specification; architectural maps use SVG (not Mermaid) with dark theme
- **On failure**: Regenerate the non-conforming output to match spec

Produce all five outputs per the Learning Output Specification:

**4a. Detailed Notes** -- Organized around causal chains from the reasoning pass. Each section exposes: what the concept is, why it works, what controls it, where it connects. Begin with the smallest causal unit and build complexity. Tag confidence levels inline.

**4b. Reasoning Layer** -- First-principles reasoning output. For each concept: causal chain, correlation/causation distinctions, invalidation conditions, confidence level, cross-concept connections via Knowledge Connection Engine pattern.

**4c. Architectural Maps** -- SVG-based diagrams with dark theme (#0d1117 background, #e6edf3 text, #30363d borders). Include: skill lifecycle, agent-to-skill extraction decision tree, SKILL.md anatomy, trigger/preloading data flow.

**4d. Notecards** -- Three types: RECALL (definitions, structure), REASONING (causal mechanism, "why" questions), INVALIDATION (boundary conditions, failure modes). Label each card with its type.

**4e. Quizzes** -- Three tiers: RECALL (terminology, structure), APPLICATION (scenario-based design decisions), ANALYSIS (invalidation, edge cases, optimization). Full answer key with explanations for every answer including why distractors are wrong.

Save the complete learning package to `artifacts/skill_authoring_mastery_learning_package.md` and open in session tab.

### Step 5: Deliver
- **Mode**: `agentic`
- **Input**: Completed learning package from Step 4
- **Output**: Formatted deliverable opened in session tab with summary
- **Validate**: Document renders correctly; SVG diagrams display properly; all five outputs present and consistent
- **On failure**: Identify rendering issues and regenerate affected sections

Present the learning package with a summary of what was produced, highlight any agent-specific extraction recommendations from Step 2, and offer to proceed with actually extracting the first skill.

## Output

A comprehensive Markdown learning package containing:
1. Detailed Notes organized around causal chains covering SKILL.md anatomy, step classification, triggers, inputs, extraction, evals, and plugins
2. Reasoning Layer with first-principles analysis, invalidation conditions, and cross-concept connections
3. SVG architectural maps (dark theme) showing skill lifecycle, extraction decision tree, SKILL.md structure, and preloading flow
4. Notecards in three categories: RECALL, REASONING, INVALIDATION
5. Tiered quizzes: Recall, Application, Analysis with full answer key and explanations

If target agents were provided: an extraction readiness matrix classifying each agent as skill-worthy or not, with recommended procedures and input parameters.

## Lessons Learned

### Do
- Ground all notes in causal chains from the reasoning pass before writing -- this prevents the V1 pattern of topic-subtopic hierarchy without mechanism explanation
- Use the user's own agents as concrete examples when available -- abstract skill authoring concepts become immediately actionable when mapped to real workflows
- Make SVG diagrams information-dense but readable -- labeled arrows, decision diamonds, feedback loops, not decorative graphics
- Tag confidence levels on contested or evolving concepts (e.g., optimal trigger design is experience-dependent, not formally proven)

### Don't
- Do not use Mermaid for architectural maps -- the user preference is SVG-based system diagrams with dark themes
- Do not include emojis in any output -- professional plain text only
- Do not write notecards that only test recall -- include REASONING and INVALIDATION types per V2 spec
- Do not present the test-improve loop as optional -- it is the quality gate between draft and production

### Common Failures
- Generating notes in topic hierarchy without causal chain structure (V1 pattern) -- fix by running the reasoning pass first and using its output as the organizational backbone
- SVG diagrams that do not render in Markdown viewers -- ensure viewBox is set, use inline styles not external CSS, keep font-family to sans-serif
- Quiz Analysis-tier distractors that are obviously wrong -- draw distractors from real misconceptions identified in the reasoning pass (e.g., "mark all steps as agentic for maximum flexibility")
- Inconsistency between the five outputs -- fix by treating the reasoning pass as the single source of truth for all outputs

### When to Ask the User
- When the subject scope is broader than skill authoring (e.g., includes agent design or platform architecture) -- confirm whether to stay focused or expand
- When target agents are provided but their system prompts are too short to analyze meaningful workflow patterns -- ask for elaboration on the agent's typical workflow
- When the user's experience level is ambiguous -- a user with 17 agents may still be new to skill formalization
