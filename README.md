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
