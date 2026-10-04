# Phishing Prevention

## Room Summary

This room examines phishing prevention as a layered security problem rather than a single-control problem. It covers sender-authentication standards, encrypted and signed email, SMTP and IMF traffic analysis, attachment inspection, and the technical and human controls organizations use to reduce phishing risk.

The practical work connected protocol-level evidence with SOC decisions: interpreting mail-server response codes, validating message authentication, identifying risky attachment types and encodings, and selecting safe analysis methods. This public write-up documents defensive methodology and learning outcomes only; direct room answers, flags, and live malicious indicators are intentionally excluded.

## Objectives

- Explain what SPF, DKIM, DMARC, and S/MIME protect—and what they do not.
- Interpret SMTP status codes and analyze email traffic in Wireshark.
- Inspect Internet Message Format (IMF) and MIME fields safely.
- Recognize dangerous attachment patterns and encoding methods.
- Evaluate technical, procedural, and user-facing anti-phishing controls.
- Build a repeatable SOC investigation and response workflow.

## Layered Email Security Model

| Layer | Primary purpose | Important limitation |
|---|---|---|
| SPF | Authorizes systems that may send mail for a domain | Does not validate the visible From address by itself |
| DKIM | Uses a cryptographic signature to verify selected message content and the signing domain | A valid signature does not prove the message is benign |
| DMARC | Requires alignment between the visible From domain and SPF or DKIM identity | Effectiveness depends on correct policy and enforcement |
| S/MIME | Provides message signing and optional end-to-end content encryption | Requires certificate lifecycle and key management |
| SEG / filtering | Detects reputation, impersonation, content, URL, and attachment risk | Some attacks still reach users |
| User reporting | Converts user observations into SOC telemetry | Depends on awareness and a responsive process |

## Sender Authentication

### SPF

Sender Policy Framework publishes a DNS TXT record describing which hosts are authorized to send mail for a domain. Common mechanisms include **ip4**, **ip6**, **a**, **mx**, and **include**. The record ends with an **all** qualifier that communicates the intended treatment of unmatched senders.

| Qualifier | Meaning |
|---|---|
| + | Pass |
| - | Fail |
| ~ | SoftFail |
| ? | Neutral |

Analyst considerations:

- Identify the domain used by the SMTP envelope sender or Return-Path.
- Confirm that the sending IP is covered by the published policy.
- Review nested include mechanisms and DNS-lookup complexity.
- Treat SPF success as authorization to send, not proof of trustworthy content.
- Remember that forwarding can break ordinary SPF evaluation.

### DKIM

DomainKeys Identified Mail adds a cryptographic signature to a message. The sending system signs selected headers and the body with a private key. The receiving system obtains the public key from DNS by using the signing domain and selector in the DKIM-Signature header.

Important fields include:

- **d=** — signing domain.
- **s=** — selector used to locate the DNS public key.
- **h=** — headers covered by the signature.
- **bh=** — body hash.
- **b=** — digital signature.

A missing, malformed, or inaccessible public key can produce a permanent evaluation error. DKIM detects unauthorized modification of the signed content, but it does not encrypt the message and does not guarantee that the signer is legitimate.

### DMARC

Domain-based Message Authentication, Reporting, and Conformance connects SPF and DKIM to the domain visible in the From header. DMARC passes when at least one aligned mechanism passes:

- SPF passes and its authenticated domain aligns with the visible From domain, or
- DKIM passes and the signing domain aligns with the visible From domain.

| Policy | Receiver action |
|---|---|
| p=none | Monitor and report |
| p=quarantine | Treat failing mail as suspicious, commonly placing it in spam or quarantine |
| p=reject | Reject failing mail |

Useful tags include **rua** for aggregate reports, **ruf** for failure reports, **pct** for the percentage of messages covered, and **fo** for failure-report options. Analysts should review alignment rather than relying only on the words SPF pass or DKIM pass.

## S/MIME

Secure/Multipurpose Internet Mail Extensions uses public-key cryptography and certificates to protect message content.

### Digital signing

1. The sender signs with the sender's private key.
2. The recipient verifies with the sender's public key.
3. This supports authenticity, integrity, and non-repudiation.

### Encryption

1. The sender encrypts for the recipient using the recipient's public key.
2. The recipient decrypts with the recipient's private key.
3. This provides confidentiality for message content.

S/MIME differs from DKIM because S/MIME can protect user-to-user content and identities, while DKIM is normally applied by a mail domain to authenticate message transit.

## SMTP Traffic Analysis

Simple Mail Transfer Protocol moves messages between clients and mail servers. A response code has three digits: the first indicates the general outcome, while the remaining digits provide more detail.

| Code class | Meaning |
|---|---|
| 2xx | Successful operation |
| 3xx | More information or another step is required |
| 4xx | Temporary failure; retry may succeed |
| 5xx | Permanent failure or policy rejection |

Examples encountered in defensive traffic analysis include service-ready responses, message-size or policy problems, and rejected mailbox actions. The text accompanying a code matters because different servers can use a code for specific local policies.

Useful Wireshark display filters:

- **smtp**
- **smtp.response.code**
- **smtp.response.code == CODE**

An analyst should correlate the response with the SMTP conversation, source and destination, timestamps, sender, recipient, and server text rather than judging the numeric code alone.

## IMF, MIME, and Attachment Analysis

Internet Message Format represents message headers and body content. MIME extends the format so email can carry multiple content types and attachments.

Useful Wireshark display filter: **imf**

Fields to review:

- From, To, Reply-To, Return-Path, and Subject.
- Message-ID and timestamps.
- Content-Type and boundary values.
- Content-Disposition and attachment filenames.
- Content-Transfer-Encoding.
- User-Agent or X-Mailer when available.

Base64 is an encoding scheme, not encryption. It makes binary content safe for transport through text-oriented systems but provides no confidentiality. A suspicious attachment should be decoded and analyzed only in an approved isolated environment.

Executable-looking extensions—including screen-saver files—should be treated as programs, not ordinary documents. Analysts should compare the filename, extension, MIME type, and magic bytes because attackers can use double extensions or misleading content types.

## Organizational Anti-Phishing Controls

### Technical defenses

- **Email filtering:** Uses IP/domain reputation, content rules, and policy checks to block or quarantine suspicious messages.
- **Secure Email Gateway (SEG):** Detects spoofing, impersonation, malicious content, and other attacks that basic filtering may miss.
- **Link rewriting:** Replaces links with controlled redirect URLs so destinations can be inspected at click time.
- **Sandboxing:** Executes suspicious files or opens links in an isolated environment to observe behavior without exposing production systems.
- **Attachment controls:** Blocks prohibited types, detonates unknown files, and may use content disarm and reconstruction.
- **Endpoint and web controls:** Detect follow-on execution, block malicious destinations, and contain affected systems.

### User-facing controls

- External-sender, suspicious-link, newly registered-domain, and impersonation warning banners.
- A simple in-client phishing-report button.
- Awareness training focused on social engineering and safe verification.
- Controlled phishing simulations that measure and reinforce behavior.

A warning banner is context, not a verdict. Excessive warnings can produce alert fatigue, so controls should be targeted and actionable.

## SOC Investigation Workflow

1. Preserve the original message and raw source.
2. Record sender, recipients, subject, timestamp, Message-ID, URLs, and attachments.
3. Validate SPF, DKIM, DMARC, and alignment.
4. Reconstruct SMTP delivery and review server responses.
5. Inspect MIME structure and identify encoded or hidden content.
6. Defang and enrich domains, URLs, IP addresses, and hashes.
7. Analyze attachments statically before any execution.
8. Use a sandbox when dynamic behavior is required and authorized.
9. Determine whether a user clicked, downloaded, executed, or submitted credentials.
10. Correlate email evidence with DNS, proxy, firewall, identity, and endpoint telemetry.
11. Contain confirmed exposure and block validated indicators.
12. Document evidence, scope, verdict, impact, and escalation rationale.

## Detection and Response Opportunities

- Detect lookalike domains, display-name impersonation, and reply-to mismatches.
- Alert on failed or misaligned DMARC results for protected domains.
- Flag risky attachment types and mismatches between extension and file signature.
- Detect Office or mail-client processes spawning shells, script interpreters, or unknown binaries.
- Correlate message delivery with new DNS queries, URL visits, downloads, and endpoint execution.
- Hunt across the environment for matching sender, subject, URL, domain, IP, and attachment hash.
- Revoke sessions and reset credentials when credential submission is suspected.
- Isolate endpoints when malicious execution or payload retrieval is confirmed.

## MITRE ATT&CK Mapping

| Technique | Relevance |
|---|---|
| [T1566 — Phishing](https://attack.mitre.org/techniques/T1566/) | Phishing as an initial-access technique |
| [T1566.001 — Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/) | Malicious files delivered by email |
| [T1566.002 — Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/) | Links leading to credential theft or payload delivery |
| [T1204.001 — Malicious Link](https://attack.mitre.org/techniques/T1204/001/) | User interaction with a phishing URL |
| [T1204.002 — Malicious File](https://attack.mitre.org/techniques/T1204/002/) | User opens a weaponized attachment |

## Skills Gained

- Interpreted SPF, DKIM, DMARC, and S/MIME evidence.
- Distinguished authentication, integrity, alignment, and encryption controls.
- Analyzed SMTP response codes and email traffic in Wireshark.
- Inspected IMF and MIME fields for message and attachment artifacts.
- Recognized Base64 as transport encoding rather than encryption.
- Selected sandboxing for safe behavioral analysis.
- Evaluated technical defenses, user warnings, reporting, and training.
- Built a layered SOC phishing investigation and response workflow.

## Key Takeaways

- No single email-security control can stop every phishing message.
- Authentication success proves limited technical facts; it does not prove benign intent.
- DMARC alignment is more meaningful than an isolated SPF or DKIM result.
- Base64 hides content from casual viewing but does not secure it.
- Attachments should be inspected statically before controlled dynamic analysis.
- Effective prevention combines email security, endpoint and network controls, trained users, and a responsive SOC.
- User interaction and post-delivery activity determine the true incident impact.

## Completion Evidence

- **Platform:** TryHackMe
- **Room:** Phishing Prevention
- **Completed:** October 3, 2026
- **Module:** Phishing Analysis
- **Tasks completed:** 9/9

## References

- [TryHackMe — Phishing Prevention](https://tryhackme.com/room/phishingprevention)
- [RFC 7208 — Sender Policy Framework (SPF)](https://www.rfc-editor.org/rfc/rfc7208)
- [RFC 6376 — DomainKeys Identified Mail (DKIM)](https://www.rfc-editor.org/rfc/rfc6376)
- [RFC 7489 — DMARC](https://www.rfc-editor.org/rfc/rfc7489)
- [RFC 8551 — S/MIME 4.0 Message Specification](https://www.rfc-editor.org/rfc/rfc8551)
- [RFC 5321 — Simple Mail Transfer Protocol](https://www.rfc-editor.org/rfc/rfc5321)
- [RFC 5322 — Internet Message Format](https://www.rfc-editor.org/rfc/rfc5322)
- [CISA — Phishing Guidance: Stopping the Attack Cycle at Phase One](https://www.cisa.gov/resources-tools/resources/phishing-guidance-stopping-attack-cycle-phase-one)
- [MITRE ATT&CK — Phishing](https://attack.mitre.org/techniques/T1566/)
- [Wireshark Display Filter Reference — SMTP](https://www.wireshark.org/docs/dfref/s/smtp.html)
