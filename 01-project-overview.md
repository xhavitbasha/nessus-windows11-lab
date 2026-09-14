# Nessus Vulnerability Assessment – Windows 10 Lab

A hands-on vulnerability assessment of an intentionally weakened Windows 10 
virtual machine using Tenable Nessus. This project demonstrates both 
unauthenticated and credentialed scanning methodologies in an isolated lab 
environment, and analyzes the difference in findings between the two.

> ⚠️ **Disclaimer:** All testing was performed in an isolated VirtualBox VM 
> that I own and control. No production systems, third-party networks, or 
> unauthorized targets were involved. This project is for educational and 
> portfolio purposes only.

---

## Objectives

- Build a deliberately vulnerable Windows 10 VM
- Perform a basic (unauthenticated) network scan
- Prepare the host for credentialed scanning
- Perform a credentialed (authenticated) scan
- Compare results between both scan types
- Analyze findings and propose remediation steps

## Tools Used

| Tool | Purpose |
|------|---------|
| Oracle VirtualBox | Virtualization platform |
| Windows 10 | Target host |
| Tenable Nessus Essentials | Vulnerability scanner |

## Project Structure

- [01 – Project Overview](./01-project-overview.md)
- [02 – Lab Environment Setup](./02-lab-environment-setup.md)
- [03 – Preparing for Credentialed Scanning](./03-preparing-for-credentialed-scanning.md)
- [04 – Basic Network Scan](./04-basic-network-scan.md)
- [05 – Credentialed Scan](./05-credentialed-scan.md)
- [06 – Findings & Analysis](./06-findings-analysis.md)
- [07 – Remediation Recommendations](./07-remediation.md)
- [08 – Lessons Learned](./08-lessons-learned.md)

## Skills Demonstrated

- Vulnerability assessment methodology
- Nessus configuration (authenticated & unauthenticated)
- Windows security configuration (services, UAC, registry policies)
- Risk analysis and remediation planning
- Technical documentation and reporting
