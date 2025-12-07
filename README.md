# linux-persistence-systemd-analysis
Linux Persistence via systemd – Detection & Analysis
This project demonstrates how a threat actor can create persistence on a Linux system using a custom systemd service, and how a SOC Analyst / Blue Team investigator can detect, analyze, and respond to this technique.
The project includes:
~ A malicious persistence script
~ A custom systemd service
~ Log analysis using journalctl
~ Service enumeration using systemctl
~ A full investigation report

🚩 Red Team – The Attack
An attacker creates a persistence mechanism by placing a malicious script in the user’s home directory:
/home/kali/persist.sh

And registers a systemd service:
/etc/systemd/system/persist.service

This ensures the script runs at boot and continuously writes activity to:
/tmp/persist.log

🛡️ Blue Team – Detection Steps
1️⃣ Listing system services
A SOC analyst identifies suspicious services by running:
systemctl list-units --type=service

Indicators of compromise include:
~ Unknown service names
~ Services that run from /home/<user>
~ Recently modified service files

2️⃣ Inspecting the suspicious service
systemctl status persist
This reveals:
~ Who created the service
~ Where the executable is located
~ Whether it failed or succeeded
~ Whether it’s enabled at boot

3️⃣ Reviewing logs using journalctl
journalctl -u persist --since "15 min ago"
This reveals execution errors, spawn attempts, and malware behavior.

4️⃣ Reviewing the suspicious script
cat /home/kali/persist.sh

5️⃣ Cleaning the persistence
sudo systemctl disable persist
sudo systemctl stop persist
sudo rm /etc/systemd/system/persist.service
sudo systemctl daemon-reload

📂 Project Structure
artifacts/      # Malicious files used in the simulation
screenshots/    # Evidence screenshots
analysis/       # Full investigation report
README.md       # This documentation
