---
name: cicc-intelligence-refresh
display_name: CICC Intelligence Refresh
description: "On-demand external cyber intelligence pipeline for Example Corp. Fetches live data from CISA KEV, NVD, EPSS, and 5 RSS attack landscape feeds, enriches and scores events against the Example Corp technology watchlist, and writes updated JSON data files. Activate when user says 'refresh intelligence', 'update cyber data', 'pull latest intel', 'CICC refresh', 'run the pipeline', or 'update KEV data'."
icon: "🛡️"
trigger: refresh intelligence cicc pipeline update pull latest cyber intel KEV EPSS NVD
tools: [url_fetch, download_file, run_python, file_write, file_read, web_search]
id: 7c89921e16114b51a3468cc40d3f1f81
---

## Overview

Executes the full CICC intelligence pipeline: fetches 6 external sources (CISA KEV, 5 RSS feeds), enriches with NVD CVSS and EPSS scores, matches against the Example Corp technology watchlist (22 vendor families across Tier 1 and Tier 2), scores with a 12-factor organizational relevance model, corroborates across sources, and writes enriched data files to agent_files/cyber_intel_command_center/.

## Technology Watchlist

### Tier 1 -- Critical Infrastructure (Product Criticality = 6/6)
Microsoft (365, Entra ID, Defender, Purview, Exchange, SharePoint, SQL Server, Windows, Outlook), AWS (Web Services, Bedrock, Lambda, CLI), Palo Alto Networks (PAN-OS, PA Firewalls, Panorama, Cortex), SentinelOne (Singularity EDR/XDR), Abnormal Security, Salesforce (Sales Cloud, Service Cloud, Platform), Ring Central (MVP, NiceXONE/NiceCXone), UKG (Pro, Ready, UltiPro, Kronos)

### Tier 2 -- Business Operations (Product Criticality = 4/6)
SAP (Concur), DocuSign, GitHub (Enterprise, Actions), Zoom (Workplace), Box, Freshworks (Freshservice, Freshdesk), Citrix (ShareFile, Cloud), Cloudflare (CDN, WAF), WordPress (WooCommerce), 1Password, Jamf (Pro), Docker (Desktop, Engine), HashiCorp (Terraform, Packer, Vault), Apache (NiFi, HTTP Server)

### Matching Rules
- Word-boundary regex to avoid false positives ("outlook" in "raised its outlook" is NOT Microsoft Outlook)
- vendorProject == "Microsoft" in KEV data is always DIRECT regardless of product
- Vendor as research publisher only (e.g., "Unit 42") = SOURCE ONLY, not DIRECT

## Workflow

### Step 1: Fetch Sources (parallel)
- **Mode**: `deterministic`
- **Tool**: `url_fetch` / `download_file`
- **Input**: 6 source URLs
- **Output**: Raw CISA KEV JSON + 5 RSS feed contents
- **Validate**: KEV JSON parses successfully, RSS feeds return content
- **On failure**: Retry once; if a feed returns 403, try url_fetch as fallback. SecurityWeek and BleepingComputer sometimes block direct downloads.

Sources:
1. CISA KEV: https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json
2. The Hacker News: https://feeds.feedburner.com/TheHackersNews
3. BleepingComputer: https://www.bleepingcomputer.com/feed/
4. The Record: https://therecord.media/feed
5. Dark Reading: https://www.darkreading.com/rss.xml
6. SecurityWeek: https://www.securityweek.com/feed/

### Step 2: Filter KEV to 30-Day Window
- **Mode**: `deterministic`
- **Tool**: `run_python`
- **Input**: Raw KEV catalog JSON
- **Output**: Filtered list of vulnerabilities where dateAdded is within 30 days of today
- **Validate**: Filtered count > 0; dates are within expected range

### Step 3: Match Against Technology Watchlist
- **Mode**: `agentic`
- **Tool**: `run_python`
- **Input**: Filtered KEV entries + watchlist
- **Output**: Each event classified as DIRECT, STRONG, INDIRECT, SOURCE ONLY, or NONE
- **Validate**: All events have a classification; DIRECT matches are real (not false positives)
- **On failure**: Review false positives. Common issues: "entra" matching "N-central", generic "outlook" matching English word. Use word-boundary patterns.

### Step 4: Fetch NVD CVSS (rate-limited)
- **Mode**: `deterministic`
- **Tool**: `run_python` with `url_fetch`
- **Input**: List of CVE IDs from filtered KEV
- **Output**: CVSS scores for each CVE
- **Validate**: Most CVEs return CVSS data (some new CVEs may not be in NVD yet)
- **On failure**: NVD rate limit is 5 requests per 30 seconds without API key. Batch in groups of 5 with 6-second delays. On 429 errors, wait 35 seconds and retry.

API: https://services.nvd.nist.gov/rest/json/cves/2.0?cveId={CVE}

### Step 5: Fetch EPSS (single batch)
- **Mode**: `deterministic`
- **Tool**: `url_fetch`
- **Input**: Comma-separated CVE IDs
- **Output**: EPSS probability scores for each CVE
- **Validate**: Most CVEs return EPSS data
- **On failure**: New CVEs may not have EPSS scores yet. Mark as "--".

API: https://api.first.org/data/v1/epss?cve={comma-separated}

### Step 6: Enrich Events
- **Mode**: `agentic`
- **Tool**: `run_python`
- **Input**: All events with CVSS/EPSS data
- **Output**: Events enriched with targetGeo, industries, actor, mitreTactics, vector
- **Validate**: Enrichment fields populated where data supports inference

Derive from titles, descriptions, CWE mappings:
- targetGeo: keyword-to-country mapping
- industries: keyword extraction (healthcare, financial, technology, etc.)
- actor: named threat actor identification
- mitreTactics: CWE-to-ATT&CK mapping + keyword inference
- vector: from CVSS vector string or keyword analysis

### Step 7: Score with 12-Factor Model
- **Mode**: `deterministic`
- **Tool**: `run_python`
- **Input**: Enriched events
- **Output**: Organizational relevance score (0-100) with factor breakdown per event

Scoring weights:
- Technology Match: 20% (DIRECT=20, STRONG=15, INDIRECT=8)
- Active Exploitation: 18%
- Threat Severity: 12%
- CISA KEV: 12%
- CVSS: 8% (9+=8, 7+=6, 4+=4, else 2)
- Exploit Availability: 7%
- Product Criticality: 6% (Tier 1=6, Tier 2=4, non-watchlist=0)
- EPSS: 5% (>=70%=5, >=30%=3, >=10%=2, else 1)
- Industry Targeting: 3%
- Source Confidence: 3%
- Intelligence Recency: 3%
- Attack Vector: 3%
- Ransomware bonus: +5

Classification: 90-100=Critical, 75-89=High, 50-74=Medium, 25-49=Low, 0-24=Informational

### Step 8: Parse and Classify RSS
- **Mode**: `agentic`
- **Tool**: `run_python`
- **Input**: Raw RSS feed contents
- **Output**: Classified articles with event type, severity, watchlist relationship
- **Validate**: Articles parsed with titles, dates, sources, classifications

### Step 9: Corroborate
- **Mode**: `agentic`
- **Tool**: `run_python`
- **Input**: KEV events + RSS articles
- **Output**: Corroboration links between sources, deduplicated events
- **Validate**: No false corroboration (e.g., Microsoft as publisher vs. Microsoft as affected vendor)

Multi-signal similarity: CVE match (95%), vendor alias match (40%), keyword Jaccard (up to 35%), vuln-type boost (+10%). Threshold: >=60% definite, >=40% probable.

### Step 10: Write Data Files
- **Mode**: `deterministic`
- **Tool**: `file_write`
- **Input**: Enriched KEV events + classified RSS articles
- **Output**: Two JSON files in agent_files/cyber_intel_command_center/
- **Validate**: Files written successfully, valid JSON, refreshDate field set to current timestamp

Files:
- data_cisa_kev.json (KEV events with full enrichment + scoreBreakdown)
- data_rss_feeds.json (classified RSS events with refreshDate)

### Step 11: Report Summary
- **Mode**: `agentic`
- **Input**: Complete enriched data
- **Output**: Structured summary with DIRECT matches, deltas, headlines

Always include: date/catalog version, total KEV count (30-day), DIRECT match table (CVE, product, CVSS, EPSS, score), delta from previous refresh, notable RSS headlines, new threat actors.

## Output

Two JSON data files written to agent_files/cyber_intel_command_center/ plus a structured text summary. The data files feed both the CICC dashboard (index.html) and the CICC Security Analyst agent.

## Lessons Learned

### Do
- Fetch all 6 sources in parallel for speed
- Use word-boundary regex for watchlist matching to prevent false positives
- Always include refreshDate in output JSON so downstream consumers can check staleness
- Show score factor breakdown for every DIRECT match so analysts understand WHY
- Batch NVD requests in groups of 5 with 6-second delays to respect rate limits
- Use vendor-level fallback: vendorProject=="Microsoft" = always DIRECT

### Don't
- Don't match "outlook" without "microsoft" context (false positive on English word)
- Don't match "entra" as substring (false positive on N-central)
- Don't classify research publishers as DIRECT matches (Unit 42 publishes research about Palo Alto threats, but Palo Alto isn't the victim)
- Don't trust RSS article text structure -- feeds vary wildly (some return XML, some plain text, some 403)

### Common Failures
- NVD 429 rate limiting: wait 35 seconds and retry the batch
- BleepingComputer/SecurityWeek 403 on direct download: use url_fetch as fallback
- New CVEs not yet in NVD or EPSS databases: mark as "--", don't fail the pipeline
- RSS XML parsing errors: wrap in try/catch, skip unparseable articles

### When to Ask the User
- When a new vendor appears that could be a watchlist addition
- When EPSS shows a dramatic shift (>3x) on a DIRECT match -- this warrants analyst attention
- When the catalog version is unchanged from last refresh (data may be cached upstream)
