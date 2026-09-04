# Amazon Quick Skills Catalog

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![Skills](https://img.shields.io/badge/Skills-66+-purple?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Amazon%20Quick-orange?style=flat-square)

A catalog of 66+ custom skills authored for the Amazon Quick platform, spanning cybersecurity operations, compliance automation, AI agent engineering, content creation, and enterprise workflow automation.

Built with [Amazon Quick](https://amazon.com/quick).

---

## Description

Amazon Quick skills are reusable, deterministic workflow definitions that encode domain expertise into repeatable procedures. Each skill defines a structured sequence of steps -- deterministic actions, agentic reasoning, tool invocations, and conditional logic -- packaged as a SKILL.md file that any Amazon Quick agent can execute.

This repository catalogs the user-created skills I have authored, organized by domain. Each skill directory contains documentation covering the skill's purpose, trigger conditions, input requirements, execution flow, and the tools it orchestrates.

## What is an Amazon Quick Skill?

A skill is a formalized workflow that:

- **Triggers** on specific user intents or phrases
- **Accepts** structured inputs (files, parameters, context)
- **Executes** a defined sequence of deterministic and agentic steps
- **Orchestrates** platform tools (file I/O, web search, data queries, API calls)
- **Produces** consistent, repeatable outputs

Skills transform ad-hoc agent conversations into reliable, auditable automation.

## Skill Catalog

### Cybersecurity and Threat Intelligence

| Skill | Description |
|-------|-------------|
| [cicc-intelligence-refresh](cicc-intelligence-refresh/) | Fetches live data from CISA KEV, NVD, EPSS, and RSS feeds; enriches and scores against technology watchlist |
| [cicc-security-analyst](cicc-security-analyst/) | Conversational analyst for security posture, vulnerabilities, threat landscape, and organizational risk |
| [vulnerability-report-summarizer](vulnerability-report-summarizer/) | Transforms raw scan output into executive-ready reports with CVSS scoring and MITRE ATT&CK mapping |
| [security-tool-agent-builder](security-tool-agent-builder/) | Builds vendor-specific security tool SME agents with full knowledge bases |

### Compliance and Audit

| Skill | Description |
|-------|-------------|
| [compliance-automation](compliance-automation/) | Generates SOC 2, ISO 27001, and HIPAA policies, evidence checklists, and gap analysis reports |

### ITSM and Service Management

| Skill | Description |
|-------|-------------|
| [freshservice-ticket-creator](freshservice-ticket-creator/) | Creates Freshservice tickets from agent findings with Outlook email notifications |
| [freshservice-ticket-pipeline](freshservice-ticket-pipeline/) | Full Freshservice API integration: tickets, assets, agents, groups, change requests, problems, releases |
| [support-ticket-triager](support-ticket-triager/) | Classifies tickets by category, urgency, and complexity; applies routing rules and flags escalations |

### AI Engineering and Skill Development

| Skill | Description |
|-------|-------------|
| [skill-authoring-mastery](skill-authoring-mastery/) | Learning pathway for mastering Amazon Quick skill authoring methodology |
| [universal-reasoning-framework](universal-reasoning-framework/) | First-principles reasoning, hypothesis testing, evidence validation, and system optimization |
| [learning-agents-framework](learning-agents-framework/) | Generates structured learning packages with notes, quizzes, and study materials |

### Content and Document Generation

| Skill | Description |
|-------|-------------|
| [html-deck-builder](html-deck-builder/) | Builds self-contained HTML slide presentations with navigation and speaker notes |
| [slide-deck-from-doc](slide-deck-from-doc/) | Converts DOCX, PDF, or Markdown into PowerPoint with smart layout selection |
| [document-comparison](document-comparison/) | Side-by-side document diff with structured change summary |
| [eliminate-ai-writing-tropes](eliminate-ai-writing-tropes/) | Enforces concrete specificity and eliminates detectable AI writing patterns |
| [business-case-builder](business-case-builder/) | Builds decision-ready business cases with cost/benefit analysis and risk assessment |

### Onboarding and Operations

| Skill | Description |
|-------|-------------|
| [onboarding-orchestrator](onboarding-orchestrator/) | Designs 30/60/90 day onboarding programs with buddy assignments and milestone tracking |

## Repository Structure

```
amazon-quick-skills/
|-- cicc-intelligence-refresh/
|-- cicc-security-analyst/
|-- freshservice-ticket-creator/
|-- freshservice-ticket-pipeline/
|-- compliance-automation/
|-- vulnerability-report-summarizer/
|-- security-tool-agent-builder/
|-- universal-reasoning-framework/
|-- business-case-builder/
|-- html-deck-builder/
|-- learning-agents-framework/
|-- eliminate-ai-writing-tropes/
|-- skill-authoring-mastery/
|-- onboarding-orchestrator/
|-- document-comparison/
|-- support-ticket-triager/
|-- slide-deck-from-doc/
|-- .gitignore
|-- README.md
```

## Getting Started

1. Clone this repository:
   ```bash
   git clone https://github.com/ajohnson/amazon-quick-skills.git
   cd amazon-quick-skills
   ```
2. Navigate to any skill directory for documentation
3. Each skill README covers: purpose, triggers, inputs, execution flow, and tools used
4. Skills are authored as SKILL.md files following the Amazon Quick skill specification

## Skill Authoring Methodology

Each skill in this catalog follows a consistent development lifecycle:

1. **Workflow observation** -- Identify a repeatable pattern from agent sessions
2. **Procedure extraction** -- Isolate the deterministic and agentic steps
3. **SKILL.md formalization** -- Encode the workflow in the skill specification format
4. **Evaluation generation** -- Create test cases that validate skill behavior
5. **Test-improve loop** -- Iterate based on evaluation results and production feedback

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

**Author:** Antonio Johnson | Security Engineer II / Enterprise AI Engineer