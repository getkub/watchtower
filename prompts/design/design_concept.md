# Watchtower Design Concept

## Overview

Watchtower provides alert storage index. External workflow tools PULL from the index.

## Architecture

```
SPL Saved Search → wt_alerts index ← Tines/n8n (PULL) → Ticketing
```

## Key Points

- **Index storage** - wt_alerts index for alerts
- **Pull-based** - External tools poll via REST API
- **Minimal** - No webhooks, no forwarding

## Usage Patterns

1. SPL Index → Workflow → Ticketing
2. Saved Search → Workflow → Ticketing  
3. AI-Powered Investigation

See `spl_design.md` for detailed implementation.
