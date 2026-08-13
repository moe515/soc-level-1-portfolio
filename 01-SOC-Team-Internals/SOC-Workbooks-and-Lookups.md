# SOC Workbooks and Lookups

**Path:** SOC Level 1 -> SOC Team Internals
**Room:** SOC Workbooks and Lookups (https://tryhackme.com/room/socworkbookslookups) - Entry level
**Status:** Completed

## Objective

Learn how to use identity/asset inventories, network diagrams, and structured SOC workbooks to gather context quickly and triage alerts consistently.

## Key Concepts

### 1. Identity Inventory
A catalog of corporate employees (user accounts), services (machine accounts), and their details: roles, contacts, privileges, and access. Helps quickly answer "who is this person, and should they have this access?" during triage.

Common sources: Active Directory / Entra ID, SSO providers (Okta, Google Workspace), HR systems (BambooHR, SAP, HiBob), or custom CSV/Excel solutions.

### 2. Asset Inventory
Also called asset lookup - a list of computing resources in the environment (servers, workstations) with hostname, location, IP, OS, owner, and purpose. Helps quickly answer "what is this host, and is this activity expected for it?"

Common sources: Active Directory, SIEM/EDR agents, MDM solutions (Intune, Jamf), or custom CSV/Excel solutions.

### 3. Network Diagrams
A visual map of an organization's locations, subnets, and how they connect. Useful for reconstructing an attack path from raw logs, for example tracing an external actor through a firewall into a VPN subnet, then into internal subnets like databases or office systems, showing where an attack likely started and what it touched next.

### 4. SOC Workbooks (Playbooks / Runbooks)
A structured document defining the exact steps to investigate and remediate a specific type of alert. Since L1 analysts aren't expected to know every possible attack scenario, senior analysts prepare workbooks so junior analysts triage consistently and don't miss steps.

Workbooks generally follow three stages:
1. **Enrichment** - gather context on the user/host involved (identity inventory, threat intel, asset inventory)
2. **Investigation** - use SIEM/EDR data to determine if the activity is expected or malicious
3. **Escalation** - write the report and escalate to L2, or close as a false positive with justification

### 5. Applying Workbooks
Practiced following workbook-style decision flows for three common scenarios:
- External email with a script or binary attachment: investigate sender/attachment, decide if malicious, escalate or close
- Executable file downloaded via PowerShell: check the download source and process tree, decide if malicious, escalate or close
- Port scanning from an internal IP: check the source IP context and scanning pattern, decide if expected or malicious, escalate or close

## Skills Gained

- Using identity and asset inventories to get investigation context quickly
- Reading network diagrams to reconstruct attack paths across subnets
- Following structured workbooks (Enrichment -> Investigation -> Escalation) for consistent triage
- Recognizing when a workbook doesn't exist and defaulting to the same three-stage thinking anyway

## Personal Notes

This ties directly into the Investigation Checklist I already have - workbooks are basically a formalized, threat-specific version of that same checklist. Worth building my own mini-workbooks for the attack types I see most.
