# Alert Lifecycle

```mermaid
stateDiagram-v2
    [*] --> New: Splunk Saved Search triggers
    
    New --> Processing: Workflow Tool polls & picks up
    
    state Processing {
        [*] --> Enriching
        Enriching --> Routing
        Routing --> Ticketing
    }
    
    Processing --> Resolved: Ticket Created / Action Taken
    Processing --> Failed: Error in Automation
    
    Failed --> Processing: Retry mechanism
    
    Resolved --> [*]
```
