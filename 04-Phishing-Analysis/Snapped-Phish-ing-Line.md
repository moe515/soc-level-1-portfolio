# Snapped Phish-ing Line

> **Public portfolio note:** This report documents the investigation process, analyst reasoning, defensive findings, and response recommendations. Direct room answers, flags, credentials, exact hashes, filenames, victim identities, and live indicators are intentionally excluded.

## Executive Summary

This investigation examined a coordinated credential-phishing campaign targeting employees of a fictional financial organization. Multiple users across different departments received suspicious messages, and the incident escalated after some recipients submitted credentials and subsequently lost access to their accounts.

The analysis connected the malicious emails to a redirect chain, a Microsoft-branded credential-harvesting page, an exposed phishing-kit archive, captured credential logs, and server-side code used to forward stolen data to the threat actor.

**Verdict:** True Positive — Active Credential-Phishing Campaign  
**Severity:** High — Confirmed credential exposure and potential account takeover

## Scenario and Objectives

The investigation was structured around five objectives:

1. Analyze the supplied email samples and extract useful artifacts.
2. Trace the phishing URL and understand the redirect behavior.
3. Retrieve and assess the phishing kit without executing untrusted content.
4. Use threat-intelligence sources to evaluate the archive.
5. Inspect the kit and exposed server content for additional indicators and evidence of impact.

## Investigation Workflow

### 1. Email Triage

The messages used a financial-document theme to create business relevance. A spoofed organization and an external sender presented an attached HTML document as a routine payment or credit notice.

The following warning signs were identified:

- External sender delivering an unexpected financial document.
- Sender domain unrelated to the recipient organization.
- Broad targeting across several departments.
- HTML attachment requiring browser interaction.
- Payment-themed language intended to encourage immediate review.
- A link or redirect destination unrelated to the impersonated brand.

The campaign was treated as a related cluster rather than as isolated messages because the sender pattern, subject matter, attachment style, and infrastructure overlapped.

### 2. Attachment and Redirect Analysis

The HTML attachment was inspected as source rather than trusted as a document. It directed the browser through attacker-controlled infrastructure to a login page imitating Microsoft.

The page pre-populated the victim's email address, which increased credibility and reduced the number of actions required from the user. The final destination requested a password over an insecure connection. This behavior was consistent with credential harvesting rather than legitimate authentication.

The safe-analysis approach included:

- Reviewing HTML and script content before rendering.
- Extracting and defanging URLs.
- Separating the root domain from the complete redirect path.
- Comparing the destination domain with the claimed Microsoft identity.
- Avoiding the entry of real credentials.

### 3. Infrastructure and Exposure Discovery

Manual path review revealed that directory indexing was enabled on the attacker-controlled web server. This information-disclosure weakness exposed additional campaign resources, including a downloadable archive and a directory containing the phishing site.

Directory listing is important evidence because it can reveal:

- Additional landing pages and campaign variants.
- Archive names and modification times.
- Server-side scripts.
- Images, CSS, and JavaScript used for brand impersonation.
- Logs containing captured victim information.

The exposed content allowed the investigation to move from a single phishing email to campaign-level analysis.

### 4. Phishing-Kit Reputation Analysis

The archive was hashed locally with SHA-256, and the digest was submitted to a multi-engine reputation service. Numerous security engines classified the sample as malicious, with labels associated with phishing, trojan behavior, and credential theft.

The analysis also showed that the archive contained many files of different types, including PHP, JavaScript, CSS, images, text files, and supporting directories. This was consistent with a packaged phishing kit rather than a harmless web template.

The exact digest, detection ratio, archive name, and file count are omitted from the public report because they are direct challenge answers.

### 5. Credential-Harvesting Confirmation

A server-exposed log demonstrated that the phishing page had received submitted credentials. Repeated entries for at least one victim showed multiple authentication attempts, which is common when a fake page deliberately rejects the first password or claims that the login failed.

The exposed records also included contextual data such as source IP, browser user agent, country, and timestamp. This confirmed that the campaign was operational and had already affected users.

No victim email addresses, passwords, IP addresses, or captured log contents are reproduced here.

### 6. Phishing-Kit Code Review

Reviewing the server-side submission script showed how the kit processed form data and assembled a structured message containing the submitted username, password, IP address, user agent, country, and date.

The code also contained an attacker-controlled collection address used to receive the stolen information. This converted the investigation from suspected phishing to confirmed credential theft with a documented exfiltration mechanism.

A separate text artifact contained an encoded challenge value. Base64 decoding followed by character reversal recovered the room flag. The flag is intentionally excluded from this report.

## Attack Flow

1. The attacker sent payment-themed phishing emails to multiple employees.
2. A recipient opened the attached HTML document.
3. The attachment redirected the browser to attacker-controlled infrastructure.
4. A Microsoft-branded page requested the victim's password.
5. Submitted data was processed by a server-side script.
6. Credentials and victim metadata were written to a log and forwarded to the attacker.
7. Stolen credentials enabled account-access attempts and possible takeover.

## Sanitized Evidence Matrix

| Finding | Observation | Security significance |
|---|---|---|
| Coordinated delivery | Similar messages reached users in multiple departments | Indicates a campaign rather than a one-off email |
| Financial pretext | Messages referenced a payment or credit document | Creates urgency and business relevance |
| HTML attachment | The document initiated browser-based activity | Provides a delivery mechanism for redirection |
| External redirect | The final domain did not match the impersonated brand | Strong evidence of deceptive infrastructure |
| Brand impersonation | The landing page imitated Microsoft authentication | Designed to steal enterprise credentials |
| Insecure authentication page | Password collection occurred over an untrusted connection | Legitimate Microsoft authentication would not behave this way |
| Open directory listing | Campaign files and an archive were publicly exposed | Enabled discovery of the broader phishing infrastructure |
| Malicious archive reputation | Multiple engines detected phishing or trojan behavior | Supports a high-confidence malicious classification |
| Captured credential log | Submitted credentials and client metadata were stored | Confirms successful victim interaction and impact |
| Server-side exfiltration | Submission code forwarded collected data externally | Confirms the attacker's credential-theft workflow |

## Analyst Verdict

**True Positive — Active Credential-Phishing Campaign**

The campaign used targeted financial lures, malicious HTML attachments, redirect infrastructure, Microsoft brand impersonation, credential-capture forms, server-side data collection, and attacker-controlled exfiltration. Exposed logs demonstrated that users had submitted credentials, so this was not merely an attempted phish; it was a confirmed security incident with potential account compromise.

## Immediate SOC Response

1. Disable or temporarily restrict affected accounts.
2. Force password resets through a trusted administrative channel.
3. Revoke active sessions, refresh tokens, app passwords, and remembered devices.
4. Confirm and enforce multi-factor authentication.
5. Review identity-provider logs for successful and failed sign-ins, impossible travel, unfamiliar devices, new MFA registrations, mailbox-rule creation, and OAuth consent.
6. Quarantine all related emails across the organization.
7. Search for recipients who opened the attachment, visited the phishing site, or submitted data.
8. Block validated domains, URLs, sender infrastructure, attachment hashes, and archive hashes at email, DNS, proxy, firewall, and endpoint layers.
9. Preserve the emails, attachments, web artifacts, code, exposed logs, and timeline for forensic analysis.
10. Escalate to Incident Response and notify fraud or finance teams because compromised mailboxes may be used for business email compromise.

## Detection and Hunting Opportunities

- Alert on external emails containing HTML attachments and financial-language keywords.
- Detect HTML files that contain JavaScript redirects, meta refreshes, encoded URLs, or credential forms.
- Compare linked domains with displayed brands and sender domains.
- Correlate email delivery with proxy visits to newly observed or low-reputation domains.
- Hunt for repeated authentication failures followed by a successful login from a new location.
- Detect new inbox-forwarding rules, suspicious OAuth grants, or MFA changes after a phishing event.
- Search web telemetry for common paths associated with login clones, submission handlers, archives, and exposed logs.
- Monitor for the same page assets, favicon, form structure, or code fragments across related infrastructure.

## MITRE ATT&CK Mapping

| Technique | ID | Relevance |
|---|---|---|
| Spearphishing Attachment | [T1566.001](https://attack.mitre.org/techniques/T1566/001/) | A malicious HTML attachment initiated the phishing flow |
| User Execution: Malicious File | [T1204.002](https://attack.mitre.org/techniques/T1204/002/) | The attack relied on the recipient opening the attachment |
| Masquerading | [T1036](https://attack.mitre.org/techniques/T1036/) | Microsoft branding and a business document pretext were used to appear legitimate |
| Input Capture: Web Portal Capture | [T1056.003](https://attack.mitre.org/techniques/T1056/003/) | A cloned login portal captured usernames and passwords |
| Application Layer Protocol: Web Protocols | [T1071.001](https://attack.mitre.org/techniques/T1071/001/) | HTTP web traffic supported redirection and credential submission |

## Skills Demonstrated

- Multi-email campaign correlation.
- Safe HTML and URL analysis.
- Redirect-chain investigation and domain extraction.
- Brand-impersonation identification.
- Directory-index exposure discovery.
- SHA-256 hashing and VirusTotal reputation analysis.
- Phishing-kit archive and source-code review.
- Credential-harvesting confirmation without publishing victim data.
- Evidence-based verdict writing, containment planning, and threat hunting.

## Key Takeaways

- A suspicious email can be the entry point to a much larger campaign.
- HTML attachments deserve the same caution as executables because they can launch credential-theft workflows.
- A legitimate-looking Microsoft page is meaningless when the domain and transport are untrusted.
- Open directory listings can expose a phishing kit, logs, and attacker infrastructure.
- Repeated submissions may show that the fake portal intentionally manipulated victims into retrying passwords.
- Credential exposure requires session revocation and identity-log review, not only a password reset.
- Sensitive victim data should be documented securely and excluded from public portfolio material.

## Completion Evidence

- **Platform:** TryHackMe
- **Room:** Snapped Phish-ing Line
- **Module:** Phishing Analysis
- **Completed:** October 11, 2026
- **Status:** 1/1 task completed

## References

- [TryHackMe — SOC Level 1 Path](https://tryhackme.com/path/outline/soclevel1)
- [MITRE ATT&CK — Phishing](https://attack.mitre.org/techniques/T1566/)
- [MITRE ATT&CK — Input Capture](https://attack.mitre.org/techniques/T1056/)
- [CISA — Recognize and Report Phishing](https://www.cisa.gov/secure-our-world/recognize-and-report-phishing)
- [VirusTotal Documentation](https://docs.virustotal.com/)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
