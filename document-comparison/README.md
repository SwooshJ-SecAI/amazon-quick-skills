# Document Comparison

![Category](https://img.shields.io/badge/Category-Content%20Generation-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Amazon%20Quick-orange?style=flat-square)

Compares two documents side-by-side, highlighting differences and generating a structured change summary. Supports DOCX, PDF, and plain text inputs.

Built with [Amazon Quick](https://amazon.com/quick).

---

## Trigger Phrases

- "compare these docs"
- "what changed between versions"
- "diff these files"
- "document comparison"
- "show differences"

## Tools Orchestrated

`file_read_docx`, `file_read_pdf`, `file_read`, `run_python`, `file_write`

## Skill Structure

```
document-comparison/
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