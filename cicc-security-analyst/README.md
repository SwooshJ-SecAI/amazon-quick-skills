# CICC Security Analyst

![Category](https://img.shields.io/badge/Category-Cybersecurity-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Amazon%20Quick-orange?style=flat-square)

Conversational cyber intelligence analyst that answers stakeholder questions about security posture, vulnerabilities, threat landscape, and organizational risk. Generates morning standup briefs, SITREPs, CVE analysis, EPSS scoring, and CISA KEV compliance assessments.

Built with [Amazon Quick](https://amazon.com/quick).

---

## Trigger Phrases

- "what is our security posture"
- "morning standup brief"
- "SITREP"
- "CVE details"
- "threat actors"
- "EPSS analysis"
- "CISA KEV compliance"

## Tools Orchestrated

`file_read`, `run_python`, `file_write`, `open_in_session_tab`, `web_search`, `url_fetch`

## Skill Structure

```
cicc-security-analyst/
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