# Watchtower - SPL TA

Alert storage index for external workflow tools.

## Overview

Watchtower provides a simple **wt_alerts index** for storing alerts. External workflow tools (Tines, n8n) **PULL** from this index. No webhooks, no forwarding - just storage.

## Architecture

```
SPL Saved Search ──▶ wt_alerts index ◀── Tines/n8n (PULL) ──▶ Ticketing
```

## Usage

### 1. Create Saved Search

In your app, create a saved search that writes to wt_alerts:

```ini
[my_detection]
search = index=firewall action=blocked 
| eval wt_alert_id=uuid(), alert_create_time=now()
| collect index=wt_alerts
```

### 2. External Tool Polls

Tines/n8n polls via SPL REST API:

```bash
GET /services/search/jobs?search=index=wt_alerts alert_status=new
```

## Index Schema

| Field | Type | Description |
|-------|------|-------------|
| wt_alert_id | string | Unique UUID |
| alert_create_time | timestamp | Creation time |
| alert_name | string | Alert name |
| alert_severity | string | low/medium/high/critical |
| alert_status | string | new/processing/resolved |
| dest | string | Target host/IP |
| user | string | Affected user |
| raw_event | string | Original event |

## Files

```
watchtower/
├── default/
│   ├── app.conf        # App metadata
│   ├── collections.conf # KVStore (optional)
│   └── transforms.conf  # Index definition
└── local/
    └── (empty - no config needed)
```

## Integration

External tools access via REST API:

```bash
curl -u user:pass "https://splunk:8089/services/search/jobs" \
  -d "search=index=wt_alerts alert_status=new | head 10"
```
