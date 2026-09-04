# Elastic Stack: The Basics

**Path:** SOC Level 1 -> Core SOC Solutions  
**Room:** Elastic Stack: The Basics  
**Status:** Completed

## Objective

Understand how the Elastic Stack collects, processes, stores, searches, and visualizes security telemetry, then use Kibana to investigate structured VPN logs with filters, KQL, visualizations, and dashboards.

## Elastic Stack Architecture

| Component | Primary Role |
|---|---|
| **Beats / Elastic Agent** | Lightweight data shippers that collect logs, metrics, Windows events, network metadata, and other endpoint telemetry. |
| **Logstash** | Processes events through input, filter, and output stages; parses, enriches, and normalizes data before delivery. |
| **Elasticsearch** | Indexes and stores JSON documents for near-real-time search and analytics. |
| **Kibana** | Analyst interface for searching, filtering, visualizing, and presenting Elasticsearch data. |

Data flow:

`Log Sources -> Beats / Elastic Agent -> Logstash -> Elasticsearch -> Kibana -> SOC Analyst`

## Kibana Discover Workflow

A repeatable investigation workflow in Discover is:

1. Select the correct data view.
2. Set a time range that includes the relevant events.
3. Confirm that logs are visible before adding filters.
4. Review available fields and their top values.
5. Add relevant fields as table columns.
6. Narrow the dataset using filters or KQL.
7. Pivot across user, source IP, geography, action, and timestamp.
8. Inspect representative raw events instead of relying on counts alone.
9. Save useful searches for reuse in dashboards.

## KQL Fundamentals

Free-text search checks searchable fields for a term:

```kql
"<search term>"
```

A wildcard can match a partial term:

```kql
<term>*
```

Field-based searches are more precise:

```kql
Source_Country : "<country>"
```

Logical operators combine or exclude conditions:

```kql
Source_Country : "<country>" AND (UserName : "<user_1>" OR UserName : "<user_2>")
```

```kql
Source_Country : "<country>" AND NOT Source_State : "<state>"
```

Time and identity can be combined to investigate activity after a known event:

```kql
UserName : "<username>" AND @timestamp > "YYYY-MM-DDT00:00:00.000Z"
```

Failed connections can be isolated with an exact field filter:

```kql
action : "failed"
```

Parentheses are important when mixing `AND` and `OR`; they make the intended logic explicit.

## Visualizations and Correlation

Kibana Lens was used to turn filtered events into analyst-friendly views:

- A table for exact user, source IP, country, action, and event-count comparisons.
- A geographic/source-IP visualization to correlate where connections originated.
- A failed-connection view to identify the account with the greatest concentration of failures.
- A time-based visualization to identify spikes and determine which source contributed most.
- Saved Lens visualizations that could be reused in a dashboard.

A visualization highlights a pattern; the underlying events still need to be reviewed before assigning a verdict.

## Dashboard Workflow

Saved searches and Lens visualizations were combined into a custom VPN-visibility dashboard:

`Saved Searches + Visualizations -> Dashboard -> Monitoring -> Investigation in Discover`

Dashboards provide fast situational awareness, while Discover remains the main place to inspect individual events and test hypotheses.

## Hands-On Exercise (Sanitized)

I investigated a January VPN dataset and used Kibana to:

- Validate the event count for a defined time window.
- Identify the highest-volume source IP and user.
- Filter activity for a specific account and pivot to its most common source IP.
- Investigate a timeline spike and determine the responsible source.
- Apply positive and negative filters to include an IP while excluding a state.
- Use KQL to combine country and username conditions.
- Check for post-termination VPN activity associated with a former employee.
- Isolate failed connection attempts and identify the most-targeted account.
- Create and save tables, charts, and a custom dashboard.

Exact room answers, flags, and screenshots containing challenge solutions are intentionally excluded from this public portfolio.

## SOC Investigation Takeaways

- Always validate the data view and time range before concluding that logs are missing.
- Start broad, then add one filter at a time so the reason for each result remains clear.
- User, source IP, country/state, action, and timestamp are high-value pivot fields for VPN investigations.
- VPN access after an employee's termination is a strong anomaly that requires identity, asset, and authorization checks.
- Repeated failures may indicate user error or a stale client, but can also indicate password spraying or brute force.
- A spike is a lead, not a verdict; inspect the contributing events and surrounding context.
- Use tables for exact evidence and charts for patterns and trends.
- Dashboards support monitoring; raw-event review supports defensible conclusions.

## Skills Gained

- Explaining the Beats/Elastic Agent, Logstash, Elasticsearch, and Kibana data flow.
- Navigating Kibana Discover and selecting a data view.
- Applying absolute and relative time filters.
- Writing free-text and field-based KQL searches.
- Combining conditions with `AND`, `OR`, `NOT`, parentheses, and wildcards.
- Building tables and Lens visualizations.
- Correlating VPN users, IP addresses, geography, actions, and time.
- Creating and saving a custom Kibana dashboard.
- Investigating VPN anomalies with a structured SOC workflow.

## References

- [Elastic — The Elastic Stack](https://www.elastic.co/docs/get-started/the-stack)
- [Elastic — Discover](https://www.elastic.co/docs/explore-analyze/discover)
- [Elastic — Kibana Query Language](https://www.elastic.co/docs/reference/query-languages/kql)
- [Elastic — Lens](https://www.elastic.co/docs/explore-analyze/visualize/lens)
- [Elastic — Dashboards](https://www.elastic.co/docs/explore-analyze/dashboards)
