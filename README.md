# GhostSecure 2.1

<p align="center">
  <b>Active Directory Attack Detector for Windows</b><br>
  Runs as a background Windows service and alerts your team the moment it detects a live AD attack.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platform-Windows%20Server%20%7C%20Windows%2010%2F11-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/python-3.10%2B-yellow?style=for-the-badge&logo=python" />
  <img src="https://img.shields.io/badge/version-2.1.1-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/tests-53%20passing-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/license-MIT-purple?style=for-the-badge" />
  <img src="https://img.shields.io/badge/status-active-success?style=for-the-badge" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/detects-Kerberoasting-critical?style=flat-square" />
  <img src="https://img.shields.io/badge/detects-Pass--the--Hash-critical?style=flat-square" />
  <img src="https://img.shields.io/badge/detects-DCSync-critical?style=flat-square" />
  <img src="https://img.shields.io/badge/detects-Golden%20Ticket-critical?style=flat-square" />
  <img src="https://img.shields.io/badge/detects-AS--REP%20Roasting-critical?style=flat-square" />
  <img src="https://img.shields.io/badge/detects-LDAP%20Recon-critical?style=flat-square" />
  <img src="https://img.shields.io/badge/detects-Skeleton%20Key-critical?style=flat-square" />
</p>

---

## Overview

**GhostSecure** monitors the **Windows Security Event Log in real time** and fires an alert the moment it detects a known **Active Directory attack pattern**.

It runs silently as a **Windows service**, survives reboots, and requires no user interaction once installed.

When suspicious activity is found, GhostSecure can:

- Display an immediate **desktop popup alert**
- Send **email notifications**
- Log detections to a structured local log file
- Show live detection stats in the built-in dashboard

Built by **Egyan** | **Red Parrot Accounting Ltd**

---

## Why GhostSecure?

Active Directory attacks often leave traces in Windows Security logs before they become full-scale compromise events.

GhostSecure gives you a lightweight way to detect common attack techniques such as:

- Kerberoasting
- Pass-the-Hash
- DCSync
- Golden Ticket
- LDAP reconnaissance / BloodHound-style enumeration
- AS-REP Roasting
- Skeleton Key behavior
- Security log clearing

---

## Features

- **Runs as a Windows Service** — starts automatically and survives reboots
- **Real-time detection** — continuously monitors Windows Security Event Logs
- **Instant popup alerts** via `msg.exe`
- **Optional email alerts** using SMTP
- **No hardcoded secrets** — passwords stored in environment variables
- **Alert deduplication** — avoids alert storms with cooldown logic
- **Live status dashboard** — run with `python main.py --gui`
- **Configurable exclusions** — reduce false positives with whitelisting
- **Structured logging** to `C:\SecurityLogs\ad_attack_log.txt`
- **Tested release** — 53 unit tests across core components

---

## Attacks Detected

| Attack | Event IDs | Description |
|-------|-----------|-------------|
| **Kerberoasting** | `4769` | Unusual Kerberos service ticket requests; attacker harvesting crackable tickets offline |
| **Pass-the-Hash** | `4624` | NTLM logon without a matching prior interactive session; stolen hash may be in use |
| **DCSync (Mimikatz)** | `4662` | Replication privileges invoked by a non-DC account; attacker attempting to dump password hashes |
| **Golden Ticket** | `4768`, `4769` | TGT issued with abnormal lifetime or encryption characteristics; forged Kerberos ticket suspected |
| **LDAP Recon / BloodHound** | `1644` | Burst of LDAP queries indicating enumeration or path-mapping activity |
| **AS-REP Roasting** | `4768` | Pre-authentication not required on an account; attacker may be collecting crackable AS-REP hashes |
| **Skeleton Key** | `4673` | Sensitive privilege use on a DC; may indicate malware implant activity |
| **Audit Log Cleared** | `1102` | Security event log wiped; attacker may be covering tracks |

---

## Architecture

GhostSecure is built around a simple, defensive architecture:

1. **Read** Windows Security Event Logs continuously
2. **Match** suspicious events against detector logic
3. **Deduplicate** repeated detections to prevent noise
4. **Alert** via popup and/or email
5. **Log** detections for later review
6. **Display** activity in a live dashboard

---

## Requirements

- **Windows Server 2016+** or **Windows 10/11**
- **Domain-joined** machine
- **Python 3.10+**
- **Domain Administrator rights** for installation
- **Windows Advanced Audit Policy logging enabled**
- **pywin32** for Windows service support

---

## Installation

### 1. Install dependencies

```bash
pip install pywin32 ldap3
