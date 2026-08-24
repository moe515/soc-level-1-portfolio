# Splunk: The Basics

**Path:** SOC Level 1 -> Core SOC Solutions  
**Room:** Splunk: The Basics  
**Status:** Completed

## Objective

Understand how Splunk collects, processes, stores, and searches security telemetry, then apply basic SPL searches to investigate structured VPN logs.

## Core Architecture

| Component | Primary Role |
|---|---|
| **Forwarder** | Lightweight agent that collects endpoint or server data and sends it to Splunk. |
| **Indexer** | Parses incoming data, creates searchable events, and stores it in indexes. |
| **Search Head** | Analyst interface for running SPL searches, reviewing results, and building reports or dashboards. |

Data flow:

`Log Source -> Forwarder -> Indexer -> Search Head -> Analyst`

## Navigating Splunk

- **Splunk Bar:** messages, settings, search activity, help, and app navigation.
- **Apps Panel:** lists installed applications; Search & Reporting is used for investigations.
- **Explore Splunk:** provides shortcuts for adding data and accessing documentation.
- **Dashboards:** present saved searches and visualizations for monitoring and analysis.

## Adding Data

Splunk supports multiple ingestion methods:

| Method | Use Case |
|---|---|
| **Upload** | Import a local file for indexing and analysis. |
| **Monitor** | Continuously watch local files, directories, or network ports. |
| **Forward** | Receive data from a Splunk forwarder on another system. |

The practical workflow was:

1. Upload a newline-delimited JSON VPN log file.
2. Confirm the detected JSON source type.
3. Store the events in a dedicated index.
4. Open Search & Reporting and set the time range appropriately.
5. Validate ingestion before applying investigation filters.

## SPL Fundamentals

```spl
index="<index_name>"
| stats count
```

Counts all events available in the selected index.

```spl
index="<index_name>"
| spath
| search UserName="<user>"
| stats count
```

Extracts JSON fields, filters by a username, and counts matching events.

```spl
index="<index_name>"
| spath
| search Source_ip="<ip_address>"
| stats values(UserName) AS UserName count
```

Pivots from a source IP to the associated usernames and event count.

```spl
index="<index_name>"
| spath
| search Source_Country!="<country>"
| stats count
```

Uses a negative field filter to count events not associated with a specified country.

## Hands-On Exercise (Sanitized)

I ingested structured VPN telemetry and used SPL to:

- Verify the total number of indexed events.
- Extract JSON key-value fields with `spath`.
- Filter activity by username and source IP.
- Pivot from an IP address to an associated user.
- Exclude a geographic value with `!=`.
- Aggregate investigation results with `stats count` and `values()`.
- Troubleshoot searches by checking the index name and time picker.

Raw room answers and flags are intentionally excluded from this public portfolio.

## Investigation Takeaways

- Start broad with the index, verify data exists, and narrow the search one condition at a time.
- Confirm the time range before assuming that data is missing.
- Structured data is useful only after the relevant fields are parsed or extracted.
- IP, user, geography, host, and time are common SOC pivot points.
- A count is a summary; analysts should still inspect representative raw events to understand context.

## Skills Gained

- Explaining Splunk's Forwarder, Indexer, and Search Head architecture.
- Uploading and indexing structured log data.
- Navigating Search & Reporting.
- Writing basic SPL pipelines.
- Extracting JSON fields with `spath`.
- Filtering and aggregating events for an investigation.
- Using user, IP, and country fields as investigation pivots.

## References

- [Splunk — About the search language](https://help.splunk.com/en/splunk-enterprise/search/search-manual/10.4/search-overview/about-the-search-language)
- [Splunk — Upload data](https://help.splunk.com/en/splunk-enterprise/get-started/get-data-in/9.4/how-to-get-data-into-your-splunk-deployment/upload-data)
- [Splunk — spath command](https://help.splunk.com/en?resourceId=Splunk_SearchReference_Spath)
- [Splunk — stats command](https://help.splunk.com/en?resourceId=Splunk_SearchReference_Stats)
