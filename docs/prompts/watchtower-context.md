# Watchtower Context & Design Prompt

Use this document as the system context when building or modifying the Watchtower project.

## Overview
Watchtower is a lightweight alert storage system designed as a bridge between SIEM platforms (Splunk, ElasticSearch) and Workflow Automation tools (Tines, n8n). 

**Core Design Principles:**
- **Zero Push / No Webhooks**: Watchtower never pushes data out. 
- **Pull-Based Integration**: External tools (Tines/n8n) poll the SIEM via its native REST API.
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
Whether gathered from Splunk or Elastic, the alerts track the following core data model:
- `wt_alert_id` (UUID)
- `alert_create_time`
- `alert_name`
- `alert_severity` (low/medium/high/critical)
- `alert_status` (new/processing/resolved)
- `alert_source`
- `dest` (Target host/IP)
- `user`
- `rule_id`
- `raw_event`
- `sourcetype`

## Directory Structure Overview
- `docs/design/architecture.md`: Common high-level architecture diagram.
- `docs/design/splunk/`: Splunk-specific data flow and lifecycle diagrams.
- `docs/design/elastic/`: Elastic-specific data flow and lifecycle diagrams.
- `docs/prompts/`: Master context prompts for AI assistants (You are here).
- `splunk/apps/TA_watchtower/`: The Splunk Technology Add-on configuration fields (`collections.conf`, `transforms.conf`, etc.).
