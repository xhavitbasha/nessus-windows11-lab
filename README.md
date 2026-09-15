# Nessus Vulnerability Assessment – Windows 11 Lab

A hands-on vulnerability assessment of an intentionally weakened Windows 11 
virtual machine using Tenable Nessus. This project compares unauthenticated 
and credentialed scanning against the same target, and demonstrates how 
credentials fundamentally change the vulnerability picture of a host.

**Headline result:** The unauthenticated scan found **0 Critical / 0 High** 
findings. The credentialed scan found a **Critical CVSS 9.8** vulnerability 
exploitable remotely without authentication.

> ⚠️ **Disclaimer:** All testing was performed in an isolated virtual machine 
> that I own and control. No production systems, third-party networks, or 
> unauthorized targets were involved. This project is for educational and 
> portfolio purposes only.

---

## Key Findings

| Severity | Unauthenticated | Credentialed |
|----------|-----------------|--------------|
| Critical | 0 | 1 |
| High | 0 | 1 |
| Medium/Mixed | 0 | 2 bulletins |
| Low | 2 | 1 |
| Info | 13 | 43 |
| **Total** | **15** | **48** |

The credentialed scan revealed vulnerabilities that were completely 
invisible to the unauthenticated scan — including missing security 
patches and a remotely exploitable Critical vulnerability.

---

## Project Structure

| # | Section | Description |
|---|---------|-------------|
| 01 | [Project Overview](./01-project-overview.md) | Purpose, objectives, scope |
| 02 | [Lab Environment Setup](./02-lab-environment-setup.md) | VM configuration and deliberate weaknesses |
| 03 | [Preparing for Credentialed Scanning](./03-preparing-for-credentialed-scanning.md) | Windows settings required for authenticated access |
| 04 | [Basic Network Scan](./04-basic-network-scan.md) | Unauthenticated scan results |
| 05 | [Credentialed Scan](./05-credentialed-scan.md) | Authenticated scan results |
| 06 | [Findings & Analysis](./06-findings-analysis.md) | Side-by-side comparison and analysis |
| 07 | [Remediation Recommendations](./07-remediation.md) | Fixes and verification steps |
| 08 | [Lessons Learned](./08-lessons-learned.md) | Reflection on the project |

---

## Tools Used

| Tool | Purpose |
|------|---------|
| UTM (QEMU-based) | Virtualization platform on macOS |
| Windows 11 Pro (25H2, Build 26200.6584) | Target host |
| Tenable Nessus Essentials | Vulnerability scanner |

---

## Methodology Summary

1. Built an isolated Windows 11 VM in UTM with host-only networking
2. Deliberately weakened it — uninstalled security updates, disabled the firewall on all profiles, installed an outdated browser
3. Ran a **basic network scan** with no credentials (external view)
4. Prepared the host for credentialed scanning — enabled Remote Registry, disabled UAC remote restrictions, set `LocalAccountTokenFilterPolicy = 1`
5. Ran a **credentialed scan** with local admin credentials (internal view)
6. Compared findings, prioritized by real-world risk, and documented remediation

---

## Key Skills Demonstrated

- Vulnerability assessment methodology
- Nessus configuration (authenticated & unauthenticated scanning)
- Windows security configuration (services, UAC, registry policies)
- Understanding of Windows remote authentication and token filtering
- Risk prioritization based on exploitability and prerequisites
- Remediation planning with verification commands
- Technical documentation and reporting
