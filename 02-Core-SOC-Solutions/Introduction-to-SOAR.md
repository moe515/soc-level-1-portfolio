# Introduction to SOAR

**Path:** SOC Level 1 → Core SOC Solutions  
**Platform:** TryHackMe  
**Status:** Completed

## Objective

Understand how Security Orchestration, Automation, and Response (SOAR) helps a Security Operations Center connect tools, automate repetitive work, and execute consistent incident-response workflows while retaining analyst judgment for ambiguous or high-impact decisions.

## Why SOC Teams Use SOAR

Traditional SOC teams often face four operational problems:

| Challenge | Operational impact |
|---|---|
| Alert fatigue | Analysts spend time sorting high volumes of low-value or duplicate alerts |
| Disconnected tools | Evidence is scattered across SIEM, EDR, threat-intelligence, firewall, IAM, and ticketing platforms |
| Manual processes | Repetitive enrichment and documentation slow investigations |
| Talent shortage | Skilled analysts become overloaded with routine work instead of complex analysis |

SOAR addresses these problems through three connected capabilities:

| Capability | Purpose |
|---|---|
| Orchestration | Connects security tools and coordinates data or actions between them |
| Automation | Performs repeatable tasks such as enrichment, searches, ticket updates, and notifications |
| Response | Executes approved containment or remediation actions such as blocking an indicator or disabling an account |

## Typical SOAR Workflow

1. Receive an alert from the SIEM.
2. Extract users, hosts, IP addresses, domains, URLs, or file hashes.
3. Enrich the indicators through threat-intelligence services.
4. Search connected tools for related activity.
5. Calculate or update severity based on the collected context.
6. Create or update the investigation ticket.
7. Request analyst approval when an action is ambiguous or high impact.
8. Execute the approved response and record every action.

## Playbooks

A playbook is a predefined workflow containing triggers, actions, decision points, approvals, and documentation steps. It makes investigations repeatable while preserving human review where context matters.

### Phishing Investigation Pattern

A phishing playbook can:

- Create a case when a suspicious email is reported.
- Extract URLs and attachment hashes.
- Enrich indicators with reputation services.
- Search the environment for other recipients or clicks.
- Route uncertain artifacts for sandbox or analyst review.
- Quarantine malicious messages and update the case with findings.

Sensitive internal artifacts should only be submitted to external analysis services when organizational data-handling policy allows it.

### Vulnerability and Patch Pattern

A patching playbook can:

- Ingest vulnerability advisories.
- Identify affected assets and versions.
- Prioritize risk using severity, exposure, and exploitability.
- Create remediation tickets.
- Coordinate staging, approval, deployment, and verification.
- Open a mitigation path when a patch is unavailable or remediation fails.

## Automation Decision Matrix

| Suitable for automation | Keep analyst-driven |
|---|---|
| IOC extraction and normalization | Ambiguous true-positive / false-positive verdicts |
| Reputation and enrichment API calls | Business-impact assessment |
| Repeatable SIEM or EDR searches | High-impact containment approval |
| Ticket creation and evidence updates | Complex investigation and exception handling |
| Notifications and pre-approved actions | Playbook design, tuning, and governance |

The practical rule is simple: automate deterministic evidence gathering and retain human judgment for ambiguous or high-impact decisions.

## Hands-On Exercise — Sanitized Summary

Built and validated a threat-intelligence integration workflow by classifying workflow activities as automated or analyst-driven. The exercise reinforced the importance of orchestration between security tools, consistent playbook logic, approval gates, and complete case documentation.

No room answers or flags are included in this public write-up.

## SOC Analyst Takeaways

- SOAR improves analyst consistency; it does not remove the need for analysts.
- Automation quality depends on clear inputs, safe conditions, tested actions, and auditability.
- Containment actions should include approval gates when they may disrupt legitimate business activity.
- Analysts remain responsible for context, judgment, exception handling, and playbook improvement.
- A mature workflow records evidence, decisions, actions, owners, and timestamps in the case.

## Skills Gained

- Explaining orchestration, automation, and response in a SOC context
- Mapping integrations across SIEM, EDR, threat intelligence, firewall, IAM, and ticketing
- Designing phishing and vulnerability-management playbooks
- Distinguishing deterministic automation from analyst decision points
- Applying human approval gates to high-impact response actions
- Documenting repeatable threat-intelligence enrichment workflows

## References

- [Microsoft Sentinel — Automation in Microsoft Sentinel](https://learn.microsoft.com/en-us/azure/sentinel/automation/automation)
- [Microsoft Sentinel — Automate threat response with playbooks](https://learn.microsoft.com/en-us/azure/sentinel/automation/automate-responses-with-playbooks)
- [NIST SP 800-40 Rev. 4 — Enterprise Patch Management Planning](https://csrc.nist.gov/pubs/sp/800/40/r4/final)
- [CISA — Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
