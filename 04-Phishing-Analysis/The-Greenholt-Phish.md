# The Greenholt Phish

> **Public portfolio note:** This write-up documents the investigation methodology, analyst reasoning, and defensive lessons. Direct room answers, flags, exact hashes, attachment names, and live indicators are intentionally excluded.

## Room Summary

This investigation follows a suspicious email reported by a sales executive at Greenholt PLC. The message used a financial-transfer theme, a generic greeting, an unexpected attachment, and sender details that did not match the customer's normal communication pattern.

The objective was to perform a complete phishing triage workflow: preserve the evidence, inspect the email body and headers, trace the delivery path, validate sender authentication, enrich the infrastructure, identify the true attachment type, assess its reputation, and produce a defensible verdict with response actions.

## Scenario and Investigation Objectives

The reported email contained several contextual warning signs:

- An unexpected request connected to a financial transfer.
- A generic greeting instead of the recipient's name.
- An unsolicited archive-like attachment.
- A display name designed to resemble a trusted business contact.
- A sender identity inconsistent with previous customer communications.

The investigation aimed to answer four questions:

1. Who actually sent the message?
2. Did the sending infrastructure have permission to represent the claimed domain?
3. What was the attachment's real file type and reputation?
4. What containment and hunting actions should the SOC take?

## Investigation Methodology

### 1. Initial Message Review

I began with the visible content rather than immediately opening the attachment. The financial language and reference number created urgency and gave the recipient a plausible business reason to interact with the file. That combination is common in invoice, payment, and SWIFT-themed phishing campaigns.

The visible display name was treated only as user-interface text. It is not a reliable identity control because an attacker can choose almost any display name. The envelope and header identities were therefore examined separately.

### 2. Header and Identity Analysis

The **From** and **Reply-To** fields pointed to different identities. This matters because a victim may believe they are replying to the visible sender while the response is silently redirected elsewhere.

The following fields were reviewed together:

- **From:** the identity shown to the recipient.
- **Reply-To:** the destination used when the recipient replies.
- **Return-Path:** the address used for delivery status and bounce processing.
- **Message-ID:** a useful clue about the system that generated the message.
- **Received:** the mail-server path used to reconstruct delivery.
- **Authentication-Results:** the receiving platform's SPF, DKIM, and DMARC evaluation.

No single field was treated as conclusive. The verdict was based on the combined identity, routing, authentication, and attachment evidence.

### 3. Routing and Infrastructure Analysis

The **Received** chain was read from the bottom upward because mail servers prepend their own entries. The earliest trustworthy external hop was used as the best available indicator of the originating infrastructure.

WHOIS/RDAP enrichment showed that the infrastructure belonged to a general hosting provider. Provider ownership alone does not prove malicious intent; shared hosting and virtual private servers serve both legitimate and abusive activity. In this case, the infrastructure became meaningful only when correlated with the sender mismatch, failed authentication, and malicious attachment.

### 4. Sender Authentication Analysis

#### SPF

The claimed domain published an SPF policy that authorized Microsoft 365 infrastructure and ended with a hard-fail directive. The observed sending source was outside that authorized path, creating strong evidence that the message was not sent by an approved server for the claimed domain.

SPF was interpreted carefully because it validates the SMTP envelope identity and can be affected by forwarding. It was not used as a standalone verdict.

#### DKIM

No trustworthy DKIM result was available to provide a cryptographic link between the message and the claimed signing domain. The absence of a valid aligned signature reduced confidence in the sender's authenticity.

#### DMARC

The message did not present a trustworthy DMARC success result. A live DNS review showed an enforcement-oriented policy for the domain, but current DNS cannot prove what the policy was at the historical time of delivery. For that reason, the live record was used as supporting context rather than treated as historical evidence.

The strongest authentication conclusion came from the alignment failure between the claimed identity and the actual sending path.

### 5. Attachment Analysis

The attachment name attempted to look like a business document while using multiple extensions and misleading visual cues. File extensions were not trusted.

Safe analysis focused on metadata and content identification:

- The MIME type was generic binary data.
- The transfer encoding was Base64, which is an email transport encoding—not encryption.
- Magic-byte and file-signature analysis identified an archive format that did not match the filename's apparent document type.
- The SHA-256 digest was searched in a multi-engine reputation service.
- A high proportion of security engines classified the sample as malicious.

The exact filename, digest, size, and detection count are excluded from this public report to avoid publishing room answers or operational indicators.

## Sanitized Evidence Matrix

| Finding | Observation | Security significance |
|---|---|---|
| Social-engineering lure | Unexpected financial-transfer request with urgency | Encourages rapid action before verification |
| Sender identity mismatch | Visible sender and reply destination did not align | Strong impersonation and response-redirection indicator |
| Suspicious routing | Earliest trustworthy hop did not match the expected mail path | Supports spoofing or unauthorized delivery infrastructure |
| SPF failure | Sending source was outside the domain's authorized infrastructure | Strong evidence against sender authenticity |
| Missing trustworthy alignment | No reliable aligned DKIM/DMARC success | Removes an important authenticity control |
| Filename masquerading | Attachment appeared document-like but used misleading extensions | Designed to lower user suspicion |
| File-type mismatch | Content signature identified an archive rather than the apparent type | Confirms deliberate concealment or unsafe packaging |
| Malicious reputation | Multi-engine reputation produced high-confidence malicious detections | Strong technical evidence of weaponization |

## Analyst Verdict

**True Positive — Malicious Phishing Email**

The email combined a financial pretext, identity inconsistencies, unauthorized sending infrastructure, authentication failure, deceptive attachment naming, a file-type mismatch, and high-confidence malicious reputation. These independent signals reinforce one another and support escalation without relying on any single indicator.

## Recommended SOC Response

1. Quarantine the message across all mailboxes.
2. Search the mail platform for the sender identities, subject pattern, attachment name, and file digest.
3. Identify every recipient and determine who opened the message, clicked a link, replied, downloaded the file, or attempted execution.
4. Hunt endpoint telemetry for the attachment digest, archive extraction, child processes, command interpreters, persistence, and outbound connections.
5. Isolate affected endpoints if execution or suspicious follow-on behavior is confirmed.
6. Block validated malicious indicators at email, web proxy, DNS, firewall, and endpoint controls where appropriate.
7. Preserve the original email, headers, attachment, and timeline for forensic investigation.
8. Escalate confirmed execution, credential exposure, or lateral movement to L2/Incident Response.
9. Notify the legitimate business contact through a trusted out-of-band channel if impersonation is involved.

## Detection and Hunting Opportunities

- Alert on **From/Reply-To** domain mismatches for external messages, while allowing documented business exceptions.
- Flag SPF hard failures combined with financial keywords or archive attachments.
- Detect double-extension and right-to-left or whitespace filename deception.
- Inspect archive and generic binary attachments using file signatures, not extensions alone.
- Correlate email delivery with endpoint file creation and process execution.
- Hunt for the same digest, sender, subject, and attachment naming pattern across the environment.
- Prioritize authentication failures when the message also contains payment, invoice, or transfer language.

## MITRE ATT&CK Mapping

| Technique | ID | Relevance |
|---|---|---|
| Spearphishing Attachment | [T1566.001](https://attack.mitre.org/techniques/T1566/001/) | The adversary delivered a malicious attachment through email |
| User Execution: Malicious File | [T1204.002](https://attack.mitre.org/techniques/T1204/002/) | The campaign depended on the recipient opening the disguised file |
| Masquerading | [T1036](https://attack.mitre.org/techniques/T1036/) | The attachment name and apparent type were designed to look legitimate |

## Skills Demonstrated

- Phishing triage and evidence preservation.
- Email-header parsing and delivery-path reconstruction.
- SPF, DKIM, and DMARC interpretation.
- WHOIS/RDAP infrastructure enrichment.
- MIME and Base64 analysis.
- Magic-byte and true file-type identification.
- Hash-based reputation analysis with VirusTotal.
- Evidence correlation, verdict writing, containment, and threat hunting.

## Key Takeaways

- A display name is not proof of identity.
- Read **Received** headers bottom-up and trust only the hops added by known mail infrastructure.
- SPF, DKIM, and DMARC are related controls, but they answer different questions.
- Live DNS is supporting context, not definitive proof of a historical policy.
- Base64 is encoding, not encryption.
- File extensions can be manipulated; content signatures reveal the real type.
- Provider ownership is context, not guilt. Correlation turns context into evidence.
- A strong verdict explains how multiple independent findings support the conclusion.

## Completion Evidence

- **Platform:** TryHackMe
- **Room:** The Greenholt Phish
- **Module:** Phishing Analysis
- **Completed:** October 3, 2026
- **Status:** 1/1 task completed

## References

- [TryHackMe — The Greenholt Phish](https://tryhackme.com/room/phishingemails5fgjlzxc)
- [RFC 5322 — Internet Message Format](https://www.rfc-editor.org/rfc/rfc5322)
- [RFC 7208 — Sender Policy Framework](https://www.rfc-editor.org/rfc/rfc7208)
- [RFC 7489 — Domain-based Message Authentication, Reporting, and Conformance](https://www.rfc-editor.org/rfc/rfc7489)
- [CISA — Recognize and Report Phishing](https://www.cisa.gov/secure-our-world/recognize-and-report-phishing)
- [VirusTotal Documentation](https://docs.virustotal.com/)
- [ARIN — Registration Data Access Protocol](https://www.arin.net/resources/registry/whois/rdap/)
