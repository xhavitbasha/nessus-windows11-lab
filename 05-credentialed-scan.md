# Credentialed Scan (Authenticated)

## Overview

This section documents the second scan performed against the target VM — 
a **credentialed scan**, in which valid Windows credentials were supplied 
to Nessus so it could authenticate to the host and inspect it from the 
inside.

The purpose of this scan was to demonstrate how much deeper a credentialed 
scan reaches compared to the unauthenticated scan in Section 04. Nessus 
was able to read the registry, enumerate installed software, and check 
patch levels directly — capabilities that are impossible without valid 
credentials.

The two scans use the **same target, same policy, and same scanner** — the 
only variable changed was the presence of credentials. This makes the 
comparison in Section 06 a clean, controlled experiment.

---

## 5.1 Scan Configuration

| Property | Value |
|----------|-------|
| Scan Name | Windows 11 – Credentialed |
| Policy | Basic Network Scan |
| Target | 192.168.1.15 |
| Scanner | Local Scanner |
| Severity Base | CVSS v3.0 |
| Credentials | Windows (SMB), local administrator account |
| Date | September 14 |
| Duration | ~14 minutes |

The same **Basic Network Scan** policy from Section 04 was reused. Only 
the Credentials tab was populated — everything else was unchanged.

*Screenshot: Scan configuration showing credentials configured*

---

## 5.2 Credential Setup

Windows credentials were provided to Nessus under the **Windows → SMB** 
category. Nessus uses SMB to authenticate to the host and then leverages 
the Remote Registry service and WMI to perform local checks.

For the credentials to work, three Windows settings had to be modified 
first — documented in Section 03:

- Remote Registry service enabled (Automatic)
- UAC set to Never Notify
- `LocalAccountTokenFilterPolicy` set to `1`

Once these were in place, Nessus authenticated successfully (Auth column 
in the scan results shows **Pass**).

---

## 5.3 Results Summary

The credentialed scan returned **48 distinct vulnerabilities**, with a 
much larger total finding count when the per-vulnerability occurrence 
counts are included. The severity distribution was:

| Severity | Distinct Findings |
|----------|-------------------|
| Critical | 1 |
| High | 1 |
| Mixed (Bulletins) | 2 entries, 14 findings total |
| Medium | Included in Mixed above |
| Low | 1 |
| Info | 43 |
| **Total distinct** | **48** |

Compared to the unauthenticated scan (0 Critical, 0 High), this is a 
dramatic difference. **The single most important finding is the Critical 
CVSS 9.8 vulnerability — something the unauthenticated scan could not 
detect at all.**

*Screenshot: Severity donut chart from the credentialed scan*

---

## 5.4 Critical Finding

### Security Updates for Microsoft .NET Framework

| Property | Value |
|----------|-------|
| Risk Factor | Critical |
| CVSS v3.0 Base Score | 9.8 |
| CVSS v3.0 Vector | AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H |
| CVSS v3.0 Temporal Score | 8.8 |
| CVSS v2.0 Base Score | 10.0 |
| VPR | 6.9 |
| EPSS | 0.0158 |
| IAVM Severity | I |
| Family | Windows : Microsoft Bulletins |

The remote Windows host is missing security updates for the Microsoft 
.NET Framework. The CVSS vector **AV:N/AC:L/PR:N/UI:N** indicates this 
is:

- **AV:N** — Exploitable over the network (remotely)
- **AC:L** — Low attack complexity
- **PR:N** — No privileges required
- **UI:N** — No user interaction required

In plain terms: **an unauthenticated attacker on the same network could 
potentially exploit this to achieve full compromise of the host — 
confidentiality, integrity, and availability are all rated High impact.**

This is exactly the kind of finding a credentialed scan exists to 
surface. Without credentials, Nessus had no way to determine whether 
the .NET Framework on the host was up to date — because that information 
is only visible from inside the operating system.

> **Remediation:** Apply the latest .NET Framework security updates via 
> Windows Update or WSUS. Verify the patch level after installation with 
> `Get-HotFix` or by re-scanning.

*Screenshot: Critical finding detail page in Nessus*

---

## 5.5 High Finding

### Windows Package Manager (WinGet) Elevation of Privilege

| Property | Value |
|----------|-------|
| Risk Factor | High |
| CVSS v3.0 Base Score | 7.3 |
| CVE | CVE-2026-68821 |
| Family | Windows : Microsoft Bulletins |

The version of **Windows Package Manager (WinGet)** installed on the host 
is prior to `1.30.80` and is affected by an elevation-of-privilege 
vulnerability. A local attacker with low privileges can exploit this to 
gain elevated (administrator) privileges on the host.

**Why this matters:** Elevation-of-privilege findings are often the 
second stage of an attack chain. An attacker who has already obtained 
a low-privilege foothold (for example, through a phishing email or a 
compromised browser) can use this vulnerability to become a full local 
administrator — enabling persistence, credential theft, and lateral 
movement.

> **Remediation:** Update WinGet to version `1.30.80` or later. This is 
> typically delivered via the Microsoft Store or through the App Installer 
> package.

*Screenshot: WinGet finding detail page in Nessus*

---

## 5.6 Missing Patch Detection

Beyond individual findings, the credentialed scan also flagged that the 
host is missing **security update 5066835**, which addresses multiple 
known vulnerabilities including:

- **CVE-2016-9535** — libtiff Predictor heap-buffer-overflow (buffer overflow in image parsing)
- **CVE-2025-2884** — TPM 2.0 reference implementation Out-of-Bounds read
- **CVE-2025-47827** — IGEL OS Secure Boot bypass

Additionally, two **Mixed-severity bulletin entries** (7 findings each, 
14 total) were reported under the Microsoft Bulletins family, indicating 
further missing patches affecting the system.

This is the core value of credentialed scanning: Nessus was able to 
read the **installed patch level directly from the registry**, compare 
it against Microsoft's update catalog, and identify specific missing 
knowledge base articles. None of this is possible without credentials.

> **Remediation:** Apply all outstanding Windows updates. Prioritize 
> 5066835 and any bulletins with Critical or High severity. Re-scan 
> after patching to confirm remediation.

*Screenshot: Missing patch / bulletin findings list*

---

## 5.7 Low Finding

### ICMP Timestamp Request Remote Date Disclosure

The same Low-severity finding from the unauthenticated scan (Section 04) 
was reported again. This is expected — it is a network-layer observation 
that credentialed access does not change.

| Property | Value |
|----------|-------|
| Risk Factor | Low |
| CVSS v2.0 Base Score | 2.1 |
| VPR | 3.2 |
| EPSS | 0.3159 |

See Section 4.3 for the full description and remediation guidance.

---

## 5.8 Observations

Compared to the unauthenticated scan in Section 04, the credentialed scan:

**Revealed:**
- 1 Critical (CVSS 9.8) missing .NET Framework update
- 1 High (CVSS 7.3) WinGet privilege escalation (CVE-2026-68821)
- Missing Windows security update 5066835
- Two Mixed-severity Microsoft Bulletins (14 findings combined)
- Full software inventory and patch state of the host

**Confirmed:**
- The host is a live Windows system reachable at 192.168.1.15
- Both the DHCP and ICMP timestamp observations from the unauthenticated scan

**Enabled:**
- Accurate determination of which specific patches are missing
- Direct reading of registry-based configuration
- Enumeration of installed applications (including the outdated Chrome noted in Section 02)

The credentialed scan found vulnerabilities at severities up to Critical 
that the unauthenticated scan could not see at all. This is the central 
lesson of the project, and it is examined in detail in Section 06.

---

## Navigation

- [← Back: 04 – Basic Network Scan](./04-basic-network-scan.md)
- [Next: 06 – Findings & Analysis →](./06-findings-analysis.md)
