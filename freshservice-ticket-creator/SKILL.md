---
name: freshservice-ticket-creator
display_name: Freshservice Ticket Creator
description: >
  Universal base for creating Freshservice tickets from any agent's findings and sending
  Outlook email notifications with recommended actions. Activate when any agent needs to
  open a Freshservice ticket, report a finding to IT, or notify a team about an action item.
  Triggers on: create ticket, send to freshservice, ticket this finding, open IT ticket,
  notify team about finding.
icon: "--"
trigger: create freshservice ticket send to freshservice ticket this open ticket notify team finding
patterns:
  - {pattern: "\\bfreshservice\\b", confidence: 0.90}
  - {pattern: "\\bcreate.*ticket\\b", confidence: 0.80}
  - {pattern: "\\bticket this\\b", confidence: 0.85}
inputs:
  - name: finding
    description: "Agent finding dict with subject, description, priority, source_agent, and optional fields (category, recommended_actions, tags, custom_fields)"
    type: object
    required: true
  - name: notify
    description: "Whether to send email notification after ticket creation"
    type: boolean
    default: true
  - name: notify_email
    description: "Override recipient email for the notification. Defaults to admin@example-company.com"
    type: string
    required: false
scripts: [freshservice_universal_base.py]
tools: [run_python, run_javascript, file_read, file_write, url_fetch]
id: 42a8a69426bf4702a9f2dc02df41d3c9
---

# Freshservice Ticket Creator

## Overview
Universal base module that converts any Amazon Quick agent's findings into Freshservice
tickets via REST API v2 and sends priority-coded email notifications with recommended
actions via Outlook. Any agent (SOC 2, CICC, Dashboard, future agents) calls this single
interface -- the ticket creation and notification pipeline is standardized.

## Configuration
- Domain: example-company.freshservice.com
- Auth: Basic Auth (API Key as username, "X" as password)
- API Key: stored in freshservice_universal_base.py
- Default requester: admin@example-company.com (SwooshJ-SecAI)

## Workflow

### Step 1: Load the Universal Base Module
- **Mode**: `deterministic`
- **Tool**: `run_python`
- **Input**: The bundled `freshservice_universal_base.py` script
- **Output**: All builder functions loaded into the Python namespace
- **Validate**: `build_ticket_payload` and `process_finding` are callable
- **On failure**: Check that the script file exists at the expected path

```python
exec(open("agent_files/freshservice_base/freshservice_universal_base.py").read())
```

### Step 2: Construct the Finding Dict
- **Mode**: `agentic`
- **Input**: `{{finding}}` -- the agent's raw finding data
- **Output**: A well-formed finding dict
- **Validate**: Dict has required keys: subject, description, priority, source_agent
- **On failure**: Prompt the calling agent for missing required fields

The finding dict must contain at minimum:
```python
finding = {
    "subject": "...",           # Ticket subject line
    "description": "...",       # HTML or plain text description
    "priority": "high",         # low | medium | high | urgent
    "source_agent": "...",      # Name of the originating agent
}
```

Optional fields: category, subcategory, recommended_actions (list of strings),
tags (list), custom_fields (dict), requester_email, group_id, agent_id, ticket_type.

### Step 3: Build Payload and API Request
- **Mode**: `deterministic`
- **Tool**: `run_python`
- **Input**: The finding dict from Step 2
- **Output**: ticket_payload (Freshservice API schema) and api_request (full HTTP spec)
- **Validate**: api_request contains url, method, headers, body
- **On failure**: Check field mapping -- priority/status strings must be lowercase

```python
result = process_finding(finding)
ticket_payload = result["ticket_payload"]
api_request = result["api_request"]
```

### Step 4: Execute the API Call
- **Mode**: `deterministic`
- **Tool**: `run_javascript`
- **Input**: api_request dict with url, method, headers, body
- **Output**: Freshservice API JSON response with created ticket data
- **Validate**: Response contains "ticket" key with an "id" field
- **On failure**: Check auth header, verify domain is reachable, inspect HTTP status code

Use run_javascript with Node.js https module or load the browser skill to execute
a fetch() POST request with the full auth headers and JSON body from the api_request spec.

### Step 5: Parse and Validate Response
- **Mode**: `deterministic`
- **Tool**: `run_python`
- **Input**: Raw API response from Step 4
- **Output**: Parsed ticket object with ID, or error details
- **Validate**: ticket_data["success"] is True and ticket has an "id"
- **On failure**: Log the error, surface to calling agent, do not proceed to notification

```python
ticket_data = parse_ticket_response(api_response)
if not ticket_data["success"]:
    raise Exception(f"Ticket creation failed: {ticket_data}")
ticket = ticket_data["ticket"]
```

### Step 6: Send Email Notification
- **Mode**: `agentic`
- **Input**: Created ticket data + original finding dict + `{{notify}}` flag + `{{notify_email}}`
- **Output**: Email sent to the designated team
- **Validate**: Email send confirmation
- **On failure**: Log the failure but do not fail the overall workflow -- the ticket exists

Only executes if `{{notify}}` is true (default). Load the outlook_builtin skill, then
build a priority-coded HTML email with the ticket link, finding details, and
recommended actions list using build_notification_email().

```python
if notify:
    email = build_notification_email(ticket, finding)
    # Send via Outlook skill tools
```

## Output
- A created Freshservice ticket with ID and URL
- An email notification sent to the designated team with:
  - Priority-coded header (color-coded by severity)
  - Ticket link
  - Finding details and source agent attribution
  - Ordered list of recommended actions
  - Auto-generated footer with agent provenance

## Finding Schema Reference

| Field              | Type      | Required | Default                        |
|--------------------|-----------|----------|--------------------------------|
| subject            | str       | yes      | "Agent Finding - Action Required" |
| description        | str       | yes      | --                             |
| priority           | str       | yes      | "medium"                       |
| source_agent       | str       | yes      | "Unknown Agent"                |
| category           | str       | no       | --                             |
| subcategory        | str       | no       | --                             |
| recommended_actions| list[str] | no       | []                             |
| requester_email    | str       | no       | admin@example-company.com     |
| requester_name     | str       | no       | SwooshJ-SecAI                |
| group_id           | int       | no       | --                             |
| agent_id           | int       | no       | --                             |
| ticket_type        | str       | no       | "Incident"                     |
| status             | str       | no       | "open"                         |
| tags               | list[str] | no       | --                             |
| custom_fields      | dict      | no       | --                             |
| notify_email       | str       | no       | admin@example-company.com     |

## Freshservice API Field Maps
- Priority: low=1, medium=2, high=3, urgent=4
- Status: open=2, pending=3, resolved=4, closed=5
- Source: email=1, portal=2, phone=3, chat=4, api=9

## Lessons Learned

### Do
- Always set source=9 (API) so Freshservice tracks automated ticket origin
- Include the source_agent name in the ticket description for traceability
- Use priority-coded colors in email headers so recipients can triage visually
- Include the direct ticket URL in notifications so teams can act immediately

### Don't
- Do not use url_fetch for Freshservice API calls -- it only supports unauthenticated GET
- Do not hardcode group_id or agent_id -- these vary by finding type and should come from the agent
- Do not skip the parse step -- Freshservice error responses need structured handling

### Common Failures
- 401 Unauthorized: API key expired or incorrect -- verify in Freshservice admin
- 400 Bad Request: Required field missing or invalid enum value -- check priority/status mapping
- Network timeout: Freshservice may rate-limit -- implement retry with backoff
- Email send failure: Outlook token expired -- does not affect ticket creation, log and continue

### When to Ask the User
- When group_id or agent assignment is needed but not provided in the finding
- When the finding priority is ambiguous and could be high or urgent
- When the notify_email is not specified and the finding category suggests a specific team
