# 🗼 Watchtower

> *A unified bridge between SIEMs and Workflow Automation—where alerts converge, AI triages, and noise ceases.*

Watchtower provides a modern **pull-based, AI-driven architecture** to intelligently store, route, and triage security alerts from platforms like Splunk and ElasticSearch.

## 🎯 The Aim
To eliminate webhook complexity and empower AI to perform automated 1st-level triage directly against your SIEM, delivering only highly-enriched, noise-free incidents to your analysts.

## ✨ Key Features

- **📥 Zero-Push Architecture**: No messy webhooks. Orchestrators (Tines/n8n) securely *poll* Watchtower via native REST APIs.
- **🧠 AI Auto-Triage**: AI autonomously groups related alerts and executes deep drill-down searches to gather SIEM context.
- **💾 Native State Tracking**: Triage AI summaries are written straight back into your SIEM (e.g., Splunk KVStore or Elastic Case Management).
- **🛤️ Unified Schema**: A standardized metadata model coupled with a highly flexible JSON `payload` supports any alert type.
- **🎫 Enriched Delivery**: Jira and ServiceNow receive fully contextualized, deduplicated incident tickets.

---

### 📚 Core Documentation
- 🗺️ **[Architecture Design](docs/design/architecture.md)**
- 🤖 **[AI Prompts & Context](docs/prompts/watchtower-context.md)**

*Built to scale. Designed for simplicity.*
