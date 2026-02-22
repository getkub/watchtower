# Sources

SIEM alert sources that Watchtower can receive alerts from.

## Supported Sources

- **Splunk** - Splunk Enterprise Security alerts via HEC
- **Elastic** - Elasticsearch watcher alerts
- **AWS Security Hub** - AWS security findings
- **Azure Sentinel** - Microsoft Sentinel alerts
- **GCP SCC** - Google Cloud Security Command Center

Each source folder contains the integration code to receive/ingest alerts from that platform.
