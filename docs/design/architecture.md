# System Architecture

```mermaid
flowchart LR
    subgraph Splunk
        SplunkRules[Saved Searches] -->|collect| SplunkIndex[(wt_alerts Index)]
        SplunkIndex -.-> KV[(KVStore:<br>wt_alerts_reference)]
        KV -.-> KVTriage[(KVStore:<br>wt_alerts_triage)]
    end
    
    subgraph ElasticSearch
        ElasticRules[Detection Rules] -->|generate| ElasticAlerts[(Alerts Management)]
        ElasticAlerts -.-> ElasticCases[(Case Management)]
    end
    
    subgraph Workflow Automation
        Tines[Tines / n8n]
        AI[AI Triage Module]
        Tines <-->|Intelligent Sorting & Merging| AI
    end
    
    subgraph Destinations
        Jira[Jira]
        SNOW[ServiceNow]
    end

    %% Splunk Interactions
    Tines -->|REST API POLL| SplunkIndex
    Tines -->|1. Drill-down Searches| SplunkIndex
    Tines -->|2. Update Status| KV
    Tines -->|3. Save Triage Context| KVTriage
    
    %% Elastic Interactions
    Tines -->|REST API POLL| ElasticAlerts
    Tines -->|1. Drill-down Searches| ElasticAlerts
    Tines -->|2. Update Case & Triage Notes| ElasticCases
    
    %% Alert Destinations
    Tines -->|Create Ticket| Jira
    Tines -->|Create Ticket| SNOW
```
