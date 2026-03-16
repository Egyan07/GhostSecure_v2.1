GhostSecure 2.1
<p align="center"> <img src="https://img.shields.io/github/v/release/Egyan07/GhostSecure?style=for-the-badge" alt="Release"> <img src="https://img.shields.io/github/license/Egyan07/GhostSecure?style=for-the-badge" alt="License"> <img src="https://img.shields.io/github/stars/Egyan07/GhostSecure?style=for-the-badge" alt="Stars"> <img src="https://img.shields.io/github/forks/Egyan07/GhostSecure?style=for-the-badge" alt="Forks"> <img src="https://img.shields.io/github/issues/Egyan07/GhostSecure?style=for-the-badge" alt="Issues"> <img src="https://img.shields.io/badge/platform-Windows-blue?style=for-the-badge&logo=windows" alt="Platform"> </p><p align="center"> <img src="https://img.shields.io/badge/python-3.10%2B-yellow?style=flat-square&logo=python" alt="Python"> <img src="https://img.shields.io/badge/tests-53%20passing-brightgreen?style=flat-square" alt="Tests"> <img src="https://img.shields.io/badge/status-active-success?style=flat-square" alt="Status"> <img src="https://img.shields.io/badge/focus-Active%20Directory-red?style=flat-square" alt="Active Directory"> <img src="https://img.shields.io/badge/license-MIT-purple?style=flat-square" alt="MIT License"> </p><p align="center"> <h1 align="center">GhostSecure</h1> <p align="center"><b>Active Directory Attack Detector for Windows</b></p> <p align="center"> Runs silently as a Windows service and alerts your team the moment it detects live AD attack activity. </p> </p><p align="center"> <b>Kerberoasting</b> • <b>Pass-the-Hash</b> • <b>DCSync</b> • <b>Golden Ticket</b> • <b>LDAP Recon</b> • <b>AS-REP Roasting</b> • <b>Skeleton Key</b> • <b>Audit Log Clearing</b> </p>
✨ Overview
GhostSecure is a lightweight Active Directory attack detector for Windows that continuously monitors the Windows Security Event Log in real time.

It runs as a background Windows service, survives reboots, and requires no user interaction once installed. When suspicious AD attack behavior is detected, GhostSecure can immediately:

show a desktop popup alert
send an email notification
write to a structured local log
update a live dashboard
GhostSecure is built for defenders who want fast visibility into known Active Directory attack patterns without the complexity of a full SIEM or EDR deployment.

Built by: Egyan
Company: Red Parrot Accounting Ltd

🔥 Why GhostSecure
Many Active Directory attacks leave clues in Windows event logs before full compromise is complete.

GhostSecure helps you catch those signals early by watching for known attack patterns tied to:

Kerberos abuse
credential theft activity
replication abuse
recon behavior
privilege misuse
defense evasion
Ideal for
Windows domain administrators
blue teams
MSPs
internal IT/security teams
AD lab environments
small to medium businesses needing focused detection
🚀 Features
Runs as a Windows Service

starts automatically
survives reboots
no visible window required
Real-time Security Event monitoring

continuously reads Windows Security Event Logs
triggers alerts immediately when suspicious patterns are detected
Instant desktop popup alerts

uses msg.exe to notify the admin workstation
Optional email alerts

SMTP support included
passwords stored in environment variables, never hardcoded
Alert deduplication

the same attack alert only fires once per cooldown period
avoids alert spam
Live GUI dashboard

launch with python main.py --gui
shows counts, status, and recent alerts
Configurable exclusions

whitelist trusted accounts to reduce false positives
Structured logging

logs detections to:
C:\SecurityLogs\ad_attack_log.txt
Tested release

53 unit tests included
CI-ready project structure
🛡️ Attacks Detected
Attack	Event IDs	Description
Kerberoasting	4769	Unusual Kerberos service ticket requests suggesting offline ticket harvesting
Pass-the-Hash	4624	NTLM network logon without expected matching session history
DCSync (Mimikatz)	4662	Replication privileges used by a non-DC account to request AD secrets
Golden Ticket	4768, 4769	Abnormal TGT or ticket behavior that may indicate forged Kerberos tickets
LDAP Recon / BloodHound	1644	Burst LDAP enumeration consistent with reconnaissance or attack path mapping
AS-REP Roasting	4768	Accounts with pre-authentication disabled may be targeted for offline cracking
Skeleton Key	4673	Sensitive privilege activity on a domain controller that may indicate implant behavior
Audit Log Cleared	1102	Security log clearing activity indicating possible defense evasion
⚡ Quick Start
1) Install dependencies
Bash

pip install pywin32 ldap3
2) Clone the repository
Bash

git clone https://github.com/Egyan07/GhostSecure.git
cd GhostSecure
3) Configure config.py
Edit these values:

ADMIN_HOSTNAME
KNOWN_DOMAIN_CONTROLLERS
DOMAIN_NAME
Example:

Python

ADMIN_HOSTNAME = "ADMIN-PC"
KNOWN_DOMAIN_CONTROLLERS = ["DC01", "DC02"]
DOMAIN_NAME = "REDPARROT"
4) Install the service
Right-click Install.bat and choose Run as administrator.

5) Verify it is running
cmd

sc query GhostSecure2ADDetector
Expected output includes:

text

STATE: RUNNING
6) Optional: launch the dashboard
Bash

python main.py --gui
📋 Requirements
Windows Server 2016+ or Windows 10/11
Domain-joined host
Python 3.10+
Domain Administrator rights for installation
Advanced Audit Policy enabled
pywin32
ldap3
🧰 Installation
Install Python packages
Bash

pip install pywin32 ldap3
Deploy GhostSecure
Copy or clone the project onto:

a Domain Controller, or
a domain-joined monitoring machine
Update configuration
Open config.py and set:

Python

ADMIN_HOSTNAME = "ADMIN-PC"
KNOWN_DOMAIN_CONTROLLERS = ["DC01", "DC02"]
DOMAIN_NAME = "REDPARROT"
Install as a Windows service
Right-click Install.bat and select Run as administrator.

Confirm the service status
cmd

sc query GhostSecure2ADDetector
🪵 Windows Audit Policy Configuration
GhostSecure needs Windows to actually record the events it monitors. Without the correct audit policies enabled, detections will not work.

Group Policy path
Open Group Policy Management, edit Default Domain Controllers Policy, then go to:

text

Computer Configuration
> Policies
> Windows Settings
> Security Settings
> Advanced Audit Policy Configuration
> Audit Policies
Enable Success and Failure where relevant for:
Account Logon → Audit Kerberos Authentication Service
Account Logon → Audit Kerberos Service Ticket Operations
DS Access → Audit Directory Service Access
Logon/Logoff → Audit Logon
Privilege Use → Audit Sensitive Privilege Use
System → Audit Security State Change
Then apply:

cmd

gpupdate /force
⚙️ Configuration
Review config.py before deployment.

Common settings include:

ADMIN_HOSTNAME
KNOWN_DOMAIN_CONTROLLERS
DOMAIN_NAME
ENABLE_EMAIL_ALERTS
exclusion / whitelist settings
SMTP settings
cooldown configuration
logging paths
Security reminder
Do not hardcode passwords in your config.

Use environment variables instead.

📧 Email Alerts
GhostSecure supports optional SMTP notifications.

Enable email alerts
In config.py:

Python

ENABLE_EMAIL_ALERTS = True
Set SMTP password securely
Run PowerShell as Administrator:

PowerShell

[System.Environment]::SetEnvironmentVariable(
  'GHOSTSECURE_SMTP_PASSWORD', 'YourPasswordHere', 'Machine'
)
Restart the service afterward.

LDAP password
If LDAP bind credentials are used, store that securely too:

PowerShell

[System.Environment]::SetEnvironmentVariable(
  'GHOSTSECURE_LDAP_PASSWORD', 'YourPasswordHere', 'Machine'
)
🖥️ Dashboard
Launch the GUI dashboard with:

Bash

python main.py --gui
The dashboard shows:

live detection counts
service status
recent alerts
runtime activity summary
📸 Screenshots
Create a folder like this if you want screenshots to appear in the README:

text

docs/
└── screenshots/
    ├── dashboard.png
    ├── popup-alert.png
    └── log-view.png
Then add these lines to render them:

Markdown

![GhostSecure Dashboard](docs/screenshots/dashboard.png)
![GhostSecure Alert](docs/screenshots/popup-alert.png)
![GhostSecure Logs](docs/screenshots/log-view.png)
✅ Running Tests
GhostSecure includes 53 unit tests covering detectors and core utilities.

Install pytest
Bash

pip install pytest
Run tests
Bash

pytest tests/ -v
Coverage includes
detectors
alert manager
time helpers
AD helpers
📁 Project Structure
text

GhostSecure/
├── main.py                    # Entry point — service + CLI + GUI launcher
├── config.py                  # Main configuration file
├── Install.bat                # Installs and starts the Windows service
├── Uninstall.bat              # Stops and removes the Windows service
├── build.bat                  # Packages into standalone executable
├── core/
│   ├── alert_manager.py       # Alert dispatch, deduplication, logging
│   ├── detector_engine.py     # Detector orchestration engine
│   └── event_reader.py        # Windows Security Event Log reader
├── detectors/
│   ├── kerberoasting.py
│   ├── pass_the_hash.py
│   ├── dcsync.py
│   ├── golden_ticket.py
│   ├── ldap_recon.py
│   ├── asrep_roasting.py
│   └── skeleton_key.py
├── utils/
│   ├── ad_helpers.py          # LDAP / AD helper methods
│   └── time_helpers.py
├── gui/
│   └── status_dashboard.py    # Live GUI dashboard
└── tests/
    ├── test_detectors.py
    ├── test_alert_manager.py
    ├── test_time_helpers.py
    └── test_ad_helpers.py
🧠 Architecture
GhostSecure follows this detection flow:

text

Windows Security Event Log
            ↓
       Event Reader
            ↓
      Detector Engine
            ↓
      Attack Detectors
            ↓
      Alert Manager
      ↙     ↓      ↘
  Popup   Email    Logs
            ↓
        Dashboard
Core logic
Read new Security Event Log entries
Parse and normalize events
Pass events through detector modules
Deduplicate repeated alerts
Trigger alert actions
Log and display detections
🗑️ Uninstall
To remove GhostSecure:

Right-click Uninstall.bat and choose Run as administrator.

This will:

stop the service
remove the service registration
preserve logs in C:\SecurityLogs\
❓ FAQ
Does GhostSecure block attacks?
No. GhostSecure is a detection and alerting tool, not a prevention product.

Does it replace SIEM or EDR?
No. It complements them by providing focused AD attack detection.

Can it run on a workstation instead of a Domain Controller?
Yes, provided the host is domain-joined, properly permissioned, and has access to the relevant logs.

Is it noisy?
It includes deduplication and exclusions to reduce repeated or known-benign alerts.

Does it require internet access?
No, not for core operation. Internet is only needed for services like external SMTP.

Where are logs stored?
By default:

text

C:\SecurityLogs\ad_attack_log.txt
🤝 Contributing
Contributions are welcome.

You can help by:
improving detector logic
reducing false positives
adding new AD attack detections
improving the GUI/dashboard
improving documentation
adding tests
reporting bugs and edge cases
Suggested workflow
Fork the repository
Create a feature branch
Make your changes
Add or update tests
Submit a pull request
If you add a new detector, include:

monitored event IDs
detection logic summary
false positive notes
unit tests
💬 Support
If you find a bug or want to request a feature, open an issue:

Issues: https://github.com/Egyan07/GhostSecure/issues
Repository: https://github.com/Egyan07/GhostSecure
📝 Changelog
v2.1.1 (current)
Fixed

Smart/curly quotes used as string delimiters causing SyntaxError on Python 3.10+
starttls() called without SSL context; now uses ssl.create_default_context()
Self-test checked for GhostSecure 2.0 while config was 2.1
14 files still referenced 2.0 in headers
EMAIL_SUBJECT_PREFIX updated from 2.0 to 2.1
build.bat, Install.bat, and Uninstall.bat updated from 2.0 references
Silent exception swallowing in status_dashboard.py replaced with debug logging
Encoding issues in user-facing alert strings cleaned up
Added

tests/test_detectors.py — 29 tests across all 7 detectors
tests/test_alert_manager.py — 6 tests
tests/test_time_helpers.py — 12 tests
tests/test_ad_helpers.py — 6 tests
GitHub Actions CI workflow
flake8, syntax checks, and test coverage pipeline
Result

53 total unit tests, all passing
v2.1
Fixed

SMTP password now read from GHOSTSECURE_SMTP_PASSWORD
alerts_triggered counter now works correctly
Golden Ticket tracker made thread-safe
Pass-the-Hash tracker made thread-safe
LDAP auth switched to SASL/GSSAPI
GUI dashboard no longer freezes
Pass-the-Hash false positives reduced
Event reader now tracks EventRecordID
Audit log clear exclusions added
LDAP bind password moved to GHOSTSECURE_LDAP_PASSWORD
v2.0
Initial release:

8 AD attack detectors
Windows service architecture
popup alerting
dashboard support
structured logging
⚠️ Disclaimer
GhostSecure is an early warning system, not a complete security platform.

It does not:

block attacks
replace endpoint protection
replace SIEM
replace professional incident response
guarantee full compromise detection
It should be used as one layer in a broader defense strategy.

Always investigate any alert generated by GhostSecure.

Compliance note
Under GDPR / ICO guidance, security logs should generally be retained for at least 12 months, depending on your environment and legal obligations.

📄 License
Licensed under the MIT License.

Free to use, modify, and distribute.

👤 Credits
Built by Egyan
Red Parrot Accounting Ltd

<p align="center"> <a href="https://github.com/Egyan07/GhostSecure">GitHub Repository</a> • <a href="https://github.com/Egyan07/GhostSecure/issues">Report an Issue</a> </p>
