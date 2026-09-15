# Findings & Analysis

## Overview

This section analyzes the results from both scans side by side. Because 
the two scans used the **same target, same policy, and same scanner**, 
the only variable changed was the presence of credentials. That makes the 
comparison a clean controlled experiment: any difference in findings is 
directly attributable to credentialed access.

The result is stark. The unauthenticated scan found **zero** findings 
above Low severity. The credentialed scan found a **Critical CVSS 9.8** 
vulnerability that could be exploited remotely without authentication.

---

## 6.1 Side-by-Side Comparison

| Metric | Unauthenticated | Credentialed | Delta |
|--------|-----------------|--------------|-------|
| Total distinct findings | 15 | 48 | +33 |
| Critical | 0 | 1 | +1 |
| High | 0 | 1 | +1 |
| Medium / Mixed | 0 | 2 bulletins (14 findings) | +14 |
| Low | 2 | 1 | −1 |
| Info | 13 | 43 | +30 |
| Missing patches detected | 0 | Yes (incl. KB5066835) | — |
| Software inventory | None | Full | — |
| Registry-level checks | None | Yes | — |

The unauthenticated scan produced a nearly empty risk picture — two Low 
findings and a pile of informational observations. The credentialed scan 
produced a **materially different** picture: missing security updates, a 
Critical remotely exploitable vulnerability, and a High-severity local 
privilege escalation.

---

## 6.2 What the Unauthenticated Scan Could See

The unauthenticated scan's capabilities were limited to what the host 
voluntarily exposes over the network:

- Open ports and running services
- Service banners and version fingerprints
- SNMP agent responses
- Web server identification
- Two Low-severity configuration observations (DHCP detection, ICMP timestamp disclosure)

This is not a flaw in Nessus — it's the fundamental limit of any 
external-only assessment. Without the ability to log in, the scanner can 
only see the **outside surface** of the host. The interior — patch 
levels, installed software, registry configuration — is invisible.

---

## 6.3 What Credentials Unlocked

Once valid Windows credentials were supplied, Nessus could:

| Capability | Enabled By | Revealed |
|------------|-----------|----------|
| Read installed patch level | Remote Registry | Missing KB5066835, .NET updates |
| Enumerate installed software | WMI / Registry | Outdated WinGet (CVE-2026-68821) |
| Check application versions | Registry | Full software inventory |
| Inspect local security config | Registry | Host-level misconfigurations |
| Correlate against CVE database | Nessus plugin engine | Severity-ranked findings |

The Critical finding — **missing .NET Framework security updates, CVSS 9.8, exploitable remotely with no privileges and no user interaction** — was simply undetectable without credentials. Nessus had no way to know whether the .NET Framework on the host was patched, because that information lives inside the OS.

---

## 6.4 Why the Difference Matters

In a real environment, this gap has direct consequences:

### For a defender
An organization that only performs unauthenticated scans would look at 
this host and conclude it is essentially clean. They would not know:

- The host is missing critical security patches
- A remote-code-execution-class vulnerability is present
- WinGet is exploitable for local privilege escalation
- The .NET Framework is out of date

Each of these would sit undiscovered until an actual attacker exploited them.

### For an attacker
The Critical finding (CVSS 9.8, `AV:N/AC:L/PR:N/UI:N`) is the kind of 
vulnerability that ends up in automated exploitation frameworks within 
days of disclosure. A single unauthenticated request could achieve full 
host compromise — no phishing, no user interaction, no privileged access 
required.

### For compliance
Standards like **PCI DSS**, **CIS Controls**, and **NIST 800-40** 
explicitly require authenticated vulnerability scanning for exactly this 
reason. An unauthenticated-only scan program would fail an audit and, 
more importantly, would fail to protect the environment.

---

## 6.5 Risk Prioritization

Not all findings deserve equal urgency. Ranking the discovered 
vulnerabilities the way a real analyst would:

| Priority | Finding | Severity | Exploitability | Recommended SLA |
|----------|---------|----------|----------------|-----------------|
| 1 | .NET Framework missing updates | Critical (9.8) | Remote, no auth, public exploit | Fix within 24–48 hours |
| 2 | Missing KB5066835 (libtiff / TPM / IGEL) | High/Mixed | Multiple CVEs, some remote | Fix within 7 days |
| 3 | WinGet elevation of privilege (CVE-2026-68821) | High (7.3) | Local only, requires foothold | Fix within 14 days |
| 4 | Other Microsoft Bulletins | Mixed | Varies | Fix within 30 days |
| 5 | ICMP Timestamp Disclosure | Low (2.1) | Requires adjacent network | Fix when convenient |
| 6 | DHCP Server Detection | Low (3.3) | Context-dependent | Investigate — expected? |

**Prioritization reasoning:**

- The Critical finding ranks first because it can be exploited remotely, without authentication, and without user interaction. Public exploit code exists. This is the finding that would keep a security team up at night.
- The WinGet finding is High but ranks third because it requires an attacker to already have a low-privilege foothold. It is a *post-exploitation* escalation, not an initial access vector.
- The ICMP finding ranks low because exploiting it requires adjacent network access — it's an information disclosure, not a compromise vector.

---

## 6.6 The Central Lesson

The single most important takeaway from this project:

> **An unauthenticated scan tells you what a host looks like from the 
> outside. A credentialed scan tells you what the host actually is.**

For this VM, the difference was extreme. The unauthenticated scan gave a 
false sense of safety: 0 Critical, 0 High. The credentialed scan revealed 
a Critical remotely exploitable vulnerability and multiple missing 
patches.

A vulnerability management program that relies only on unauthenticated 
scanning is not assessing its environment — it is assessing only its 
network perimeter. Real, defensible vulnerability management requires 
authenticated scanning on every host it can reach.

---

## 6.7 Response Actions

The next section (Section 07) documents the specific remediation steps 
for each of the findings above. The full lifecycle — **scan → analyze → 
remediate → verify** — is what separates vulnerability *assessment* from 
vulnerability *management*.

---

## Navigation

- [← Back: 05 – Credentialed Scan](./05-credentialed-scan.md)
- [Next: 07 – Remediation Recommendations →](./07-remediation.md)
