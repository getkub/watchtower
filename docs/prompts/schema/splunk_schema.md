# Splunk Watchtower Schema & Mapping

**Core Metadata (Required Fields):**
- `wt_alert_id` (UUID)
- `alert_create_time` (Timestamp of evaluation)
- `alert_name` (Name of the saved search detection)
- `alert_severity` (low/medium/high/critical)
- `alert_source` (Typically 'splunk')
- `rule_id` (Identifier of the detection rule)
- `sourcetype` (Original log source type)

**Alert Payload (Flexible):**
- `payload`: A generic JSON string containing the extracted fields specific to this alert (e.g., `dest`, `user`, `file_hash`).
- `raw_event`: The raw, original log string (if applicable).

**State Tracking (KVStore `wt_alerts_reference` Specific):**
- `alert_status` (new/processing/resolved)
- `sent_to_soar` (Boolean)
- `soar_incident_id` (String ID mapped from Jira/ServiceNow)
- `soar_story_name` (String)
- `soar_response` (String)
- `retry_count` (Number)
- `last_updated` (Number)
- `error_message` (String)
