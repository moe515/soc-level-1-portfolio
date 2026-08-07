# SOC L1 Alert Triage

**Path:** SOC Level 1 -> SOC Team Internals
**Room:** SOC L1 Alert Triage (https://tryhackme.com/room/soclalerttriage) - ~60 min - Entry level
**Status:** Completed

## Objective

Understand how a SOC alert is generated and how to triage it systematically, from raw event to final verdict, using a SOC dashboard (SIEM-style alert board).

## Key Concepts

### 1. From Event to Alert
- An event is any logged activity (login, process launch, file download).
- All systems ship logs to a security solution (SIEM/EDR).
- An alert is a notification generated when a specific event or pattern matches a detection rule, this is what saves analysts from manually reviewing millions of raw logs.

### 2. Alert Management Platforms

| Solution | Examples | Purpose |
|---|---|---|
| SIEM | Splunk ES, Elastic | Central alert management, best fit for most SOC teams |
| EDR/NDR | MS Defender, CrowdStrike | Endpoint/network detection, own dashboards |
| SOAR | Splunk SOAR, Cortex SOAR | Aggregates and centralizes alerts from multiple sources |
| ITSM | Jira, TheHive | Custom ticketing for alert/case tracking |

### 3. L1 Analyst Role in Triage
- L1 analysts: first line of defense, review alerts, separate false positives from real threats, escalate to L2 if needed.
- L2 analysts: receive escalations, do deeper analysis and remediation.
- SOC engineers: make sure alerts carry enough context for efficient triage.
- SOC manager: tracks triage speed/quality to avoid missed attacks.

### 4. Alert Properties
Every alert generally carries these fields:

| Property | Meaning |
|---|---|
| Alert Time | When the alert fired (usually a few minutes after the actual event) |
| Alert Name | Summary based on the detection rule |
| Severity | Low / Medium / High / Critical, set by detection engineers, adjustable by analysts |
| Status | New to In Progress to Closed/Resolved |
| Verdict | True Positive (real threat) vs. False Positive (noise) |
| Assignee | Analyst responsible for the alert |
| Description | Explains the detection logic and why it may indicate an attack |
| Fields | Supporting details (hostname, command, user, etc.) used to make the call |

### 5. Alert Prioritization Strategy
When facing a queue of alerts, the standard approach is:
1. Filter, only pick new/unseen alerts, skip ones already assigned or in progress.
2. Sort by severity, critical first, then high, medium, low.
3. Sort by time, oldest first (an older breach likely has more damage/data already exfiltrated).

## Skills Gained

- Reading and interpreting SOC dashboard alerts end-to-end
- Applying a repeatable alert prioritization framework
- Distinguishing true positives from false positives using alert context
- Familiarity with SIEM/EDR/SOAR/ITSM ecosystem and where each fits

## Personal Notes

First hands-on room simulating a live SOC alert queue. Good introduction to the triage mindset, treat every alert methodically instead of jumping between them randomly.
