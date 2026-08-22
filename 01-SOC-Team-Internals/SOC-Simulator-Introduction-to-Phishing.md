# SOC Simulator: Introduction to Phishing

**Platform:** TryHackMe SOC Simulator  
**Path:** SOC Level 1 → SOC Team Internals  
**Completed:** August 22, 2026  
**Environment:** Simulated training lab

## Overview

I completed my first live-style SOC simulation focused on phishing alert triage. The scenario required me to investigate an alert queue, correlate email and firewall telemetry in Splunk, classify each alert, decide whether escalation was necessary, and document the evidence supporting each decision.

This write-up focuses on my investigation process and lessons learned. It does not include room answers, flags, or lab-specific indicators.

## Results

| Metric | Result |
|---|---:|
| Alerts closed | 4 |
| True positive alerts | 3 |
| False positive alerts | 1 |
| True positive identification rate | 100% |
| False positive identification rate | 100% |
| Mean time to resolve | 18 minutes |
| Mean dwell time | 76 minutes |

## Investigation Workflow

### 1. Review the alert

For each alert, I identified:

- Alert rule, type, and severity
- Sender and recipient
- Subject and message content
- URLs, domains, IP addresses, and attachments
- Timestamp and direction of activity

### 2. Build an initial hypothesis

I looked for common phishing indicators such as:

- Typosquatted sender domains
- Shortened or mismatched URLs
- Generic greetings
- Urgent or threatening language
- Requests to sign in or provide sensitive information
- Unexpected attachments or business requests

I treated these as indicators requiring investigation, not automatic proof of phishing.

### 3. Search and correlate logs in Splunk

I used targeted searches to validate whether endpoints attempted to access suspicious URLs and whether the connections were allowed or blocked.

Example searches:

```spl
datasource=firewall "<domain>"
| table timestamp SourceIP DestinationIP DestinationPort Action URL Rule
```

```spl
datasource=firewall SourceIP="<source-ip>"
| sort 0 timestamp
| table timestamp SourceIP DestinationIP Action URL Rule
```

```spl
datasource=firewall DestinationIP="<destination-ip>"
| table timestamp SourceIP DestinationIP Action URL Rule
```

```spl
datasource=email "<sender-domain>"
| table timestamp sender recipient subject
```

These searches helped me correlate phishing emails with firewall activity, identify affected endpoints, and scope whether other users or systems contacted the same destination.

### 4. Classify and document

I classified alerts using the evidence collected:

- **False positive:** Business context confirmed that the email was legitimate.
- **True positive, contained:** A malicious connection was attempted but blocked before completion.
- **True positive, escalation required:** A connection to a credential-phishing page was allowed, creating possible exposure that required additional investigation.

## Key Lessons Learned

### Classification and escalation are separate decisions

A true positive does not always require escalation. If a malicious request is blocked and there is no evidence of impact, the alert can be classified as a true positive while documenting that the control contained the activity.

Escalation becomes appropriate when the connection is allowed, impact is observed, or additional investigation is required to determine whether credentials, data, or systems were compromised.

### Blocked does not mean false positive

A blocked connection still confirms that the detection identified real malicious activity. The correct wording is:

> The endpoint attempted to connect to the malicious URL, but the firewall blocked the request before the connection completed. No impact was observed.

### Allowed does not automatically mean compromised

An allowed firewall connection confirms exposure to the destination, but it does not prove that credentials were submitted or malware was executed. Reports should clearly separate what was observed from what remains possible.

### Business context matters

An external link is not automatically malicious. Internal correspondence and expected business activity helped validate one legitimate onboarding email and prevented an unnecessary incident response.

### Precise reporting improves decisions

A strong case report should include:

- **Who:** User, sender, recipient, and affected endpoint
- **What:** Observed activity and alert type
- **When:** Raw event timestamp with timezone
- **Where:** Mailbox, endpoint, source IP, and destination
- **Why:** Evidence supporting the classification
- **Impact:** What occurred, what did not occur, and what remains unknown

## Skills Demonstrated

- Phishing email analysis
- Splunk Search Processing Language (SPL)
- Email and firewall log correlation
- IOC extraction and scoping
- True-positive and false-positive classification
- Impact assessment and escalation decisions
- Evidence-based incident reporting
- SOC metrics: MTTR, dwell time, and identification rates

## Reflection

This was my first complete SOC Simulator scenario. I correctly classified all four alerts and achieved 100% true-positive and false-positive identification rates.

The report feedback highlighted two areas for improvement: avoiding unnecessary escalation when controls fully contain an event, and writing more precise statements about timestamps and observed impact. My next goal is to keep reports concise while clearly separating confirmed evidence, possible risk, and recommended action.

## References

- [TryHackMe SOC Simulator](https://tryhackme.com/soc-sim)
- [NIST SP 800-61 Rev. 3 — Incident Response Recommendations and Considerations](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [CISA — Recognize and Report Phishing](https://www.cisa.gov/secure-our-world/recognize-and-report-phishing)
- [Splunk Search Manual](https://help.splunk.com/en/splunk-enterprise/search/search-manual/9.4)
