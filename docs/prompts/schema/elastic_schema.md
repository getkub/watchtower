# Elastic Watchtower Schema

Unlike Splunk's explicit index + KVStore design, Elastic pairs native Alerts Management with Case Management. 

**Core Metadata Mapping:**
- `alert.id` (UUID) 
- `@timestamp` (Alert generation time)
- `kibana.alert.rule.name` / `rule.name` (Detection Name)
- `kibana.alert.severity` / `event.severity` (low/medium/high/critical)
- `rule.id` (Identifier of the detection rule)
- `data_stream.dataset` (Original log source type)

**Alert Payload (Flexible):**
- `payload` / `alert_data`: The rich ECS-formatted document array/object containing all dynamic event details.

**State Tracking (via Elastic Case Management Tags):**
Because native case statuses may vary, Watchtower systematically syncs states across all tools using specific `wt:<status>` tags on cases:
- `wt:unknown`
- `wt:new`
- `wt:progress`
- `wt:pending`
- `wt:resolved`
- `wt:closed`
- `case.id` (Native case reference)
- Additional tags linking back to Jira/ServiceNow incident IDs.
