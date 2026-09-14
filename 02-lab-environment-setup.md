# Lab Environment Setup

## Overview

This section documents the construction of the lab environment used for the 
assessment. The target is a Windows 11 Pro virtual machine running inside 
UTM on a macOS host. The VM was intentionally weakened to simulate a 
poorly-maintained endpoint — the kind commonly found in small business or 
home-office environments where systems are not centrally managed and 
security updates have lapsed.

All work was performed in an isolated virtual environment that I own and 
control. No production or third-party systems were involved.

---

## 2.1 Virtual Machine Configuration

| Component | Specification |
|-----------|---------------|
| Host OS | macOS |
| Hypervisor | UTM (QEMU-based) |
| Guest OS | Windows 11 Pro |
| Version / Build | 25H2 (OS Build 26200.6584) |
| RAM Allocated | 4 GB |
| CPU Cores | 2 |
| Disk Size | 50 GB |
| Network Mode | Host-Only |
| Hostname | xhavitWS |
| Account Type | Local Account |

The **host-only network mode** was chosen so the VM could communicate 
directly with the host machine for scanning, without exposing the VM to 
the wider network. Connectivity between host and VM was verified with a 
simple `ping` before any scanning began.

*Screenshot: UTM VM configuration panel*

---

## 2.2 Base Windows 11 Installation

Windows 11 Pro was installed as a clean image inside UTM. After 
installation, the system was allowed to run through its default setup 
and was connected to the host-only network. No antivirus or endpoint 
protection was configured beyond the default Windows Defender state, 
and the machine was left with its default out-of-box configuration 
prior to weakening.

*Screenshot: Windows 11 `winver` dialog showing version 25H2 (Build 26200.6584)*

---

## 2.3 Deliberate Weaknesses Introduced

To make the assessment meaningful, three deliberate weaknesses were 
introduced into the VM. The purpose was to simulate a realistic 
unpatched and misconfigured endpoint, giving Nessus a wider attack 
surface to detect and report on.

### Weakness 1 – Uninstalled Windows Security Updates

Windows Update history was reviewed and the pending/uninstalled updates 
that were queued in the system were removed. This left the machine 
without recent security patches, exposing it to known CVEs that a 
patched system would not exhibit.

**Steps taken:**
1. Opened **Settings → Windows Update → Update history**
2. Selected **Uninstall updates**
3. Removed the updates listed in the system

*Screenshot: Windows Update → Uninstall updates page (showing "No updates found" after removal)*

### Weakness 2 – Disabled Windows Firewall (All Profiles)

The Windows Defender Firewall was turned off for all three network 
profiles — **Domain**, **Private**, and **Public** — removing the 
host-based network filtering layer entirely.

**Steps taken:**
1. Opened **Settings → Privacy & Security → Windows Security → Firewall & network protection**
2. Selected each profile individually
3. Toggled **Microsoft Defender Firewall** to **Off** for all three

*Screenshot: Firewall & network protection showing all profiles off*

### Weakness 3 – Installed Outdated Google Chrome

An older, vulnerable version of Google Chrome was installed on the VM. 
Legacy browser versions are a common finding in real-world assessments 
because users rarely update them, and they frequently expose the host 
to publicly-known browser exploits.

*Screenshot: Chrome version / About Chrome page*

---

## 2.4 Network Configuration

The VM was configured in **host-only networking mode**, meaning it can 
communicate directly with the host but is isolated from the external 
network. This provided:

- A stable, predictable IP address for Nessus to target
- Isolation from the internet to prevent accidental exposure
- Direct reachability verified with `ping` from the host to the VM

*Screenshot: Network adapter settings in UTM*

*Screenshot: Successful ping from host to VM*

---

## 2.5 Baseline State Summary

Before any scanning, the VM was in the following state:

| Aspect | State |
|--------|-------|
| Windows Updates | Removed — no security patches recently applied |
| Windows Firewall | Disabled on all profiles |
| Browser | Outdated Google Chrome (vulnerable version) |
| Network Isolation | Host-only (reachable from host only) |
| Antivirus | Default Windows Defender state |
| Remote Access | Not yet configured (see Section 03) |

This baseline represents a deliberately weakened endpoint and forms the 
starting point for the vulnerability scans documented in the following 
sections.

---

## Navigation

- [← Back to Overview](./01-project-overview.md)
- [Next: 03 – Preparing for Credentialed Scanning →](./03-preparing-for-credentialed-scanning.md)
