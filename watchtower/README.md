# Watchtower

Bridge security alerts from SIEM tools directly to SOAR platforms.

## Purpose

Watchtower forwards alerts from SIEM platforms (Splunk, Elasticsearch, AWS Security Hub, etc.) directly to SOAR tools (Tines, n8n), bypassing the native alerting in SIEM products.

```
SIEM Tools (Splunk, Elastic, etc.) → Watchtower → SOAR (Tines, n8n)
```

## Structure

```
watchtower/
├── src/
│   ├── sources/     # SIEM alert sources (Splunk, Elastic, etc.)
│   └── sinks/      # SOAR destinations (Tines, n8n)
├── config/         # Configuration
├── tests/          # Tests
└── docs/           # Documentation
```

## Quick Start

```bash
# Configure sources and sinks
cp config/example.yaml config.yaml

# Run
python -m watchtower
```
