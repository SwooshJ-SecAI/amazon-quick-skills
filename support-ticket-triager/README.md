# Support Ticket Triager

![Category](https://img.shields.io/badge/Category-ITSM-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Amazon%20Quick-orange?style=flat-square)

Classifies incoming support tickets by category, urgency, and complexity. Applies routing rules to assign teams, generates initial response drafts, and flags escalation candidates based on customer tier, issue severity, and SLA proximity.

Built with [Amazon Quick](https://amazon.com/quick).

---

## Trigger Phrases

- "triage these tickets"
- "classify support requests"
- "route this ticket"
- "prioritize the queue"
- "which tickets need escalation"

## Tools Orchestrated

`file_read`, `file_write`, `run_python`, `query_dataset`, `search_relevant_content`

## Skill Structure

```
support-ticket-triager/
|-- SKILL.md             # Skill definition file
|-- README.md            # Documentation
|-- evals/               # Evaluation test cases
```

## Status

Production. This skill is actively deployed and maintained.

## License

This project is licensed under the MIT License. See [LICENSE](../../LICENSE) for details.

---

**Author:** SwooshJ-SecAI | Security Engineer II / Enterprise AI Engineer