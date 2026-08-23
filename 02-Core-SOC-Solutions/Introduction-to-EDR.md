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
