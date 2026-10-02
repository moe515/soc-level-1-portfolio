# Phishing Analysis Tools

## Room Summary

This room develops a repeatable tool-assisted workflow for investigating suspicious emails. It covers artifact collection, header and body analysis, URL and IP reputation checks, safe attachment handling, phishing-report automation, and dynamic malware analysis in an isolated sandbox.

The practical exercises connect email evidence with endpoint and network behavior. The workflow progresses from validating message metadata to examining document-based delivery chains and identifying exploitation, payload retrieval, and malicious infrastructure.

This public write-up documents methodology and defensive lessons only. Direct room answers, flags, file hashes, and live malicious indicators are intentionally excluded.

## Objectives

- Identify the artifacts that should be preserved from a suspicious email.
- Analyze routing, authentication, sender, and recipient information in email headers.
- Extract and assess URLs without browsing to them from a production endpoint.
- Hash and investigate attachments using reputation services.
- Use sandbox reports to reconstruct process and network behavior.
- Separate suspicious evidence from confirmed malicious activity.
- Document and contain phishing incidents using a repeatable SOC workflow.

## Evidence Collection

A phishing investigation should begin by preserving the original message and recording enough context to reproduce the analysis.

| Evidence category | Examples | Why it matters |
|---|---|---|
| Message metadata | Sender, recipients, subject, timestamp, Message-ID | Establishes identity, targeting, and timeline |
| Routing data | Received chain, originating IP, mail servers | Helps reconstruct delivery and identify infrastructure |
| Authentication | SPF, DKIM, DMARC results | Tests authorization and alignment, but does not prove benign intent |
| Body artifacts | Visible text, HTML, hyperlinks, forms, remote images | Reveals social engineering, hidden destinations, and tracking |
| Attachments | Name, type, size, hashes, metadata | Supports reputation checks and controlled analysis |
| User context | Expected message, clicks, downloads, execution, credential entry | Determines exposure and response priority |

## Tool-Assisted Analysis Workflow

### Header Analysis

Header-parsing tools can convert a long raw header into a readable delivery timeline, but the analyst must still interpret the results. Important checks include:

- Compare `From`, `Reply-To`, and `Return-Path`.
- Read `Received` headers from the bottom upward to reconstruct the route.
- Identify the earliest trustworthy external hop and originating IP.
- Review SPF, DKIM, and DMARC results and their alignment with the visible sender.
- Enrich public IP addresses using reputation and ownership data.

Authentication success is not a clean verdict by itself. An attacker can correctly authenticate a domain that they own while still impersonating another organization.

### Email Body and URL Analysis

The visible text of a button or hyperlink may not match its real destination. A safe workflow is to extract the URL from HTML or source data, defang it, and investigate it with approved reputation and scanning services.

Recommended checks:

1. Compare visible anchor text with the underlying `href`.
2. Identify shorteners and record each redirect.
3. Determine the final registrable domain.
4. Check domain age, ownership, reputation, and observed behavior.
5. Review remote images and tracking resources.
6. Never submit real credentials to a suspected landing page.

### Attachment Analysis

Attachments should be hashed and inspected statically before dynamic execution. The extension, MIME type, magic bytes, metadata, embedded links, and external relationships should agree with the claimed document type.

Useful steps include:

- Calculate SHA-256 locally.
- Search the hash in an approved threat-intelligence service.
- Extract URLs and embedded objects without enabling active content.
- Review archive contents in a controlled environment.
- Detonate the sample only when authorized and isolated.

One byte of modification changes a cryptographic hash, so a clean or unknown hash result does not establish safety.

## Malware Sandbox Analysis

A sandbox provides behavioral evidence that static inspection may miss. During analysis, the most valuable evidence is the relationship between the original document, child processes, created files, and network destinations.

### Process Activity

Review:

- The application that opened the attachment.
- Parent-child process relationships.
- Command-line arguments.
- Office applications spawning shells, script interpreters, LOLBins, or unknown executables.
- Persistence, process injection, and security-control tampering.

### Network Activity

Review:

- DNS queries and resolved IP addresses.
- HTTP/S requests and redirect chains.
- Payload downloads and unusual file types.
- Connections to newly observed or malicious infrastructure.
- Repeated beacon-like traffic.

### Verdict Interpretation

`Suspicious activity` indicates behavior that requires further investigation but may not independently prove compromise. `Malicious activity` indicates that the sandbox observed stronger evidence such as exploitation, payload execution, malicious communications, or multiple correlated detections.

## Document-Based Phishing Patterns

### PDF Lure

A PDF may imitate a trusted service and contain a link that leads to credential or payment theft. The document should be inspected for embedded URLs, actions, scripts, and external resources without following them from a normal workstation.

### Weaponized Spreadsheet

An Excel attachment can be used as an initial-access mechanism rather than a normal business document. In the investigated pattern, opening the spreadsheet led to behavior consistent with exploitation and communication with malicious infrastructure.

The defensive lesson is to correlate:

```text
Email attachment → Office process → Child process or exploit behavior → DNS/HTTP activity → Payload or command execution
```

Legacy Office vulnerabilities remain relevant when organizations use unsupported or unpatched software. Endpoint controls should monitor unusual Office child processes and prevent automatic external-content retrieval.

## MITRE ATT&CK Mapping

| Technique | Relevance |
|---|---|
| [T1566.001 — Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/) | Malicious documents delivered by email |
| [T1566.002 — Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/) | Hidden, shortened, or redirected links |
| [T1204.001 — Malicious Link](https://attack.mitre.org/techniques/T1204/001/) | User interaction with a phishing URL |
| [T1204.002 — Malicious File](https://attack.mitre.org/techniques/T1204/002/) | User opens a weaponized attachment |
| [T1203 — Exploitation for Client Execution](https://attack.mitre.org/techniques/T1203/) | A crafted document exploits a client application |
| [T1105 — Ingress Tool Transfer](https://attack.mitre.org/techniques/T1105/) | Follow-on payload retrieval from external infrastructure |

## SOC Investigation Workflow

1. Preserve the original email and collect the raw source.
2. Record sender, recipients, subject, time, Message-ID, links, and attachment names.
3. Validate sender identity and authentication results.
4. Extract and defang URLs without visiting them directly.
5. Calculate attachment hashes and perform static analysis.
6. Enrich IPs, domains, URLs, and hashes with approved intelligence sources.
7. Use a sandbox when authorized and necessary.
8. Reconstruct the process tree and network timeline.
9. Search email, DNS, proxy, firewall, identity, and endpoint telemetry for related activity.
10. Determine whether users clicked, downloaded, executed, or entered credentials.
11. Contain confirmed exposure and block validated indicators.
12. Document evidence, scope, verdict, impact, and escalation rationale.

## Detection and Response Opportunities

- Detect Office applications spawning shells, interpreters, or suspicious utilities.
- Alert on attachments followed by newly observed domain or IP connections.
- Correlate email delivery with DNS, proxy, and endpoint events.
- Block automatic external content for untrusted documents.
- Apply attachment sandboxing and content disarm and reconstruction where appropriate.
- Patch supported Office installations and remove unsupported versions.
- Hunt for the same sender, subject, URL, domain, IP, and attachment hash across the environment.
- Reset credentials and revoke sessions when credential submission is suspected.
- Isolate endpoints when execution or payload retrieval is confirmed.

## Skills Gained

- Collected and normalized email investigation artifacts.
- Parsed message headers and reconstructed delivery paths.
- Interpreted SPF, DKIM, and DMARC without over-trusting authentication success.
- Extracted and defanged hidden URLs.
- Performed IP, domain, URL, and file-hash enrichment.
- Used PhishTool to structure email analysis.
- Interpreted sandbox process, file, registry, and network evidence.
- Distinguished suspicious behavior from confirmed malicious activity.
- Identified document-based exploitation and payload-delivery patterns.
- Mapped findings to MITRE ATT&CK and response actions.

## Key Takeaways

- Email analysis is strongest when message, identity, endpoint, and network evidence are correlated.
- A professional-looking brand and valid email authentication do not guarantee legitimacy.
- URLs and attachments should be extracted and analyzed without direct interaction.
- Hash reputation is useful but brittle; behavioral evidence provides stronger context.
- Process ancestry often explains what a malicious document actually did.
- The most important scoping question is whether any user interacted and what happened afterward.

## Completion Evidence

- **Platform:** TryHackMe
- **Room:** Phishing Analysis Tools
- **Completed:** October 1, 2026
- **Module:** Phishing Analysis
- **Tasks completed:** 10/10

## References

- [TryHackMe — Phishing Analysis Tools](https://tryhackme.com/room/phishingemails3tryoe)
- [CISA — Phishing Guidance: Stopping the Attack Cycle at Phase One](https://www.cisa.gov/sites/default/files/2025-03/Phishing%20Guidance%20-%20Stopping%20the%20Attack%20Cycle%20at%20Phase%20One%20508.pdf)
- [MITRE ATT&CK — Phishing](https://attack.mitre.org/techniques/T1566/)
- [MITRE ATT&CK — User Execution](https://attack.mitre.org/techniques/T1204/)
- [MITRE ATT&CK — Exploitation for Client Execution](https://attack.mitre.org/techniques/T1203/)
- [NIST NVD — CVE-2017-11882](https://nvd.nist.gov/vuln/detail/CVE-2017-11882)
- [Google Admin Toolbox — Messageheader](https://toolbox.googleapps.com/apps/messageheader/)
- [urlscan.io — Documentation](https://urlscan.io/docs/)
- [VirusTotal — Documentation](https://docs.virustotal.com/docs/)
- [ANY.RUN — Interactive Malware Sandbox](https://any.run/)
- [PhishTool — Email Analysis Platform](https://www.phishtool.com/)
