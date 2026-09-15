# Remediation Recommendations

## Overview

This section documents the specific remediation steps for each finding 
identified in the credentialed scan (Section 05). The recommendations 
are ordered by priority, matching the risk ranking established in 
Section 6.5.

The goal of remediation is not just to make findings disappear from a 
scanner report — it is to genuinely reduce the attack surface of the 
host. Each recommendation below explains **what to do**, **how to verify 
it worked**, and **why it matters**.

---

## 7.1 Priority 1 — Apply .NET Framework Security Updates

**Finding:** Security Updates for Microsoft .NET Framework  
**Severity:** Critical (CVSS 9.8)  
**Target SLA:** 24–48 hours

### Why This Matters

This is the most severe finding in the project. The CVSS vector 
`AV:N/AC:L/PR:N/UI:N` describes a vulnerability that is:

- Exploitable remotely over the network
- Low complexity to exploit
- Requires no privileges on the target
- Requires no user interaction

In other words, an unauthenticated attacker on the same network could 
potentially achieve full compromise of the host. This is the finding 
that would be escalated to an incident response team immediately in a 
real environment.

### Remediation Steps

1. Open **Settings → Windows Update**
2. Click **Check for updates**
3. Install all available updates, prioritizing:
   - .NET Framework security updates
   - Cumulative updates for Windows 11
4. Restart the host when prompted
5. After restart, check for updates again (Windows often needs multiple passes to fully patch)

### Alternative (Command Line)

For environments where updates are managed centrally:

```powershell
# Trigger Windows Update scan and install from PowerShell
Install-Module PSWindowsUpdate -Force
Get-WindowsUpdate
Install-WindowsUpdate -AcceptAll -AutoReboot
