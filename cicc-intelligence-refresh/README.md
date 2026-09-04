# CICC Intelligence Refresh

![Category](https://img.shields.io/badge/Category-Cybersecurity-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Amazon%20Quick-orange?style=flat-square)

On-demand external cyber intelligence pipeline. Fetches live data from CISA KEV, NVD, EPSS, and five RSS attack landscape feeds, enriches and scores events against an organizational technology watchlist, and writes updated JSON data files.

Built with [Amazon Quick](https://amazon.com/quick).

---

## Trigger Phrases

- "refresh intelligence"
- "update cyber data"
- "pull latest intel"
- "CICC refresh"
- "run the pipeline"
- "update KEV data"

## Tools Orchestrated

`url_fetch`, `download_file`, `run_python`, `file_write`, `file_read`, `web_search`

## Skill Structure

```
cicc-intelligence-refresh/
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