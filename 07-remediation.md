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

   
---

## Notes on This Page

A few design decisions worth knowing:

1. **Each finding has a target SLA.** Real vulnerability management uses SLAs (fix Critical in 24–48h, High in 7 days, etc.). Adding these makes your project read like an actual remediation plan, not a checklist. Reviewers who work in security will recognize this pattern immediately.

2. **Each finding has a verification step.** Most portfolio projects stop at "here's how to fix it." Adding "here's how you know it's fixed" — with actual commands like `Get-HotFix` and `winget --version` — shows you think about closing the loop, not just checking a box.

3. **Priority 6 (DHCP) is framed as an investigation, not a fix.** Because it might be a false positive or expected behavior of the host-only network, jumping straight to "disable DHCP" would be wrong. Framing it as "investigate first" shows professional judgment.

4. **7.8 sets up the possibility of a rescan.** If you decide to actually remediate the VM and re-scan, this is where you'd add the "after" screenshot. That would elevate the project from "I found vulnerabilities" to "I found vulnerabilities, fixed them, and proved it worked." **If you have time, do this** — it's a big differentiator.

---

## Optional: The Remediation Rescan

If you want to take this project from strong to exceptional, here's the move:

1. Snapshot the current VM state in UTM (so you can revert if needed)
2. Apply fixes for priorities 1–4 (Windows Update + WinGet)
3. Re-run the credentialed scan
4. Screenshot the new severity chart
5. Add a section 7.9 "Before vs. After" showing the delta

This would give you a **four-stage project**:
- Stage 1: Unauthenticated scan (baseline external view)
- Stage 2: Credentialed scan (full internal view)
- Stage 3: Analysis (findings and comparison)
- Stage 4: Remediation and verification (fix and prove)

That's a complete vulnerability management cycle — the same one used in real security operations. Very few beginner portfolios show this. It would immediately distinguish your project from typical "I ran Nessus" write-ups.

If you don't have time, no problem — the current version is already very strong.

---

## What's Next: Page 08 – Lessons Learned

Page 08 is short and reflective. It's where you write in first person about what you actually learned from building this. Tell me when Page 07 is committed, and we'll write the final page — then your project will be complete and ready to feature on your profile.

### Alternative (Command Line)

For environments where updates are managed centrally:

```powershell
# Trigger Windows Update scan and install from PowerShell
Install-Module PSWindowsUpdate -Force
Get-WindowsUpdate
Install-WindowsUpdate -AcceptAll -AutoReboot


