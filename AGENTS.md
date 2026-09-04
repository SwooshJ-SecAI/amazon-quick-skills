# Skills Catalog

A catalog of 17 reusable AI skills built with Amazon Quick. Each skill is a self-contained `SKILL.md` definition with structured workflows, triggers, and inputs. Every skill folder contains a `README.md` (overview) and the `SKILL.md` (full definition).

**Total skills:** 17

---

## Security & Threat Intelligence

| Skill | Description |
|:---|:---|
| [cicc-intelligence-refresh](./cicc-intelligence-refresh/) | On-demand external cyber intelligence pipeline. Fetches live data from CISA KEV, NVD, EPSS, and five RSS attack landscape feeds. |
| [cicc-security-analyst](./cicc-security-analyst/) | Conversational cyber intelligence analyst answering stakeholder questions about security posture, vulnerabilities, and threat landscape. |
| [vulnerability-report-summarizer](./vulnerability-report-summarizer/) | Transforms raw vulnerability scan output (Nessus, Qualys, OWASP ZAP) into executive-ready reports with CVSS scoring. |
| [security-tool-agent-builder](./security-tool-agent-builder/) | Builds vendor-specific security tool SME agents with full knowledge bases (Arctic Wolf, SentinelOne, Splunk, and more). |

## Compliance & ITSM

| Skill | Description |
|:---|:---|
| [compliance-automation](./compliance-automation/) | Generates draft compliance policies for SOC 2 Type I/II, ISO 27001, and HIPAA. Produces evidence checklists and gap analysis. |
| [freshservice-ticket-creator](./freshservice-ticket-creator/) | Universal base skill for creating Freshservice tickets from agent findings and sending email notifications with recommended actions. |
| [freshservice-ticket-pipeline](./freshservice-ticket-pipeline/) | Full Freshservice ITSM integration with five composable Python modules covering the complete API surface. |
| [support-ticket-triager](./support-ticket-triager/) | Classifies support tickets by category, urgency, and complexity. Applies routing rules and flags escalation candidates. |

## Document & Content

| Skill | Description |
|:---|:---|
| [document-comparison](./document-comparison/) | Compares two documents side-by-side, highlighting differences and generating a structured change summary. Supports DOCX, PDF, MD. |
| [html-deck-builder](./html-deck-builder/) | Builds professional HTML slide presentations as single self-contained files with keyboard navigation and speaker notes. |
| [slide-deck-from-doc](./slide-deck-from-doc/) | Converts any document (DOCX, PDF, Markdown) into a professional PowerPoint with smart layout selection. |
| [business-case-builder](./business-case-builder/) | Builds decision-ready business case documents combining stakeholder inputs with market research. |
| [eliminate-ai-writing-tropes](./eliminate-ai-writing-tropes/) | Writing ruleset that eliminates detectable AI-generated patterns from prose. Enforces concrete specificity and vocabulary discipline. |

## Learning & Reasoning

| Skill | Description |
|:---|:---|
| [learning-agents-framework](./learning-agents-framework/) | Generates structured learning experiences: study packages, notes, quizzes, notecards, and subject-specific learning agents. |
| [skill-authoring-mastery](./skill-authoring-mastery/) | Structured learning pathway for mastering Amazon Quick skill authoring: structure, triggers, evals, and the test-improve loop. |
| [universal-reasoning-framework](./universal-reasoning-framework/) | First-principles reasoning, concept deconstruction, hypothesis testing, and conversion of validated knowledge into frameworks. |

## Operations

| Skill | Description |
|:---|:---|
| [onboarding-orchestrator](./onboarding-orchestrator/) | Designs end-to-end employee onboarding with 30/60/90 day plans, buddy assignments, and milestone tracking. |

---

*Built with [Amazon Quick](https://github.com/SwooshJ-SecAI). Each skill can be invoked on demand or attached to a scheduled agent.*
