# Basic Network Scan (Unauthenticated)

## Overview

This section documents the first scan performed against the target VM — 
a **basic network scan with no credentials provided**. Nessus probes the 
host from the outside, using only what it can learn from exposed network 
services, banners, and protocol responses.

The purpose of this scan was to establish a baseline of what an external 
attacker (or a scanner with no internal access) can see about the target. 
It sets up the comparison with the credentialed scan documented in 
Section 05.

---

## 4.1 Scan Configuration

| Property | Value |
|----------|-------|
| Scan Name | Windows 11 – Unauthenticated |
| Policy | Basic Network Scan |
| Target | 192.168.1.15 |
| Scanner | Local Scanner |
| Severity Base | CVSS v3.0 |
| Credentials | None provided |
| Date | September 14 |
| Duration | ~13 minutes |

The **Basic Network Scan** template was used with default settings. The 
Credentials tab was left completely empty, ensuring the scan ran purely 
from an external perspective.

*Scan configuration showing no credentials*

<img width="1096" height="578" alt="Screenshot 2026-09-15 at 13 52 58" src="https://github.com/user-attachments/assets/f4fc2315-4557-458e-8bc9-accf0ff05cae" />




---

## 4.2 Results Summary

Nessus returned **15 vulnerabilities** against the host. The severity 
distribution was:

| Severity | Count |
|----------|-------|
| Critical | 0 |
| High | 0 |
| Medium | 0 |
| Low | 2 |
| Info | 13 |
| **Total** | **15** |

The dominant severity was **Informational**, with only two Low-severity 
findings and nothing above Low. This is typical of unauthenticated scans: 
without the ability to log in, Nessus can only observe what services the 
host exposes and how they respond.

*Severity donut chart from the unauthenticated scan*

<img width="298" height="223" alt="Screenshot 2026-09-15 at 13 51 51" src="https://github.com/user-attachments/assets/72d25ce4-89c7-4280-a19b-641bd3b96731" />

---

## 4.3 Findings

### Low-Severity Findings

#### DHCP Server Detection

| Property | Value |
|----------|-------|
| Risk Factor | Low |
| CVSS v2.0 Base Score | 3.3 |
| CVSS v2.0 Vector | AV:A/AC:L/Au:N/C:P/I:N/A:N |
| Family | Service detection |

A DHCP server was detected on the target host. While not a vulnerability 
on its own, this is notable because rogue or unintended DHCP servers can 
be abused in network attacks — for example, to redirect traffic, perform 
man-in-the-middle positioning, or cause denial of service by handing out 
incorrect network configurations. On a workstation, this behavior is 
unexpected and worth investigating.

#### CMP Timestamp Request Remote Date Disclosure

| Property | Value |
|----------|-------|
| Risk Factor | Low |
| CVSS v2.0 Base Score | 2.1 |
| CVSS v2.0 Vector | AV:L/AC:L/Au:N/C:P/I:N/A:N |
| VPR | 3.2 |
| EPSS | 0.3159 |
| Family | General |

The host responds to ICMP timestamp requests, which discloses the system 
clock to remote parties. This information alone is low-risk, but an 
accurate system time can assist an attacker in fingerprinting the OS, 
correlating log events, or timing other attacks. Best practice is to 
block ICMP timestamp requests at the firewall.

### Informational Findings

The remaining 13 findings were informational and included:

- Service and version detection results
- Port scanner identification (Nessus SYN Scanner, etc.)
- SNMP agent detection
- Web server identification
- General host information and settings

These are not vulnerabilities in themselves — they are the raw 
observations Nessus collected while probing the host from the outside.

*Vulnerability list from the unauthenticated scan*

<img width="1575" height="1037" alt="Screenshot 2026-09-15 at 13 51 25" src="https://github.com/user-attachments/assets/8f6685f5-027f-43f8-8e3b-af2f233aa7e7" />

---

## 4.4 Observations

The unauthenticated scan was able to:

- Identify the host as a live Windows system
- Enumerate open ports and detected services
- Fingerprint the web server and SNMP agent
- Detect two Low-severity configuration issues

The unauthenticated scan **could not**:

- Detect any missing security patches
- Identify installed software versions on the host
- Read registry-based configuration
- Discover the outdated Chrome installation
- Reveal any Critical or High-severity vulnerabilities

This gap is expected. Without credentials, Nessus has no way to look 
*inside* the system — it can only inspect what the system voluntarily 
exposes over the network. The two Low findings above represent the entire 
visible surface to an unauthenticated scanner.

The contrast with the credentialed scan in Section 05 illustrates why 
authenticated scanning is considered the industry standard for 
vulnerability assessment.

---

## Navigation

- [← Back: 03 – Preparing for Credentialed Scanning](./03-preparing-for-credentialed-scanning.md)
- [Next: 05 – Credentialed Scan →](./05-credentialed-scan.md)
