# Watchtower SPL TA Design

## Overview

Watchtower is a **Technology Add-on (TA)** that provides index storage for alerts. External workflow tools (Tines, n8n) **PULL** from the index. Watchtower does NOT send webhooks - it keeps things simple.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Watchtower                             │
│  ┌─────────────┐    ┌─────────────────────────────────┐   │
│  │ Saved Search│───▶│ wt_alerts index                 │   │
│  └─────────────┘    │ (alert storage)                 │   │
│                     └─────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
           │                              ▲
           │ PULL via REST API            │
           ▼                              │
┌─────────────────────────────────────────────────────────────┐
│  External Workflow Tools (Tines, n8n)                      │
│  - Poll wt_alerts index                                    │
│  - Process alerts                                          │
│  - Push to ticketing (ServiceNow, Jira)                    │
└─────────────────────────────────────────────────────────────┘
```

## Design Principles

1. **No Webhooks** - Watchtower doesn't push anywhere
2. **Pull-based** - External tools poll the index
3. **Minimal** - Just storage, no forwarding logic
4. **Index-first** - wt_alerts is source of truth

## How It Works

### 1. SPL Saves to Index

Customer creates saved search that writes to wt_alerts:

```ini
[my_detection]
search = index=firewall action=blocked 
| eval wt_alert_id=uuid(), alert_create_time=now()
| collect index=wt_alerts
```

### 2. External Tool Polls

Tines/n8n polls wt_alerts via REST API:

```
GET /services/search/jobs?search=index=wt_alerts alert_status=new
```

### 3. External Tool Processes

- Tines/n8n reads alerts
- Determines severity, enrichment
- Creates tickets in ServiceNow/Jira

## Index Schema: wt_alerts

| Field | Type | Description |
|-------|------|-------------|
| wt_alert_id | string | Unique UUID |
| alert_create_time | timestamp | Creation time |
| alert_name | string | Alert name |
| alert_severity | string | low/medium/high/critical |
| alert_status | string | new/processing/resolved |
| alert_source | string | Source app |
| dest | string | Target host/IP |
| user | string | Affected user |
| rule_id | string | Rule identifier |
| raw_event | string | Original event |
| sourcetype | string | Original sourcetype |

## Usage Patterns

### Pattern 1: SPL Index → Tines/n8n → Ticketing
```
SPL (index/notables) ──▶ wt_alerts ──▶ Tines/n8n ──▶ ServiceNow/Jira
```

### Pattern 2: Saved Search → Tines/n8n → Ticketing
```
SPL (savedsearches) ──▶ wt_alerts ──▶ Tines/n8n ──▶ ServiceNow/Jira
```

### Pattern 3: AI-Powered Investigation
```
SPL (savedsearches) ──▶ wt_alerts ──▶ Tines/n8n + AI ──▶ Research/Playbook
```

## API Access

External tools access via SPL REST API:

```bash
# Search for new alerts
curl -u admin:pass \
  "https://splunk:8089/services/search/jobs" \
  -d "search=index=wt_alerts alert_status=new"

# Get alert details
curl -u admin:pass \
  "https://splunk:8089/services/search/jobs/{sid}/results"
```

## File Structure

```
watchtower/
├── default/
│   ├── app.conf              # App metadata
│   ├── collections.conf      # KVStore schema
│   └── transforms.conf       # Index definition
└── local/
    └── inputs.conf           # Local settings
```

No Python scripts needed - pure SPL-based storage!

## Key Benefits

1. **Simple** - No webhook complexity
2. **Scalable** - Index-based storage
3. **Flexible** - Any tool can poll
4. **Secure** - Uses existing SPL auth
5. **Future-proof** - Works with any workflow tool
