# Unified Kill Chain

## Room Summary

This room introduced the Unified Kill Chain (UKC), an 18-phase model created by Paul Pols to describe modern cyberattacks from initial preparation through network propagation and final objectives. The framework complements the Lockheed Martin Cyber Kill Chain and MITRE ATT&CK by providing a more detailed, end-to-end view of adversary behavior.

## Learning Objectives

- Explain how threat modelling supports risk identification and defensive planning.
- Understand the 18 phases of the Unified Kill Chain.
- Group attack activity into Initial Foothold, Network Propagation, and Action on Objectives.
- Map attacker behavior to relevant SOC telemetry and MITRE ATT&CK tactics.
- Distinguish collection, exfiltration, impact, and the adversary's strategic objective.

## Threat Modelling

Threat modelling is a structured process for identifying assets, weaknesses, likely attack paths, and appropriate security controls before or during system design.

A practical workflow is:

1. Identify systems, applications, data, and business functions that require protection.
2. Assess vulnerabilities, weaknesses, trust boundaries, and possible attack paths.
3. Prioritize risks and define mitigations.
4. Implement controls, policies, secure development practices, and user awareness.
5. Reassess the model when the environment or threat landscape changes.

Frameworks such as STRIDE, DREAD, and CVSS can support different parts of this process.

## Unified Kill Chain Overview

The UKC organizes attacker activity into three high-level goals:

| Goal | Purpose | Included phases |
|---|---|---|
| **In — Initial Foothold** | Prepare the operation, gain the first access, establish persistence, and control the compromised system. | Reconnaissance, Weaponization, Delivery, Social Engineering, Exploitation, Persistence, Defense Evasion, Command and Control |
| **Through — Network Propagation** | Use the foothold to understand the environment, gain privileges, steal credentials, and move toward additional systems. | Pivoting, Discovery, Privilege Escalation, Execution, Credential Access, Lateral Movement |
| **Out — Action on Objectives** | Gather or steal information, disrupt operations, and achieve the attacker's strategic goal. | Collection, Exfiltration, Impact, Objectives |

Real intrusions are not always linear. Attackers may repeat discovery, execution, credential access, and lateral movement across multiple systems.

## The 18 Phases

### In — Initial Foothold

1. **Reconnaissance** — Research and select targets through passive or active information gathering.
2. **Weaponization** — Prepare the infrastructure and capabilities required for the operation.
3. **Delivery** — Transfer a malicious object, link, payload, or other attack mechanism to the target.
4. **Social Engineering** — Manipulate a person into performing an unsafe action.
5. **Exploitation** — Abuse a vulnerability or weakness to gain execution or access.
6. **Persistence** — Maintain access through reboots, credential changes, or other interruptions.
7. **Defense Evasion** — Avoid detection or disable defensive mechanisms.
8. **Command and Control** — Communicate with compromised systems and issue instructions.

> The room uses the term **Weaponization**. Current MITRE ATT&CK terminology uses **Resource Development (TA0042)** for establishing infrastructure, accounts, and capabilities that support an operation.

### Through — Network Propagation

9. **Pivoting** — Use a compromised system as a tunnel or staging point to reach otherwise inaccessible systems.
10. **Discovery** — Learn about users, hosts, applications, permissions, network shares, and configurations.
11. **Privilege Escalation** — Obtain higher permissions such as local administrator, SYSTEM, root, or domain-level access.
12. **Execution** — Run attacker-controlled commands, scripts, tools, or payloads.
13. **Credential Access** — Steal credentials, password material, hashes, tokens, or secrets.
14. **Lateral Movement** — Enter and control additional systems inside the environment.

### Out — Action on Objectives

15. **Collection** — Identify and gather data relevant to the adversary's goal.
16. **Exfiltration** — Transfer stolen data out of the victim environment.
17. **Impact** — Manipulate, interrupt, encrypt, or destroy systems and data.
18. **Objectives** — Achieve the strategic purpose of the attack, such as financial gain, espionage, disruption, or reputational damage.

## SOC Detection and Investigation Map

| Attack behavior | Useful telemetry | What an analyst should look for |
|---|---|---|
| Reconnaissance | WAF, IDS/IPS, DNS, authentication, external exposure monitoring | Scanning, enumeration, repeated requests, and unusual account research |
| Delivery and social engineering | Email gateway, proxy, browser, sandbox, user reports | Spoofing, malicious links, attachments, redirects, and user interaction |
| Exploitation and execution | EDR, Sysmon, application logs, process telemetry | Suspicious parent-child processes, scripts, exploit alerts, and abnormal command lines |
| Persistence | Services, registry, scheduled tasks, startup locations, account changes | New autoruns, malicious services, recurring scripts, and unauthorized accounts |
| Defense evasion | EDR, security-tool logs, PowerShell, audit policy changes | Tool tampering, log clearing, obfuscation, exclusions, and disabled controls |
| Command and control | DNS, proxy, firewall, TLS metadata, NDR | Beaconing, rare destinations, unusual protocols, and periodic outbound traffic |
| Discovery and privilege escalation | Process creation, command line, identity, endpoint events | Enumeration commands, token abuse, local exploits, and new privileged membership |
| Credential access | LSASS access, authentication, EDR, secrets-management logs | Credential dumping, keylogging, abnormal token access, and secret theft |
| Pivoting and lateral movement | RDP, SMB, WinRM, SSH, VPN, authentication, network flows | New internal connections, remote execution, pass-the-hash, and unusual admin logons |
| Collection and exfiltration | File auditing, DLP, cloud logs, proxy, firewall, DNS | Archive creation, sensitive file access, large outbound transfers, and uncommon destinations |
| Impact | File, backup, endpoint, service, and availability monitoring | Mass encryption, deletion, recovery inhibition, defacement, or denial of service |

## Relationship to the CIA Triad

- **Confidentiality:** threatened by credential theft, collection, and exfiltration.
- **Integrity:** threatened by unauthorized modification, manipulation, and destructive activity.
- **Availability:** threatened by encryption, service interruption, deletion, and denial of service.

## Practical Exercise

The practical task required matching attacker actions to the most appropriate UKC phases. The exercise reinforced the differences between reconnaissance, persistence, command and control, pivoting, and action on objectives.

Direct answers and flags are intentionally excluded from this public portfolio. Private learning evidence is retained separately.

## Skills Gained

- Applied an 18-phase model to reconstruct end-to-end attack progression.
- Distinguished initial access, persistence, command and control, pivoting, and lateral movement.
- Connected UKC phases with MITRE ATT&CK tactics and observable SOC evidence.
- Identified detection opportunities across email, endpoint, identity, network, and data telemetry.
- Related collection, exfiltration, and impact to the CIA triad.
- Used threat modelling to identify assets, attack surfaces, weaknesses, and mitigations.

## Key Takeaways

- The Unified Kill Chain adds detail to traditional kill-chain models without replacing them.
- Attackers may move backward, repeat phases, or operate across multiple systems simultaneously.
- The **Through** phase provides valuable visibility into post-compromise behavior that simpler models may compress.
- Mapping detections and controls to attack phases helps identify coverage gaps.
- UKC and MITRE ATT&CK work best together: UKC explains the attack journey, while ATT&CK provides detailed tactics and techniques.

## References

- [Unified Kill Chain — Official Website](https://www.unifiedkillchain.com/)
- [Paul Pols — The Unified Kill Chain White Paper](https://www.unifiedkillchain.com/assets/The-Unified-Kill-Chain.pdf)
- [MITRE ATT&CK — Enterprise Tactics](https://attack.mitre.org/tactics/)
- [NIST CSRC — Threat Modelling](https://csrc.nist.gov/glossary/term/threat_modeling)
- [TryHackMe — Unified Kill Chain](https://tryhackme.com/room/unifiedkillchain)
