# Freshservice Ticket Creator

![Category](https://img.shields.io/badge/Category-ITSM-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Amazon%20Quick-orange?style=flat-square)

Universal base skill for creating Freshservice tickets from any agent's findings and sending Outlook email notifications with recommended actions. Designed as a composable building block that other agents invoke when findings need to be tracked in the ITSM system.

Built with [Amazon Quick](https://amazon.com/quick).

---

## Trigger Phrases

- "create ticket"
- "send to freshservice"
- "ticket this finding"
- "open IT ticket"
- "notify team about finding"

## Tools Orchestrated

`run_python`, `run_javascript`, `file_read`, `file_write`, `url_fetch`

## Skill Structure

```
freshservice-ticket-creator/
|-- SKILL.md             # Skill definition file
|-- README.md            # Documentation
|-- evals/               # Evaluation test cases
```

## Status

Production. This skill is actively deployed and maintained.

## License

This project is licensed under the MIT License. See [LICENSE](../../LICENSE) for details.

---

**Author:** Antonio Johnson | Security Engineer II / Enterprise AI Engineer