GhostSecure 2.1
<p align="center"> <img src="https://img.shields.io/github/v/release/Egyan07/GhostSecure?style=for-the-badge" alt="Release"> <img src="https://img.shields.io/github/license/Egyan07/GhostSecure?style=for-the-badge" alt="License"> <img src="https://img.shields.io/github/actions/workflow/status/Egyan07/GhostSecure/ci.yml?branch=main&style=for-the-badge" alt="CI"> <img src="https://img.shields.io/github/stars/Egyan07/GhostSecure?style=for-the-badge" alt="Stars"> <img src="https://img.shields.io/github/forks/Egyan07/GhostSecure?style=for-the-badge" alt="Forks"> <img src="https://img.shields.io/github/issues/Egyan07/GhostSecure?style=for-the-badge" alt="Issues"> </p><p align="center"> <img src="https://img.shields.io/badge/platform-Windows-blue?style=flat-square&logo=windows" alt="Platform"> <img src="https://img.shields.io/badge/python-3.10%2B-yellow?style=flat-square&logo=python" alt="Python"> <img src="https://img.shields.io/badge/tests-53%20passing-brightgreen?style=flat-square" alt="Tests"> <img src="https://img.shields.io/badge/status-active-success?style=flat-square" alt="Status"> <img src="https://img.shields.io/badge/domain-Active%20Directory-red?style=flat-square" alt="Active Directory"> </p><p align="center"> <b>Active Directory Attack Detector for Windows</b><br> Runs silently as a background Windows service and alerts your team the moment it detects live AD attack activity. </p><p align="center"> Detects <b>Kerberoasting</b> • <b>Pass-the-Hash</b> • <b>DCSync</b> • <b>Golden Ticket</b> • <b>LDAP Recon</b> • <b>AS-REP Roasting</b> • <b>Skeleton Key</b> • <b>Audit Log Clearing</b> </p>
Contents
Overview
Why GhostSecure
Key Features
Attacks Detected
Quick Start
Requirements
Installation
Windows Audit Policy Configuration
Configuration
Email Alerts
Dashboard
Screenshots
Running Tests
Project Structure
Architecture
Uninstall
FAQ
Contributing
Support
Changelog
Disclaimer
License
Credits
Overview
GhostSecure is a lightweight Active Directory attack detector for Windows that continuously monitors the Windows Security Event Log in real time.

It is designed to run as a background Windows service, requiring no user interaction after installation. When suspicious AD attack behavior is detected, GhostSecure can immediately:

display a desktop popup alert
send an email notification
write to a local structured log
update a live monitoring dashboard
GhostSecure is ideal as an early warning layer for AD environments that want quick visibility into known attack techniques without deploying a full SIEM or EDR stack.

Built by Egyan | Red Parrot Accounting Ltd

Why GhostSecure
Many Active Directory attack techniques generate recognizable Windows event patterns before privilege escalation, credential theft, or domain compromise is complete.

GhostSecure focuses on detecting those patterns early.

Good fit for:
Small to mid-sized Windows environments
Internal security teams
MSP / IT administrators
Domain Controller monitoring
Lab and research environments
Blue team visibility and alerting
Useful when you want:
simple deployment
immediate on-screen alerts
minimal operational overhead
focused AD attack detection
a service-based detector rather than a full platform
Key Features
Windows Service based

starts automatically
survives reboots
runs silently in the background
Real-time event monitoring

continuously reads the Windows Security Event Log
detects suspicious activity as it happens
Attack-specific detection logic

built-in detectors for common AD attack techniques
Popup desktop alerts

uses msg.exe to notify the admin workstation immediately
Optional email notifications

SMTP supported
passwords stored in environment variables, never hardcoded
Alert deduplication

the same alert only triggers once within a cooldown window
reduces noise and alert storms
Dashboard mode

launch with python main.py --gui
shows live service and detection status
Configurable exclusions

whitelist trusted accounts to reduce false positives
Structured logging

writes detections to:
C:\SecurityLogs\ad_attack_log.txt
Tested codebase

53 unit tests included
CI workflow for Python 3.10 / 3.11 / 3.12
Attacks Detected
Attack	Event IDs	Description
Kerberoasting	4769	Unusual Kerberos service ticket requests suggesting offline ticket harvesting
Pass-the-Hash	4624	NTLM network logon without expected matching session history
DCSync (Mimikatz)	4662	Replication privileges used by a non-DC account to request AD secrets
Golden Ticket	4768, 4769	Abnormal TGT or ticket behavior that may indicate forged Kerberos tickets
LDAP Recon / BloodHound	1644	Burst LDAP enumeration consistent with reconnaissance or attack path mapping
AS-REP Roasting	4768	Accounts with pre-authentication disabled may be targeted for offline cracking
Skeleton Key	4673	Sensitive privilege activity on a domain controller that may indicate implant behavior
Audit Log Cleared	1102	Security log clearing activity indicating possible defense evasion
Quick Start
1. Install dependencies
Bash

pip install pywin32 ldap3
2. Clone the repository
Bash

git clone https://github.com/Egyan07/GhostSecure.git
cd GhostSecure
3. Configure config.py
Set:

ADMIN_HOSTNAME
KNOWN_DOMAIN_CONTROLLERS
DOMAIN_NAME
4. Install the Windows service
Right-click Install.bat and choose Run as administrator.

5. Verify the service
cmd

sc query GhostSecure2ADDetector
6. Launch the dashboard optionally
Bash

python main.py --gui
Requirements
Windows Server 2016+ or Windows 10/11
Domain-joined system
Python 3.10+
Domain Administrator rights for installation
Advanced Audit Policy configured correctly
pywin32
ldap3
Installation
Install Python packages
Bash

pip install pywin32 ldap3
Deploy the project
Copy or clone the GhostSecure folder to:

a Domain Controller, or
another domain-joined monitoring host
Edit config.py
Open config.py and configure:

Python

ADMIN_HOSTNAME = "ADMIN-PC"
KNOWN_DOMAIN_CONTROLLERS = ["DC01", "DC02"]
DOMAIN_NAME = "REDPARROT"
Install as a service
Right-click:

text

Install.bat
Then select:

text

Run as administrator
Verify service health
cmd

sc query GhostSecure2ADDetector
Expected:

text

STATE: RUNNING
Windows Audit Policy Configuration
GhostSecure depends on Windows security audit logs. Without the correct policies enabled, detections will not work.

Configure Group Policy
Open:

Group Policy Management
Edit Default Domain Controllers Policy
Navigate to:

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
Apply the changes:

cmd

gpupdate /force
Configuration
Before deployment, review config.py.

Important values include:

ADMIN_HOSTNAME
KNOWN_DOMAIN_CONTROLLERS
DOMAIN_NAME
ENABLE_EMAIL_ALERTS
detector exclusions / whitelist settings
email settings
cooldown timing
log locations
Security note
Do not hardcode passwords in config.py.

Use environment variables instead.

Email Alerts
GhostSecure supports optional SMTP email alerts.

Enable email alerts
In config.py:

Python

ENABLE_EMAIL_ALERTS = True
Configure SMTP settings
Add your SMTP server details in config.py.

Store SMTP password securely
Run PowerShell as Administrator:

PowerShell

[System.Environment]::SetEnvironmentVariable(
  'GHOSTSECURE_SMTP_PASSWORD', 'YourPasswordHere', 'Machine'
)
Restart the service after setting the variable.

LDAP password
If needed, also store the LDAP bind password securely:

PowerShell

[System.Environment]::SetEnvironmentVariable(
  'GHOSTSECURE_LDAP_PASSWORD', 'YourPasswordHere', 'Machine'
)
Dashboard
Start the GUI dashboard with:

Bash

python main.py --gui
The dashboard provides:

live alert counts
service status
recent detections
runtime summary information
This is useful for operators who want a simple local monitoring view without opening logs manually.

Screenshots
Add screenshots here to make the project page more visual and professional.

Dashboard
Markdown

![GhostSecure Dashboard](docs/screenshots/dashboard.png)
Popup Alert Example
Markdown

![GhostSecure Alert](docs/screenshots/popup-alert.png)
Event / Log Example
Markdown

![GhostSecure Logs](docs/screenshots/log-view.png)
If you create a docs/screenshots/ folder and add images, these will render automatically.

Running Tests
GhostSecure ships with 53 unit tests covering detectors and core components.

Install pytest
Bash

pip install pytest
Run the test suite
Bash

pytest tests/ -v
Coverage areas
detectors
alert manager
time helpers
AD helpers
Project Structure
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
Architecture
GhostSecure follows a simple detection pipeline:

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
Core flow
Read new Security Event Log entries
Parse and normalize events
Pass events through detector modules
Deduplicate repeated alerts
Trigger response actions
Record activity in logs and dashboard
Uninstall
To remove GhostSecure:

Right-click:

text

Uninstall.bat
Choose:

text

Run as administrator
This will:

stop the service
remove the service registration
preserve log files in C:\SecurityLogs\
FAQ
Does GhostSecure block attacks?
No. GhostSecure is a detection and alerting tool, not a prevention product.

Does it replace SIEM or EDR?
No. It complements them by providing focused AD attack detection.

Can it run on a workstation instead of a Domain Controller?
Yes, as long as the host is domain-joined, properly permissioned, and has access to the relevant logs.

Is it noisy?
It includes deduplication and exclusions to reduce repeated or known-benign alerts.

Does it require internet access?
No, not for core operation. Internet access is only needed if using remote services such as external SMTP.

Where are logs stored?
By default:

text

C:\SecurityLogs\ad_attack_log.txt
Contributing
Contributions are welcome.

Ways to contribute
improve detector logic
reduce false positives
add new AD attack detections
improve GUI/dashboard experience
add documentation
improve tests and CI
report bugs and edge cases
Basic workflow
Fork the repository
Create a feature branch
Make your changes
Add or update tests
Submit a pull request
If you add a new detector, please also include:

event IDs monitored
detection logic summary
false positive notes
tests
Support
If you find a bug or want to request a feature, open an issue:

GitHub Issues:
https://github.com/Egyan07/GhostSecure/issues

Repository:
https://github.com/Egyan07/GhostSecure

Changelog
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
Disclaimer
GhostSecure is an early warning system, not a complete security platform.

It does not:

block attacks
replace endpoint protection
replace SIEM
replace professional incident response
guarantee full compromise detection
It is best used as one part of a layered defense strategy.

Always investigate any alert generated by GhostSecure.

Compliance note
Under GDPR / ICO guidance, security logs should generally be retained for at least 12 months, depending on your environment and legal obligations.

License
Licensed under the MIT License.

Free to use, modify, and distribute.

Credits
Built by Egyan
Red Parrot Accounting Ltd

<p align="center"> <a href="https://github.com/Egyan07/GhostSecure">GitHub Repository</a> • <a href="https://github.com/Egyan07/GhostSecure/issues">Report an Issue</a> </p>
