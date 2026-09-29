# Phishing Emails in Action

## Room Summary

This room applies phishing-analysis fundamentals to realistic email samples. The exercises focus on identifying social-engineering pressure, validating the true sender, resolving hidden link destinations, recognizing tracking pixels, detecting credential-harvesting portals, and analyzing suspicious document-based delivery chains.

The scenarios progress from simple brand impersonation and shortened URLs to multi-stage redirects, embedded document links, and an office attachment that attempts to retrieve and execute a payload.

> This write-up documents methodology and defensive lessons only. Direct room answers, flags, and live malicious indicators are intentionally excluded.

## Objectives

- Detect display-name spoofing and unrelated sender domains.
- Identify urgency, fear, and transaction-based social-engineering lures.
- Compare visible link text with the real hyperlink destination.
- Recognize URL shorteners, redirect chains, and tracking pixels.
- Identify credential-harvesting portals and fake cloud-login workflows.
- Assess suspicious PDF, Word template, and Excel attachments safely.
- Map observed behavior to MITRE ATT&CK.
- Document evidence without exposing active indicators.

## Investigation Mindset

A convincing logo or professional-looking template is not proof that a message is legitimate. Each sample was evaluated through several evidence layers:

1. **Sender identity** — display name, mailbox, domain, Reply-To, and alignment with the claimed brand.
2. **Recipient context** — direct recipient, CC/BCC behavior, and whether the message was expected.
3. **Social engineering** — urgency, account suspension, unexpected purchases, package delivery, and document-expiration claims.
4. **Link behavior** — visible text, actual href, shortened URLs, redirects, and final landing pages.
5. **Remote content** — images, tracking pixels, and resources loaded from unrelated infrastructure.
6. **Attachments** — file type, embedded links, external downloads, and execution behavior.
7. **Business context** — whether the user expected the transaction, document, shipment, or account notification.

## Scenario Patterns

| Scenario theme | Primary indicators | Defensive lesson |
|---|---|---|
| Unexpected payment receipt | Display-name spoofing, unrelated sender domain, urgent cancellation button, shortened URL | Inspect the actual sender and expand links without visiting the destination directly |
| Package tracking notice | Fake tracking reference, sender-domain mismatch, hidden hyperlink, remote tracking image | Review raw HTML for href destinations and zero-size or remote images |
| Shared document notification | Expiring-document pressure, multiple redirects, copied cloud-service branding, fake sign-in portal | Follow the redirect chain in an isolated analysis service and never submit credentials |
| Account billing warning | Brand impersonation, suspicious attachment, embedded payment-update link, grammar inconsistencies | Extract links from the document without opening them on a production endpoint |
| App-store purchase alert | Blank body, BCC delivery, unusual Word template attachment, image-based lure | Treat unexpected templates and image-only receipts as suspicious delivery mechanisms |
| Courier shipment notice | Sender-domain mismatch, geographical/language inconsistencies, spreadsheet attachment, external payload retrieval | Analyze office documents statically and block external content and child-process execution |

## Display Names and Sender Spoofing

Attackers can set a trusted company name as the visible sender while using a mailbox on an unrelated domain. The display name is presentation data; it does not establish identity.

Useful checks include:

- Compare the visible brand with the domain after the @ symbol.
- Look for misspellings, extra words, or unrelated top-level domains.
- Review Reply-To and Return-Path for mismatches.
- Confirm whether the brand normally communicates from that domain.
- Correlate the sender with SPF, DKIM, and DMARC results, while remembering that authentication of an attacker-controlled domain does not make the impersonation legitimate.

## Urgency and Pretext Analysis

The samples used several high-pressure pretexts:

- An unexpected purchase that must be cancelled.
- A parcel that requires immediate tracking.
- A document that expires the same day.
- A suspended streaming account.
- An app-store transaction requiring action.
- A scheduled courier shipment with an attached invoice.

Urgency is not proof of phishing, but it is a strong social-engineering indicator when combined with sender mismatches, unexpected attachments, or requests for credentials and payment information.

## Link and Redirect Analysis

The visible label of a button can differ from its actual destination. Raw HTML or document structure should be used to extract the underlying link safely.

Key checks:

- Compare anchor text with the href value.
- Identify URL shorteners and redirect services.
- Record each redirect without browsing from a normal workstation.
- Determine the final registrable domain.
- Defang URLs and domains before sharing them.
- Check whether the landing page imitates a trusted service but uses unrelated infrastructure.

Example safe notation:

```text
Domain: suspicious[.]example
URL: hxxps[://]suspicious[.]example/login
```

## Tracking Pixels

A tracking pixel is commonly implemented as a tiny or hidden remote image. When an email client retrieves it, the remote server can learn that the message was opened and may receive metadata such as time, IP address, user agent, or a campaign-specific identifier.

During analysis:

- Keep remote images disabled.
- Inspect HTML for external image sources.
- Look for zero-width, zero-height, or hidden image elements.
- Record the hosting domain as an indicator.
- Use email-gateway and proxy telemetry to check whether remote content was retrieved.

## Credential-Harvesting Portals

One scenario used a multi-stage redirect chain that imitated familiar document-sharing and login services. The final page collected credentials rather than authenticating the user.

Indicators included:

- A login prompt hosted on an unrelated domain.
- Reused logos and copied cloud-service branding.
- Generic errors after credential submission.
- Multiple redirects before the final portal.
- Inconsistent wording and formatting.
- A request to authenticate solely to view an unexpected document.

A generic error does not make a page harmless. It may be shown after credentials have already been transmitted to attacker infrastructure.

## Attachment-Based Delivery

### PDF

A PDF can contain an embedded hyperlink while appearing to be a normal billing document. The URL should be extracted with a static-analysis tool rather than clicked.

### Word Template

An unexpected template file is unusual for a purchase receipt. Image-based content and embedded links can hide the real destination from simple text inspection.

### Excel Workbook

A spreadsheet may contain clickable content that retrieves a second-stage payload. The investigation should examine relationships, external links, scripts, and child-process behavior without enabling editing or executing content.

Recommended controls include:

- Block or quarantine high-risk attachment types from untrusted senders.
- Disable automatic external-content retrieval.
- Restrict Office child processes through endpoint controls.
- Detonate suspicious files in an isolated sandbox.
- Monitor Office applications spawning interpreters, LOLBins, or unknown executables.
- Correlate download, DNS, proxy, and endpoint events.

## MITRE ATT&CK Mapping

| Technique | Relevance |
|---|---|
| [T1566.001 — Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/) | Documents and spreadsheets used to deliver malicious links or code |
| [T1566.002 — Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/) | Shortened, redirected, or disguised links delivered by email |
| [T1204.001 — Malicious Link](https://attack.mitre.org/techniques/T1204/001/) | User interaction required to follow a malicious link |
| [T1204.002 — Malicious File](https://attack.mitre.org/techniques/T1204/002/) | User interaction required to open a malicious attachment |
| [T1056.003 — Web Portal Capture](https://attack.mitre.org/techniques/T1056/003/) | Fake authentication portals used to collect credentials |
| [T1105 — Ingress Tool Transfer](https://attack.mitre.org/techniques/T1105/) | An attachment or link retrieves an additional payload |

## SOC Triage Workflow

1. Preserve the original email and obtain the raw source.
2. Record sender, recipients, subject, timestamp, Message-ID, and attachment names.
3. Validate sender and Reply-To domains against the claimed organization.
4. Review authentication and routing headers.
5. Extract URLs from HTML and attachments without clicking.
6. Resolve shortened URLs and redirect chains using an approved isolated service.
7. Keep remote images disabled and identify tracking content.
8. Hash attachments and perform static analysis first.
9. Use a sandbox only when authorized and required.
10. Search email-gateway, DNS, proxy, endpoint, and identity telemetry for exposure.
11. Determine whether any user clicked, downloaded, executed, or entered credentials.
12. Contain affected accounts and endpoints, then block confirmed indicators.
13. Document evidence, scope, verdict, impact, and response actions.

## Skills Gained

- Triaged phishing lures across payment, shipping, document-sharing, streaming, app-store, and courier themes.
- Detected display-name spoofing and sender-domain mismatches.
- Identified BCC-based bulk delivery and image-only lures.
- Extracted hidden hyperlinks and evaluated redirect chains safely.
- Recognized email tracking pixels in HTML source.
- Distinguished credential-harvesting portals from legitimate authentication.
- Assessed PDF, Word template, and Excel attachment risks.
- Connected phishing evidence to endpoint, proxy, DNS, identity, and email telemetry.
- Mapped observed behaviors to relevant MITRE ATT&CK techniques.

## Key Takeaways

- Branding is easy to copy; domain identity and message context matter more.
- The visible link label is not the destination.
- Remote images can report that an email was opened.
- HTTPS protects transport but does not prove that a site is trustworthy.
- Credential-harvesting pages may display an error after stealing submitted data.
- Attachments can conceal links or trigger second-stage downloads.
- Strong phishing triage determines whether the user interacted and then scopes the resulting exposure.

## Completion Evidence

- **Platform:** TryHackMe
- **Room:** Phishing Emails in Action
- **Completed:** September 29, 2026
- **Module:** Phishing Analysis
- **Tasks completed:** 8/8

![TryHackMe Phishing Emails in Action room completion](./images/phishing-emails-in-action/01-room-completed.png)

## References

- [TryHackMe — Phishing Emails in Action](https://tryhackme.com/room/phishingemails2rytmuv)
- [CISA — Recognize and Report Phishing](https://www.cisa.gov/secure-our-world/recognize-and-report-phishing)
- [MITRE ATT&CK — Phishing](https://attack.mitre.org/techniques/T1566/)
- [MITRE ATT&CK — Web Portal Capture](https://attack.mitre.org/techniques/T1056/003/)
- [Microsoft — Protect against phishing](https://support.microsoft.com/windows/protect-yourself-from-phishing-0c7ea947-ba98-3bd9-7184-430e1f860a44)
- [Apple — Recognize and avoid phishing messages](https://support.apple.com/102568)
- [Netflix — Phishing or suspicious emails](https://help.netflix.com/en/node/65674)
