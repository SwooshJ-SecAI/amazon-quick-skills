---
name: freshservice-ticket-pipeline
display_name: Freshservice Ticket Pipeline
version: 2.0.0
description: >
  Full Freshservice ITSM integration for Example Corp. Activate when the user asks to
  create/close/query/update Freshservice tickets, add notes, search tickets, reassign,
  escalate, list assets/agents/groups, create change requests, problems, or releases,
  or any operation against example-company.freshservice.com. Five composable Python modules
  in agent_files/freshservice_base/ cover the complete API surface.
icon: "--"
trigger: freshservice ticket create close query update change problem release itsm note assign escalate asset
patterns:
  - {pattern: "\\bfreshservice\\b", confidence: 0.95}
  - {pattern: "\\bticket\\b.*\\b(close|create|update|search|list|assign|escalate)\\b", confidence: 0.80}
  - {pattern: "\\b(change request|problem record|release)\\b", confidence: 0.75}
inputs:
  - name: finding
    description: "Agent finding dict for ticket creation (subject, description, priority, source_agent, etc.)"
    type: object
    required: false
  - name: ticket_id
    description: "Freshservice ticket ID for read/update/close/note operations"
    type: integer
    required: false
  - name: status
    description: "Target ticket status: open, pending, resolved, or closed"
    type: string
    required: false
  - name: priority
    description: "Target ticket priority: low, medium, high, or urgent"
    type: string
    required: false
  - name: query
    description: "Freshservice query string for ticket/asset search operations"
    type: string
    required: false
tools_used:
  - run_python
  - run_python_with_write
  - browser (for HTTP requests to Freshservice REST API)
  - outlook_builtin (for email notifications)
id: 386e05ed0f2a4417a7cb73aa150eedfc
---

# Freshservice Ticket Pipeline

## Overview

Full ITSM integration pipeline for example-company.freshservice.com. Five composable
Python components covering ticket creation, lifecycle management, read/query operations,
field updates, and ITSM entities (changes, problems, releases). Any Amazon Quick agent
can import individual components -- each imports shared config from the base module
with zero duplication.

## Component Inventory

| Component | File | Functions | Purpose |
|---|---|---|---|
| Base (Creation) | freshservice_universal_base.py | 7 | Create tickets, format descriptions, route to teams, notify |
| Close (Lifecycle) | freshservice_close_ticket.py | 7 | Notes, status transitions, close with audit trail, bulk close |
| Query (Read) | freshservice_query.py | 15 | Search/list/get tickets, people, assets, metrics |
| Update (Modify) | freshservice_update.py | 7 | Assign, escalate, categorize, tag, set due dates |
| ITSM (Entities) | freshservice_itsm.py | 12 | Changes, problems, releases (full CRUD) |

All files live in: `agent_files/freshservice_base/`

## Configuration

- Domain: example-company.freshservice.com
- Auth: Basic Auth (API Key)
- API Version: v2
- Default requester: admin@example-company.com
- Team routing: AGENT_GROUP_MAP (Security Team, SOC Compliance)

## Workflow Steps

### Step 1: Identify the Operation [agentic]
- **Input**: User request (e.g. "create a ticket", "close ticket #75939", "list open tickets")
- **Output**: Which component and function to use
- **Validate**: Operation maps to a known function in one of the 5 components

Determine which component handles the request:
- Creating tickets from findings --> Base module
- Closing/noting/status changes --> Close module
- Reading/searching/listing --> Query module
- Updating fields (priority, assign, tags) --> Update module
- Changes/problems/releases --> ITSM module

### Step 2: Load the Component [deterministic]
- **Tool**: `run_python`
- **Input**: Component file path from agent_files/freshservice_base/
- **Output**: Functions loaded into namespace

```python
import sys
sys.path.insert(0, "agent_files/freshservice_base")
# Import the specific component needed:
from freshservice_query import list_tickets, search_tickets, get_ticket
# or: from freshservice_close_ticket import close_ticket_with_note
# or: from freshservice_update import assign_ticket, change_priority
# or: from freshservice_itsm import create_change, list_changes
```

### Step 3: Build the Request [agentic]
- **Input**: User parameters (ticket_id, finding dict, query string, etc.)
- **Output**: Prepared request dict with method, URL, headers, payload

Each function returns a prepared request dict -- it does NOT execute the HTTP call.
The calling agent decides how to execute (browser fetch, requests library, etc.).

For ticket creation, build the finding dict first:
```python
finding = {
    "subject": "...",
    "description": "...",
    "priority": "high",
    "source_agent": "Agent Name",
    "category": "Software",
    "recommended_actions": ["Action 1", "Action 2"],
}
result = process_finding(finding)
```

For lifecycle operations:
```python
note = build_resolution_note(
    finding_summary="...",
    action_taken="...",
    closure_rationale="...",
)
request = close_ticket_with_note(ticket_id=75939, note_body=note)
```

### Step 4: Execute the API Call [deterministic]
- **Tool**: `browser` (fetch) or `run_python` (requests)
- **Input**: Prepared request dict from Step 3
- **Output**: Freshservice API response

Execute the request via browser fetch():
```javascript
const resp = await fetch(request.url, {
    method: request.method,
    headers: request.headers,
    body: JSON.stringify(request.payload)
});
const data = await resp.json();
```

For two-step operations (close_ticket_with_note), execute step_1_note first,
confirm success, then execute step_2_close.

### Step 5: Parse and Report [agentic]
- **Input**: API response from Step 4
- **Output**: Human-readable summary of what happened

For creation: Report ticket ID, URL, assigned team.
For queries: Format results as a clean table or summary.
For updates: Confirm what changed.
For lifecycle: Confirm note added and status changed.

## API Field Maps

**Priority**: low=1, medium=2, high=3, urgent=4
**Status**: open=2, pending=3, resolved=4, closed=5
**Source**: email=1, portal=2, phone=3, chat=4, api=9
**Valid Categories**: Hardware, Software, Network, Other
**Change Types**: minor=1, standard=2, major=3, emergency=4
**Risk Levels**: low=1, medium=2, high=3, very_high=4

## Lessons Learned

### Do
- Always add the private note BEFORE closing a ticket -- some FS configs restrict modifications to closed tickets
- Use "Software" category for security findings -- "Security" is not a valid FS category
- Include audit-trail context in resolution notes (finding, action taken, closure rationale)
- Validate category against VALID_CATEGORIES set before submitting
- Use AGENT_GROUP_MAP for automatic team routing based on source agent

### Don't
- Don't use "Security" as a category -- it will be rejected by the API
- Don't send test emails automatically -- wait for explicit user trigger
- Don't hardcode group IDs in agent instructions -- use AGENT_GROUP_MAP lookups
- Don't close tickets without a resolution note -- breaks the SOC 2 evidence chain

### Common Failures
- Category validation: "Security" rejected -- map to "Software"
- Auth failures: Check API key in FS_CONFIG
- 404 on ticket operations: Verify ticket_id exists and is accessible
- Rate limiting: FS API limits to ~50 requests/minute -- add delays for bulk ops

### When to Ask the User
- Which team/group to assign a ticket to (if not auto-routable via AGENT_GROUP_MAP)
- Whether to send email notification after ticket creation
- Confirmation before bulk operations (closing multiple tickets)
- Custom field values (agent can't know instance-specific custom fields)

## Architecture
See: agent_files/freshservice_base/freshservice_architecture.html (v4.0)

## Output
Depends on the operation:
- Creation: Ticket ID + URL + confirmation
- Query: Formatted results table or detail view
- Update: Confirmation of field changes
- Close: Confirmation with note + status change
- ITSM: Entity ID + confirmation
