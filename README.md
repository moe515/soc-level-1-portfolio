# SOC Level 1 — Learning Portfolio

Notes and progress log from the TryHackMe SOC Level 1 learning path. This repo documents concepts, tools, and workflows I studied — not raw answers or room walkthroughs (in line with TryHackMe's terms of service).

## About Me

Aspiring SOC Analyst building practical, hands-on skills in alert triage, log analysis, and incident response through TryHackMe's SOC Level 1 path.

## Progress

| # | Module | Room | Status | Notes |
|---|--------|------|--------|-------|
| 1 | SOC Team Internals | [SOC L1 Alert Triage](./01-SOC-Team-Internals/SOC-L1-Alert-Triage.md) | Done | Alert lifecycle, triage workflow |
| 2 | SOC Team Internals | [SOC L1 Alert Reporting](./01-SOC-Team-Internals/SOC-L1-Alert-Reporting.md) | Done | Five Ws reporting, escalation, SOC communication |
| 3 | SOC Team Internals | [SOC Workbooks and Lookups](./01-SOC-Team-Internals/SOC-Workbooks-and-Lookups.md) | Done | Identity/asset inventories, network diagrams, workbook-driven triage |
| 4 | SOC Team Internals | [SOC Metrics and Objectives](./01-SOC-Team-Internals/SOC-Metrics-and-Objectives.md) | Done | MTTD/MTTA/MTTR, False Positive/Escalation/Detection rates |
| 5 | SOC Team Internals | [SOC Simulator: Introduction to Phishing](./01-SOC-Team-Internals/SOC-Simulator-Introduction-to-Phishing.md) | Done | End-to-end triage under time pressure, AI-reviewed reporting |
| 6 | Core SOC Solutions | [Introduction to EDR](./02-Core-SOC-Solutions/Introduction-to-EDR.md) | Done | EDR vs antivirus, agent/console architecture, telemetry, detection and response capabilities |
| 7 | Core SOC Solutions | [Introduction to SIEM](./02-Core-SOC-Solutions/Introduction-to-SIEM.md) | Done | Host vs network-centric logs, SIEM features, log ingestion, detection rules, alert investigation |
| 8 | Core SOC Solutions | [Splunk: The Basics](./02-Core-SOC-Solutions/Splunk-The-Basics.md) | Done | Forwarder/Indexer/Search Head architecture, uploading data, SPL query basics |
| 9 | Core SOC Solutions | [Elastic Stack: The Basics](./02-Core-SOC-Solutions/Elastic-Stack-The-Basics.md) | Done | Elastic data flow, Kibana Discover, KQL filtering, Lens visualizations and dashboards |

## Skills Covered So Far

- Alert lifecycle: event to alert to triage to verdict
- Alert management platforms (SIEM, EDR/NDR, SOAR, ITSM)
- Alert properties and how to read them (severity, status, verdict, fields)
- Alert prioritization strategy (filter, severity, time)
- Alert reporting using the Five Ws (Who, What, When, Where, Why)
- Escalation criteria and SOC crisis communication
- Identity and asset inventories for fast investigation context
- Reading network diagrams to trace attack paths
- Following structured SOC workbooks (Enrichment, Investigation, Escalation)
- Core SOC metrics (AC, FPR, AER, TDR) and SLA timing metrics (MTTD, MTTA, MTTR)
- Running a full triage workflow end-to-end under a simulated live shift
- Differentiating EDR from traditional antivirus
- Reading EDR telemetry and process chains to reconstruct an attack path
- Recognizing EDR detection techniques (behavioral, anomaly, IOC, MITRE ATT&CK, ML)
- Knowing EDR response actions (isolate, terminate, quarantine, remote access, artifact collection)
- Distinguishing host-centric vs. network-centric log sources
- Understanding core SIEM capabilities: collection, normalization, correlation, alerting, dashboards
- Building/reading detection rules from log source, Event ID, and field conditions
- Knowing common log ingestion methods (agent, syslog, manual upload, port forwarding)
- Explaining Splunk's Forwarder / Indexer / Search Head architecture
- Uploading and indexing raw log files into Splunk
- Writing basic SPL queries (index scoping, spath, search filtering, stats aggregation)
- Explaining the Beats/Elastic Agent, Logstash, Elasticsearch, and Kibana data flow
- Investigating VPN telemetry in Kibana Discover with data views and time filters
- Writing field-based KQL with AND, OR, NOT, parentheses, and wildcards
- Correlating users, source IPs, geography, actions, and timestamps
- Building and saving Kibana Lens visualizations and custom dashboards

## Repo Structure

Each module has its own folder. Inside, one markdown file per room with:
- Objective — what the room covers
- Key Concepts — summarized in my own words
- Skills Gained — practical takeaways

Note: No direct answers, flags, or step-by-step solutions are published here, per TryHackMe's rules.

## Certifications Target

- [ ] SAL1 (Security Analyst Level 1)

## How I Log Each Room (Workflow)

Every time I finish a room, I add:
1. A folder under the matching module (create one if it's new).
2. A markdown note with Objective, Key Concepts, and Skills Gained, in my own words.
3. A new row in the Progress table above.
