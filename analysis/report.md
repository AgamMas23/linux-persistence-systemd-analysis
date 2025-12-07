Linux Persistence via systemd – Full Investigation Report
Forensic Analysis | SOC Investigation | Linux Persistence
1. Executive Summary
During routine threat hunting on a Linux workstation, a suspicious systemd service named persist.service was identified.
The service executed a script located in a non-standard directory (/home/kali/), and maintained continuous activity by writing to /tmp/persist.log.
The investigation confirmed that the service was malicious and designed to provide attacker persistence.

2. Indicators of Compromise (IOCs)
**Malicious Script:**	/home/kali/persist.sh
**Malicious Service:**	/etc/systemd/system/persist.service
**Persistence Target:**	Enabled at boot
**Output Artifact:**	/tmp/persist.log
**Behavior:**	Infinite loop with periodic writes

3. Detailed Timeline
**T0 – Routine auditing begins**
**SOC analyst runs:**
systemctl list-units --type=service
**Finds:**
persist.service  loaded  active  running
**T5 – Service flagged as suspicious**
**Reasons:**
~ Unknown service name
~ Running from user directory /home/kali/
~ Automatically enabled
Screenshot: service_list.png

4. Service Inspection
**Command:**
systemctl status persist
**Findings:**
~ Service is active
~ ExecStart points to: /home/kali/persist.sh
~ Description: “Suspicious persistence service”
~ Runtime confirmed
Screenshot: serviceList.png

5. Log Investigation
**Command:**
journalctl -u persist --since "15 min ago"
**Observations:**
~ Service successfully started
~ No system errors
~ Malicious script executed normally
~ Persistence mechanism functional
Screenshot: journalctl_logs.png

6. Malicious Script Analysis
**Contents:**
#!/bin/bash
while true; do
  echo "[+] suspicious ping" >> /tmp/persist.log
  sleep 5
done
**Behavior summary:**
~ Runs indefinitely
~ Creates ongoing heartbeat-like entries
~ Typical persistence or beaconing mechanism
~ Not a legitimate system component

7. Persistence Mechanism Breakdown
**The attacker achieved persistence by:**
1) Placing an executable script in /home/kali/
2) Creating a systemd service that:
   ~ Runs the script
   ~ Is enabled on boot
   ~ Has root-level execution via systemd
  This technique is commonly used by malware and APT groups due to:
   ~ High reliability
   ~ Integration with OS startup
   ~ Low visibility to inexperienced admins

8. Remediation Steps
**The following steps were taken to remove the persistence:**
sudo systemctl stop persist
sudo systemctl disable persist
sudo rm /etc/systemd/system/persist.service
sudo rm /home/kali/persist.sh
sudo systemctl daemon-reload
**Verification:**
systemctl status persist
**Result:**
Unit persist.service could not be found.
**Persistence fully removed.**

9. Conclusion
This investigation demonstrates a realistic attacker technique for achieving Linux persistence through systemd.
**The analyst performed:**
~ Enumeration of services
~ Identification of anomalies
~ Deep inspection of service files and script behavior
~ Log analysis
~ Complete removal of malicious components
**This project shows strong SOC-level skills in:**
~ Threat hunting
~ Linux internals
~ Service auditing
~ Malware behavior analysis
~ Persistence detection
~ Incident remediation

**End of Report !**
