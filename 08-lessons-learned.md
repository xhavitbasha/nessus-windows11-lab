# Lessons Learned

## Overview

This project was my first end-to-end vulnerability assessment using a 
professional scanner. The technical steps — building the VM, preparing 
it for credentialed scanning, running the scans, and analyzing the 
results — taught me things that reading documentation alone could not. 
This section is a reflection on what I learned and how it changed the 
way I think about vulnerability management.

---

## 8.1 Credentialed Scanning Is Not Optional

Before this project, I understood credentialed scanning as an "advanced" 
feature of Nessus — something you enabled if you wanted more detail. 
After seeing the two scans side by side, I understand it differently.

The unauthenticated scan found nothing above Low severity. The 
credentialed scan found a Critical CVSS 9.8 vulnerability that could 
compromise the host remotely without authentication.

Same host. Same scanner. Same policy. The only variable was credentials.

This made it concrete for me: **an unauthenticated-only vulnerability 
management program is not assessing its environment — it is assessing 
its perimeter.** Any organization relying only on external scans is 
missing the vulnerabilities that matter most.

---

## 8.2 Windows Security Defaults Are Deliberate

The preparation required for credentialed scanning was one of the most 
educational parts of the project. Enabling the Remote Registry service, 
disabling UAC remote restrictions, and setting 
`LocalAccountTokenFilterPolicy = 1` were all changes that *weaken* the 
host — and Windows blocks them by default for good reason.

Working through this helped me understand:

- **Why** Windows strips admin tokens over the network (protection against lateral movement and pass-the-hash)
- **Why** UAC blocks remote elevation (defense against unauthorized privileged actions)
- **Why** the Remote Registry service is disabled (attack surface reduction)

The lesson is that these settings exist as a **security posture**, not 
as obstacles. In a real environment, weakening them to enable scanning 
requires a documented risk decision — you don't just flip them without 
thinking about the trade-off.

---

## 8.3 Vulnerability Data Requires Interpretation

The credentialed scan returned 48 distinct findings. At first glance 
that sounds alarming, but once I started reviewing them, most were 
informational — service banners, version detections, general host 
observations. The real signal was a small number of findings that 
mattered:

- 1 Critical (.NET Framework missing updates)
- 1 High (WinGet elevation of privilege)
- Missing patches under the Microsoft Bulletins family

If I had simply reported "48 vulnerabilities found," I would have 
communicated almost nothing useful. The value came from **prioritizing** 
— considering exploitability, prerequisites, and business impact, not 
just raw severity count.

This changed how I read scan results. A finding count is not the same 
thing as a risk picture.

---

## 8.4 Documentation Is Part of the Work

Writing this project up — the setup, the preparation, the scan 
configuration, the analysis, the remediation plan — took nearly as long 
as the technical work itself. At first that felt like overhead. It 
turned out to be the most valuable part.

Documenting forced me to:

- Explain **why** each step mattered, not just list it
- Verify my reasoning was sound (writing exposes gaps in understanding)
- Produce something a non-technical stakeholder could follow
- Turn isolated tasks into a coherent narrative

In a real security role, the documentation is often what a client or 
manager sees — the tool output is raw material, not a deliverable.

---

## 8.5 What I Would Do Differently

A few things I would change if I ran this project again:

1. **Capture screenshots as I go.** I took screenshots after the fact in 
   some cases, which meant some states had already changed. In a real 
   engagement, evidence capture is continuous, not retrospective.

2. **Snapshot the VM before making changes.** UTM snapshots would have 
   let me roll back to specific states and re-demonstrate steps cleanly. 
   I now consider this essential for any lab work.

3. **Perform the remediation rescan.** I documented how to fix each 
   finding but did not actually re-scan after remediation. Doing so 
   would have demonstrated the full lifecycle — scan, remediate, verify 
   — rather than stopping at the recommendation stage.

4. **Choose the version of the browser more deliberately.** I installed 
   an outdated Chrome, but did not document its specific version or CVEs. 
   In future lab projects, I will pick vulnerability versions with 
   known, well-documented CVEs to make the findings more concrete.

---

## 8.6 What This Project Confirmed for Me

This project confirmed that I enjoy working in vulnerability management 
— specifically the analytical part: taking raw scan output, determining 
what actually matters, and communicating the risk clearly.

It also showed me how much of the field is about judgment rather than 
tooling. Nessus is a good scanner, but the difference between a useful 
report and a useless one comes down to interpretation and prioritization.

I am continuing to build on this by exploring related areas: 
authenticated scanning across different operating systems, exploitation 
validation with tools like Metasploit, and vulnerability management 
workflows in enterprise environments.

---

## 8.7 Closing Reflection

This was a small project — one VM, two scans, a handful of findings. 
What made it worthwhile was treating it as a complete exercise rather 
than a series of tool commands. Building the lab, weakening it 
deliberately, preparing it for credentialed access, running both scan 
types, analyzing the difference, and documenting remediation steps 
covered the full lifecycle that a vulnerability management analyst 
works with every day.

The single most important thing I learned is this:

> **The scanner is only as good as the access you give it. Credentials 
> are what turn a network probe into an assessment.**

---

## Navigation

- [← Back: 07 – Remediation Recommendations](./07-remediation.md)
- [Return to Project Overview](./README.md)
