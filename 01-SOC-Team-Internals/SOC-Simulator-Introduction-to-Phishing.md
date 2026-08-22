# SOC Simulator: Introduction to Phishing

**Path:** SOC Level 1 -> SOC Team Internals
**Room:** SOC Simulator: Introduction to Phishing (https://tryhackme.com/soc-sim) - Interactive simulation
**Status:** Completed (First Scenario)

## Objective

Apply everything from the module (alert triage, reporting, escalation, workbooks, metrics) in a single simulated SOC shift: triage a queue of alerts, correctly classify true/false positives, write reports, and respond in time - with the results scored against timing and accuracy metrics automatically.

## Key Concepts

### 1. It's the Module's Final Exam
Unlike the individual task-based rooms, the simulator doesn't walk you through the theory - it drops you into a live-feeling alert queue and expects the workflow (prioritize -> triage -> report -> escalate/close) to already be second nature.

### 2. Scored on the Same Metrics I Just Learned
The results screen grades performance using the exact SOC metrics from the "SOC Metrics and Objectives" room:
- True Positive identification rate
- MTTR (Mean Time to Respond)
- Dwell time (how long a threat sat active before containment)
- Per-alert close time

This made the metrics room click in a very concrete way - they're not abstract KPIs, they're literally how this run was graded.

### 3. AI-Reviewed Reporting
Report quality on true positive alerts gets automated feedback (AI-assisted), checking for:
- Clear correlation between related alerts
- Naming the specific individuals/roles involved
- Including location/system context (hostnames, network segments)
- Explicitly stating the attacker's likely intent, to support escalation and remediation decisions

This maps directly to the Five Ws framework from the Alert Reporting room - the AI feedback was essentially flagging where I under-specified Who/Where/Why.

## Skills Gained

- Running a full alert-triage workflow end-to-end under time pressure, not just per-task
- Recognizing how my own MTTR/dwell time/true-positive-rate reflect real habits, not just theory
- Writing reports that hold up to structured (AI) review - specifically getting more precise on individuals, systems, and intent

## Personal Notes

Best "did I actually learn this" checkpoint so far. First run: identified all true positives (passed), true positive rate improved to 60%, but MTTR and dwell time were slower than ideal - reports were correct but too generic on the who/where/why. Concrete next-practice-run goal: name specific systems/roles and state attacker intent explicitly in every report.
