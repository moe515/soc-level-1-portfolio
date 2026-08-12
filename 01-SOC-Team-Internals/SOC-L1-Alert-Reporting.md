# SOC L1 Alert Reporting

**Path:** SOC Level 1 -> SOC Team Internals
**Room:** SOC L1 Alert Reporting (https://tryhackme.com/room/socl1alertreporting) - ~60 min - Entry level
**Status:** Completed

## Objective

Learn how to properly report, escalate, and communicate about high-risk SOC alerts once triage is done.

## Key Concepts

### 1. The Alert Funnel
Alerts move through a filtering funnel as they get more serious: L1 analysts handle the raw volume of SIEM alerts, filtering noise and escalating real threats. L2 analysts receive only the true positives, doing deeper investigation and remediation. If something major is confirmed, it becomes a DFIR (Digital Forensics and Incident Response) case. The ultimate goal at every stage is protecting the organization's data.

- Alert Reporting: formally documenting alert details and findings
- Alert Escalation: the process of passing a validated threat to L2 for deeper review
- Communication: coordinating with other teams (IT, HR, legal, management) during or after an investigation

### 2. Why Write Alert Reports
- Provides context for escalation, saves L2 analysts time re-investigating from scratch
- Preserves findings for the record (raw SIEM logs are often only kept 3-12 months, but alerts and their reports are kept indefinitely)
- Writing a clear report is also a skill-builder: if you can't explain a finding simply, you don't understand it well enough yet

### 3. The Five Ws Report Format
A good alert report answers:
- Who: the user/account involved
- What: the exact action or event sequence
- When: when the suspicious activity started and ended
- Where: which device, IP, or site was involved
- Why: the reasoning behind the final verdict (the most important W)

### 4. When to Escalate
Escalate an alert to L2 if:
1. It indicates a major cyberattack requiring deeper investigation or DFIR
2. Remediation actions are needed (malware removal, host isolation, password reset)
3. Communication with customers, partners, management, or law enforcement is required
4. You're unsure and need senior support - it's better to ask than to misclassify

Escalation is usually as simple as reassigning the alert to the L2 on shift and notifying them, though some teams require a formal escalation request with more detail.

### 5. SOC Communication / Crisis Cases
- Urgent alert with no L2 response: escalate up the chain (L2, then L3, then manager)
- Suspected account compromise: verify with the affected user through a different channel than the one that may be compromised, never through the same medium
- Overload of alerts at once: keep following the standard prioritization workflow, but let your L2 know you're overwhelmed
- Realizing late that an alert was misclassified: report it immediately, threat actors can stay quiet for weeks before acting
- Logs not parsing or searchable: don't skip the alert, investigate what's available and flag the tooling issue to L2 or the SOC engineer

## Skills Gained

- Writing structured, useful alert reports using the Five Ws
- Knowing when (and how) to escalate vs. handle at L1
- Handling real-world SOC communication scenarios, including crisis situations
- Understanding how alerts flow from L1 through L2 to a full incident response

## Personal Notes

Builds directly on the Alert Triage room. The Five Ws format is genuinely reusable for every future alert, not just for this room.
