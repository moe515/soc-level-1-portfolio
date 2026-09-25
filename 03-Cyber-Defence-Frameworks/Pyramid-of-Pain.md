# Pyramid of Pain

**Module:** Cyber Defence Frameworks  
**Platform:** TryHackMe — SOC Level 1  
**Status:** Completed  
**Completed:** 2026-09-25

> This learning note summarizes concepts and analyst takeaways in my own words. It intentionally excludes room answers, flags, and step-by-step solutions.

## Objective

Understand how the Pyramid of Pain ranks threat indicators by the operational cost imposed on an adversary when defenders detect and act on them. The higher the detection moves from atomic indicators toward adversary behavior, the harder it is for the attacker to adapt.

## The Pyramid

| Tier | Relative Pain | Defensive Value |
|---|---|---|
| Hash Values | Trivial | Identify an exact known file, but change when the file changes |
| IP Addresses | Easy | Track infrastructure, but adversaries can rotate or replace addresses |
| Domain Names | Simple | Reveal malicious infrastructure, but domains can be changed or re-registered |
| Network Artifacts | Annoying | Detect patterns such as URIs, User-Agent strings, protocol behavior, and beacon characteristics |
| Host Artifacts | Annoying | Detect file paths, registry changes, services, process relationships, and other endpoint evidence |
| Tools | Challenging | Identify an adversary's software, capabilities, and reusable implementation patterns |
| Tactics, Techniques, and Procedures (TTPs) | Tough | Detect how the adversary operates, forcing meaningful changes to behavior and tradecraft |

## Core Analyst Takeaways

- **Do not stop at a single IOC.** Use a hash, IP, or domain as a starting point, then pivot to processes, command lines, persistence, traffic patterns, and ATT&CK techniques.
- **Atomic indicators are useful but brittle.** They support rapid enrichment and blocking, but an adversary can often replace them quickly.
- **Behavioral detections are more durable.** Parent-child process relationships, credential-access patterns, persistence behavior, and command execution can survive changes to files or infrastructure.
- **Use multiple layers together.** IOC matching provides speed; artifact and behavior analytics provide resilience and investigative context.
- **Document the evidence chain.** Connect the initial indicator to affected assets, user activity, host telemetry, network activity, and the most likely adversary technique.

## Detection Engineering Perspective

| Indicator Level | Example Defensive Approach |
|---|---|
| Hash Values | File reputation, allow/block lists, malware repository correlation |
| IP Addresses | Firewall, proxy, VPN, and flow-log correlation |
| Domain Names | DNS analytics, domain reputation, registration context, fast-flux detection |
| Network Artifacts | IDS signatures, URI and header analytics, beacon-pattern detection |
| Host Artifacts | EDR detections for processes, files, registry keys, services, and persistence |
| Tools | YARA rules, capability-based signatures, and fuzzy or similarity matching |
| TTPs | MITRE ATT&CK-aligned behavioral analytics and multi-event correlation |

## Practical Workflow

1. Validate the initial indicator and record its source.
2. Search SIEM, EDR, DNS, proxy, firewall, and email telemetry for matches.
3. Pivot from the indicator to related hosts, users, processes, domains, and connections.
4. Identify the host and network artifacts produced by the activity.
5. Determine the likely tool or capability involved.
6. Map the observed behavior to MITRE ATT&CK.
7. Build or tune detections at the highest reliable level while retaining lower-level IOCs for short-term blocking and enrichment.

## Skills Gained

- Classified hashes, IP addresses, domains, host artifacts, network artifacts, tools, and TTPs by defensive value.
- Explained why higher-level behavioral detections impose more cost on an adversary.
- Connected atomic IOCs to broader host, network, and behavioral evidence.
- Recognized fast-flux infrastructure, domain-generation behavior, and lookalike-domain techniques as challenges for indicator-only defense.
- Applied YARA and fuzzy-hashing concepts to tool and malware identification.
- Mapped adversary behavior to the MITRE ATT&CK knowledge base.
- Completed the room's practical indicator-classification exercise.

## References

- [David J. Bianco — The Pyramid of Pain](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [YARA Documentation](https://yara.readthedocs.io/en/stable/)
- [Wireshark — TShark Manual](https://www.wireshark.org/docs/man-pages/tshark.html)
- [NIST — Approximate Matching / Fuzzy Hashing](https://www.nist.gov/itl/ssd/software-quality-group/approximate-matching)
