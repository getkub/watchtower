# Watchtower Context & Design Prompt

Use this document as the system context when building or modifying the Watchtower project.

### ⚠️ AI Assistant Directive ⚠️
**IMPORTANT**: You must continuously update the prompt documents (in `docs/prompts/`) and design artifacts (in `docs/design/`) every time there is a major architectural, conceptual, or schema change during your sessions. Keeping these documents precisely aligned with the code is critical.

## Overview
Watchtower is a lightweight alert storage system designed as a bridge between SIEM platforms (Splunk, ElasticSearch) and Workflow Automation tools (Tines, n8n). 

**Core Design Principles:**
- **Zero Push / No Webhooks**: Watchtower never pushes data out. 
- **Pull-Based Integration**: External tools (Tines/n8n) poll the SIEM via its native REST API.
- **AI-Driven Auto-Triage**: Orchestrators use AI specifically to dynamically run drill-down searches across the SIEM, intelligently sort/merge alerts, and write 1st-level triage context *back* into the SIEM natively (via KVStore `wt_alerts_triage` in Splunk or Case updates in Elastic).
- **Minimal Logic**: Watchtower provides only the storage layer (index, KVStore, case management) and relies on external orchestrators to execute all routing, enrichment, and ticketing (e.g., Jira, ServiceNow).

## Architecture

1. **Splunk Integration (`TA_watchtower`)**
   - **Alert Generation**: Saved searches write detection events into the `wt_alerts` index.
   - **State Tracking**: A KVStore collection `wt_alerts_reference` maintains the alert lifecycle status (`new`, `processing`, `resolved`), and holds automation metadata (e.g., `sent_to_soar`).
   - **Polling**: Tines/n8n polls `/services/search/jobs` for new alerts and updates the KVStore via REST.

2. **ElasticSearch Integration**
   - **Alert Generation**: Elastic Detection Rules generate alerts in the native Alerts Management index.
   - **State Tracking**: Elastic Case Management is used to track the lifecycle of the alert processing.
   - **Polling**: Tines/n8n polls Elastic Alerts via native REST APIs and updates Case objects.

## Data Schema
Each supported SIEM product implements its own specific schema mapping that strictly divides generalized metadata (e.g., detection name, UUID, status) from the flexible alert payload.

See the specific schema definition AI prompts here:
- [Splunk TA Schema](schema/splunk_schema.md)
- [Elastic Schema](schema/elastic_schema.md)

## Directory Structure Overview
- `docs/design/architecture.md`: Common high-level architecture diagram.
- `docs/design/splunk/`: Splunk-specific data flow and lifecycle diagrams.
- `docs/design/elastic/`: Elastic-specific data flow and lifecycle diagrams.
- `docs/prompts/`: Master context prompts for AI assistants (You are here).
- `splunk/apps/TA_watchtower/`: The Splunk Technology Add-on configuration fields (`collections.conf`, `transforms.conf`, etc.).
