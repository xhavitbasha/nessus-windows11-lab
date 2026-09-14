# Nessus Vulnerability Assessment – Windows 10 Lab

# Project Overview

## Purpose

The purpose of this project was to perform a hands-on vulnerability 
assessment of a Windows 10 endpoint using Tenable Nessus. The 
assessment was conducted in a controlled, isolated lab environment 
with the goal of understanding how vulnerability scanners behave 
against hosts in different states of security hardening.

## Scenario

To simulate a realistic poorly-maintained endpoint, a Windows 10 
virtual machine was intentionally weakened by:

- Removing previously installed Windows security updates
- Pausing future Windows updates
- Disabling all Windows Firewall profiles (Domain, Private, Public)

This configuration mirrors conditions commonly found in small business 
or home-office environments where endpoints are not centrally managed.

## Objectives

1. Build an isolated, deliberately vulnerable Windows 10 VM
2. Perform a **basic (unauthenticated) network scan** using Nessus
3. Prepare the host for **credentialed scanning** by configuring 
   Windows services, UAC, and registry policies
4. Perform a **credentialed (authenticated) scan** using Nessus
5. Compare the findings between the two scan types
6. Analyze vulnerabilities and propose remediation strategies
7. Document the entire methodology as a reproducible reference

## Scope

| Item | Detail |
|------|--------|
| Target | Single Windows 10 virtual machine |
| Network | Isolated host-only / NAT network |
| Scanner | Tenable Nessus Essentials |
| Scan Types | Unauthenticated and credentialed |
| Out of Scope | External systems, production networks, exploit attempts |

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Oracle VirtualBox | X.X | Virtualization platform |
| Windows 10 | Build XXXX | Target operating system |
| Tenable Nessus Essentials | X.X | Vulnerability scanner |

## Skills Demonstrated

- Vulnerability assessment methodology
- Nessus scan configuration (authenticated and unauthenticated)
- Windows security configuration (services, UAC, registry policies)
- Understanding of credentialed vs. unauthenticated scanning
- Risk analysis, prioritization, and remediation planning
- Technical documentation and reporting

## Disclaimer

All activity in this project was performed in an isolated virtual 
machine that I own and control. No production systems, third-party 
networks, or unauthorized targets were involved. This project is 
intended purely for educational and portfolio purposes.

## Navigation

- [02 – Lab Environment Setup](./02-lab-environment-setup.md)
- [03 – Preparing for Credentialed Scanning](./03-preparing-for-credentialed-scanning.md)
- [04 – Basic Network Scan](./04-basic-network-scan.md)
- [05 – Credentialed Scan](./05-credentialed-scan.md)
- [06 – Findings & Analysis](./06-findings-analysis.md)
- [07 – Remediation Recommendations](./07-remediation.md)
- [08 – Lessons Learned](./08-lessons-learned.md)
