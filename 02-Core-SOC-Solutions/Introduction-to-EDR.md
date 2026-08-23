# Introduction to EDR

**Path:** SOC Level 1 -> Core SOC Solutions
**Room:** Introduction to EDR (https://tryhackme.com/room/introtoedr) - 60 min
**Status:** Completed

## Objective
Understand the fundamentals of Endpoint Detection and Response (EDR): how it differs from traditional antivirus, its architecture, the telemetry it collects, and its detection/response capabilities.

## Key Concepts

### 1. EDR vs. Antivirus
Antivirus checks files against a known-bad signature database - like an immigration checkpoint matching passports to a criminal list. It misses novel or fileless threats that have no prior signature.

EDR instead continuously monitors endpoint behavior (like security officers watching CCTV inside the building), so it can catch each step of an attack chain even when individual actions look legitimate in isolation - e.g. a Word doc spawning a PowerShell macro, an obfuscated download, then process injection into a legitimate binary. AV may miss every step; EDR flags the chain.

### 2. Architecture: Agents + Console
- **EDR Agents** - lightweight sensors deployed on each endpoint. They watch all activity in real time and forward telemetry to the console.
- **EDR Console** - central platform that correlates data from all agents using detection logic, threat intel, and machine learning, then generates alerts (detections) with severity ratings.

### 3. Telemetry Collected
- Process executions and terminations (parent-child relationships, suspicious executables)
- Network connections (C2 communication, unusual ports, exfiltration, lateral movement)
- Command line activity (including obfuscated PowerShell/CMD usage)
- File and folder modifications (data staging, ransomware, malicious drops)
- Registry modifications (persistence mechanisms, config changes)

### 4. Detection Techniques
- **Behavioral detection** - flags the full observed behavior of a process, not just its signature
- **Anomaly detection** - compares activity against an established endpoint baseline
- **IOC matching** - correlates activity with known indicators from threat intel feeds
- **MITRE ATT&CK mapping** - tags detections with the relevant tactic/technique (e.g. T1003.001 Credential Dumping)
- **Machine learning models** - catch fileless or multi-staged attacks that don't rely on a single strong indicator

### 5. Response Capabilities
- **Isolate host** - cuts the endpoint off from the network to contain spread while investigation continues
- **Terminate process** - kills a malicious process immediately when necessary
- **Quarantine** - moves a malicious file to a safe location so it can't execute, without fully removing it
- **Remote access** - analysts can connect into the endpoint to run commands/scripts directly
- **Artifact collection** - pulling memory dumps, event logs, and specific files for deeper forensic review

### 6. Investigating Alerts in the EDR
Each detection view typically includes: a Summary, Process Info (the full process chain from parent to child, command line, hash, signer, user, timestamps), IOC/Indicators (file paths, hashes, IPs, domains and the action taken on each), and Actions/Response. Reading the process chain end-to-end is what lets an analyst reconstruct the attack path - e.g. explorer.exe -> a suspicious binary -> credential access behavior -> an attempted exfiltration connection.

## Skills Gained

- Differentiating EDR from traditional antivirus and explaining why both are needed
- Understanding EDR agent/console architecture
- Reading endpoint telemetry (process, network, command line, file, registry)
- Recognizing detection techniques (behavioral, anomaly, IOC, MITRE ATT&CK, ML)
- Knowing available EDR response actions (isolate, terminate, quarantine, remote access, artifact collection)
- Investigating a realistic multi-detection EDR scenario and tracing process chains to root cause

## Personal Notes

This was the first room in the Core SOC Solutions module. The practical scenario had multiple linked detections across different hosts (a malicious Office document as initial access, credential dumping via LSASS memory access, and suspicious execution from AppData) - good practice at using the process chain and IOC tabs together to piece together a single attacker's path across an environment rather than treating each alert in isolation.

## Practical Investigation — Portfolio-Safe Case Study

> This section documents the investigation method and conclusions in my own words. It intentionally omits room answers, flags, and exact challenge values.

### Detection A — Malicious Office Document

**Observed chain:** Office application -> Windows command shell -> native download utility -> downloaded executable.

**Evidence reviewed:**
- An Office document spawned a command shell, which is unusual for normal document use.
- The shell launched a native Windows download utility.
- The downloaded executable was written to a public user directory.
- EDR identified related network and file indicators and quarantined the payload.

**Assessment:** High-confidence true positive. The combined process ancestry, download behavior, suspicious destination path, and EDR containment action are consistent with phishing-led initial access and payload delivery.

### Detection B — Credential Dumping and Exfiltration Attempt

**Observed chain:** User shell -> unsigned executable from a temporary directory -> LSASS memory access.

**Evidence reviewed:**
- The executable accessed the memory of `lsass.exe`.
- It created a credential-dump artifact on disk.
- Persistence-related registry changes were recorded.
- The process attempted to upload the dump to an external file-transfer service.
- EDR blocked the outbound activity and quarantined the executable.

**Assessment:** Confirmed malicious credential-access activity. The strongest evidence is the combination of LSASS memory access, dump creation, persistence, and attempted exfiltration.

### Detection C — AppData Execution

**Observed chain:** User shell -> unsigned updater-like executable from a user profile directory.

**Evidence reviewed:**
- Execution from AppData and the lack of a digital signature initially raised suspicion.
- The network destination was an internal private address.
- Threat intelligence identified the program as a known internal IT utility.

**Assessment:** Likely authorized administrative activity, but the alert should only be closed after validating the software owner, expected hash, deployment record, and business purpose. A benign label is context, not proof by itself.

### MITRE ATT&CK Mapping

| Observed behavior | ATT&CK technique |
|---|---|
| Command shell launched from a document | [T1059.003 — Windows Command Shell](https://attack.mitre.org/techniques/T1059/003/) |
| Payload downloaded to the endpoint | [T1105 — Ingress Tool Transfer](https://attack.mitre.org/techniques/T1105/) |
| LSASS memory accessed for credentials | [T1003.001 — LSASS Memory](https://attack.mitre.org/techniques/T1003/001/) |
| Registry-based startup persistence | [T1547.001 — Registry Run Keys / Startup Folder](https://attack.mitre.org/techniques/T1547/001/) |
| Dump uploaded to a file-sharing service | [T1567.002 — Exfiltration to Cloud Storage](https://attack.mitre.org/techniques/T1567/002/) |

### Investigation Workflow Used

1. Read the alert summary to identify the affected host, user, severity, and detection objective.
2. Reconstruct the process tree from parent to child and inspect command lines.
3. Review hashes, paths, domains, IPs, registry changes, and signer information.
4. Check which indicators were blocked, logged, flagged, or quarantined.
5. Correlate related detections across users, hosts, and timestamps.
6. Decide the verdict from the complete behavior, not from one indicator.
7. Identify containment and escalation requirements.

### Analyst Takeaways

- Process ancestry often provides more context than a file name alone.
- An EDR response action shows what was contained; it does not replace analyst validation.
- Unsigned execution from AppData is suspicious context, but it is not automatically malicious.
- LSASS access plus dump creation and external transfer is a strong credential-theft signal.
- Related alerts should be correlated across the environment before assigning final scope.

## References

- [Microsoft: Endpoint detection and response overview](https://learn.microsoft.com/en-us/defender-endpoint/overview-endpoint-detection-response)
- [Microsoft: Take response actions on a device](https://learn.microsoft.com/en-us/defender-endpoint/respond-machine-alerts)
- [MITRE ATT&CK Enterprise techniques](https://attack.mitre.org/techniques/enterprise/)
