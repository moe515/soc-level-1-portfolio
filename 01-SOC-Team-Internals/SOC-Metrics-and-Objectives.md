# SOC Metrics and Objectives

**Path:** SOC Level 1 -> SOC Team Internals
**Room:** SOC Metrics and Objectives (https://tryhackme.com/room/socmetricsandobjectives) - 45 min - Premium
**Status:** Completed

## Objective

Understand the core metrics used to measure SOC effectiveness (MTTD, MTTA, MTTR, False Positive Rate) and how an L1 analyst can help improve them.

## Key Concepts

### 1. Core Metrics

The SOC's job is to protect confidentiality, integrity, and availability of the organization's digital assets. Four core metrics track how well that's happening:

| Metric | Formula | Measures |
|---|---|---|
| Alerts Count (AC) | Total alerts received | Overall load on SOC analysts (5-30/day per L1 is a healthy range) |
| False Positive Rate (FPR) | False Positives / Total Alerts | Noise level in alerts. 0% is unrealistic; 80%+ is a serious problem, usually fixed via detection rule tuning |
| Alert Escalation Rate (AER) | Escalated Alerts / Total Alerts | L1 experience/independence. Healthy target: below 50%, ideally below 20% |
| Threat Detection Rate (TDR) | Detected Threats / Total Threats | SOC reliability. Should always trend toward 100% - a missed threat can mean ransomware or data exfiltration |

### 2. SLA Timing Metrics
An SLA (Service Level Agreement) is a document between the SOC team and management, or between an MSSP and its customers, defining acceptable response times:

- **MTTD** (Mean Time to Detect): average time between the attack occurring and SOC tools detecting it
- **MTTA** (Mean Time to Acknowledge): average time for an L1 analyst to start triaging a new alert
- **MTTR** (Mean Time to Respond): average time for the SOC to actually stop the breach from spreading

Common reference targets: 24/7 SOC availability, MTTD ~5 min, MTTA ~10 min, MTTR ~60 min (varies by team/SLA).

### 3. Improving the Metrics

| Issue | Typical Fix |
|---|---|
| High False Positive Rate | Exclude trusted/expected activity from detection rules; automate triage of common alerts via SOAR or scripts |
| High MTTD | Push SOC engineers to tune detection rules to run faster; confirm SIEM logs are ingested in near real-time |
| High MTTA | Ensure real-time analyst notifications; distribute alert queue evenly across the shift |
| High MTTR | As L1, escalate promptly instead of sitting on unclear alerts; make sure response playbooks exist for common attack scenarios |

### 4. Why This Matters as L1
Metrics aren't just for management reporting - they're also how L1 performance gets evaluated, and directly tie into career growth toward L2. Practiced applying this by working through realistic MDR/SOC scenarios (an unhappy customer complaining about slow response, a delayed alert investigation, and analyst burnout from alert noise) and mapping each to the metric it affects and a concrete fix.

## Skills Gained

- Reading and calculating core SOC metrics (AC, FPR, AER, TDR)
- Understanding SLA timing metrics (MTTD, MTTA, MTTR) and how they interact
- Diagnosing which metric is failing from a real-world scenario and proposing the right fix
- Connecting day-to-day L1 habits (fast acknowledgement, correct escalation) to team-level KPIs

## Personal Notes

Good bridge between "doing the work" (triage, reporting) and "proving the work matters" (metrics). Worth tracking my own version of these informally even in practice labs - noticing my own false positive rate and response time is a habit that transfers directly to the job.
