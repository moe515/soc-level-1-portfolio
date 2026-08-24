# Splunk: The Basics

**Path:** SOC Level 1 -> Core SOC Solutions
**Room:** Splunk: The Basics (https://tryhackme.com/room/splunk101) - 30 min
**Status:** Completed

## Objective
Understand how Splunk (a leading SIEM solution) is structured, how logs get ingested into it, and how to search/analyze indexed data using SPL (Search Processing Language).

## Key Concepts

### 1. Splunk's Three Core Components
- **Forwarder** - lightweight agent installed on the endpoint being monitored. Collects data (web traffic, Windows Event Logs/PowerShell/Sysmon data, Linux host-centric logs, database connection logs, etc.) and sends it to the Splunk instance. Minimal performance impact on the host.
- **Indexer** - processes the data received from forwarders: parses and normalizes it into field-value pairs, categorizes it, and stores the results as searchable events.
- **Search Head** - the interface (Search & Reporting app) where analysts query the indexed logs using SPL, and where results can be turned into presentable tables, charts, and visualizations.

### 2. Navigating Splunk
- **Splunk Bar** (top panel) - system notifications/messages, Settings (instance configuration), Activity (search job/process progress), Help (docs/tutorials), Find (search across the app), and switching between installed apps.
- **Apps Panel** - shows installed Splunk apps; Search & Reporting is the default.
- **Explore Splunk** - quick links to add data, add new apps, and access documentation.
- **Home Dashboard** - default view is empty; dashboards can be selected from a listing page or built and pinned here.

### 3. Adding Data
Splunk can ingest almost any data type - firewall logs, website logs, network events, cloud/security/virtualization/application sources, etc. Ingestion methods include:
- **Upload** - upload files directly from a local machine (used in this room's lab for a VPN log file).
- **Monitor** - watch a file/data stream on the local Splunk platform host in real time.
- **Forward** - receive data forwarded from a remote Splunk Forwarder.

The upload workflow: select the log file and source type -> input settings (choose/create the index the data will be stored under, associate a host name) -> review configuration -> complete.

### 4. Search Processing Language (SPL) Basics
Once data is indexed, it can be queried in the Search & Reporting app. Core patterns used:
- `index="<name>"` - scope the search to a specific index.
- `spath` - extract fields from structured data (e.g. JSON) so they can be searched/filtered on.
- `search <field>="<value>"` - filter events by a field's value (supports `!=` for "not equal to").
- `stats count` - aggregate/count matching events.

Example pattern: `index="vpn_logs" | spath | search UserName="<user>" | stats count` - narrows all VPN log events down to a specific user and returns how many matched.

## Skills Gained

- Explaining Splunk's Forwarder / Indexer / Search Head architecture
- Navigating the Splunk interface (Splunk Bar, Apps Panel, dashboards)
- Uploading and indexing a raw log file into Splunk
- Writing basic SPL queries: index scoping, spath field extraction, search filtering, stats aggregation
- Comfort reading real Splunk Search & Reporting output (event lists and statistics tables)

## Personal Notes

Third room in Core SOC Solutions, first hands-on time in an actual Splunk instance rather than a described dashboard. Uploading raw JSON VPN logs and then filtering them down field by field (by user, by source IP, by country) made the SIEM normalization/correlation concepts from the previous room concrete - could directly see how spath turns nested JSON into searchable fields.
