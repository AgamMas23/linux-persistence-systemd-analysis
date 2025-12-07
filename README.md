**Linux Persistence via systemd – Detection & Analysis**
A Practical SOC Investigation Project
This project demonstrates how a threat actor can establish Linux persistence using a malicious custom systemd service, and how a SOC Analyst detects, analyzes, and removes that persistence.
**It includes:**
~ A malicious persistence script
~ A custom systemd service (persist.service)
~ Service enumeration using systemctl
~ Log analysis with journalctl
~ A full professional investigation report

**Project Goal**
To simulate a real-world scenario where an attacker plants a persistence mechanism on a Linux system, and the Blue Team must:
~ Discover the suspicious service
~ Analyze its behavior and purpose
~ Review logs and artifacts
~ Mitigate and remove the persistence
This mirrors the workflow expected from a SOC Tier 1–2 analyst.

🚩 Red Team (Attacker) Action
The attacker deploys:
🔹 A malicious script
**Located at:**
/home/kali/persist.sh
**This script runs indefinitely and writes periodic activity to:**
/tmp/persist.log
🔹 A malicious systemd service
**Installed at:**
/etc/systemd/system/persist.service
**Service definition:**
~ Loads at system boot
~ Executes the malicious script
~ Provides long-term persistence
**The service successfully starts:**
Started persist.service - Suspicious persistence service.

🛡️ Blue Team (SOC Analyst) Investigation
1️⃣ Identifying suspicious services
systemctl list-units --type=service
**The analyst detects a suspicious service:**
persist.service   loaded   active   running
Why suspicious?
~ Unknown name
~ Executable located in /home/kali/ (never legitimate for systemd services)
~ Enabled on boot

2️⃣ Inspecting the service
systemctl status persist
**Findings:**
~ Service is active and running
~ ExecStart points to a non-standard location
~ Indicates attacker-created persistence

3️⃣ Log Analysis
journalctl -u persist --since "15 min ago"
**The logs show:**
~ The service started successfully
~ The malicious script is running in the background
~ Persistence mechanism is functioning

4️⃣ Reviewing the malicious script
cat /home/kali/persist.sh
**Behavior:**
~ Infinite loop
~ Periodic writes to /tmp/persist.log
~ Common tactic for basic malware heartbeat or signaling

5️⃣ Removing the persistence
**Cleanup details appear in the full report:**
analysis/report.md

📂 Project Structure
artifacts/      # Malicious files (service + script)
screenshots/    # Evidence screenshots
analysis/       # Full investigation report
README.md       # Project summary

📑 Full Report

The complete forensic analysis—including discovery, investigation, artifacts, logs, and remediation steps—is available in:

➡ analysis/report.md
