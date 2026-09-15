# Preparing for Credentialed Scanning

## Overview

A credentialed scan allows Nessus to log into the target system and inspect 
it from the inside — reading the registry, enumerating installed software, 
checking patch levels, and reviewing local security configuration. This 
produces far deeper and more accurate results than an unauthenticated 
network scan.

However, Windows does not allow a local administrator account to use its 
full privileges over the network by default. This is a deliberate security 
feature introduced with UAC to prevent lateral movement and pass-the-hash 
style attacks. Because of this, a credentialed scan will typically fail or 
return incomplete results until three specific settings are adjusted.

This section documents those three changes and explains why each one is 
necessary for Nessus to authenticate successfully.

---

## 3.1 The Problem: Remote Admin Token Filtering

When a local administrator account authenticates to a Windows machine over 
the network (for example, via SMB), Windows **strips the administrator 
token** from the session. The account is treated as a standard user even 
though it belongs to the Administrators group.

This behavior is controlled by a registry value called 
`LocalAccountTokenFilterPolicy`. By default, this value does not exist, 
which means the filtering is active. Nessus therefore connects successfully 
but cannot perform privileged actions — resulting in partial or failed 
credentialed scans.

The three changes below remove this barrier:

1. **Enable Remote Registry** — allows Nessus to read registry keys remotely
2. **Disable UAC remote restrictions** — prevents UAC from blocking remote access
3. **Set LocalAccountTokenFilterPolicy to 1** — grants the full admin token over the network

Each is documented below.

---

## 3.2 Enable the Remote Registry Service

The Remote Registry service allows a remote system to read and modify 
registry keys on the target machine. Nessus uses this to enumerate 
installed software, check configuration settings, and identify missing 
patches.

**Steps taken:**
1. Pressed `Win + R`, typed `services.msc`, and pressed Enter
2. Located **Remote Registry** in the list
3. Right-clicked → **Properties**
4. Changed **Startup type** from *Disabled* to **Automatic**
5. Clicked **Start** to launch the service immediately
6. Clicked **Apply**, then **OK**

*Remote Registry service set to Automatic and running*
<img width="400" height="467" alt="Screenshot 2026-09-15 at 13 34 24" src="https://github.com/user-attachments/assets/86ab425e-9797-4a18-b4b7-22c4b8ddfabe" />

---

## 3.3 Disable UAC Remote Restrictions

User Account Control (UAC) prompts the user for confirmation when 
administrative actions are performed. For remote sessions, UAC blocks 
elevation entirely by default — meaning a remote admin login cannot 
trigger privileged operations.

Setting UAC to **Never Notify** on the target machine removes this 
restriction for the local session, which allows the credentialed scan 
to proceed without elevation prompts blocking it.

**Steps taken:**
1. Opened the Start menu and searched for **UAC** or **Change User Account Control settings**
2. Opened the **User Account Control Settings** window
3. Dragged the slider down to **Never notify**
4. Clicked **OK** and confirmed the change when prompted


*UAC slider set to "Never notify"*

<img width="731" height="563" alt="Screenshot 2026-09-15 at 13 34 59" src="https://github.com/user-attachments/assets/f89b8233-c09c-436e-a6b3-99a821766b17" />


> **Security note:** In a production environment, disabling UAC is 
> strongly discouraged. It removes a critical defense-in-depth layer. 
> In this lab, it was done deliberately to enable the credentialed scan 
> and to simulate a poorly-configured endpoint.

---

## 3.4 Configure LocalAccountTokenFilterPolicy

This is the most important change for credentialed scanning against 
local accounts. It tells Windows to grant the full administrator token 
to local accounts authenticating over the network, rather than filtering 
it down to a standard user token.

**Steps taken:**
1. Pressed `Win + R`, typed `regedit`, and pressed Enter
2. Navigated to the following path:
3. Right-clicked in the right-hand pane → **New → DWORD (32-bit) Value**
4. Named the value:LocalAccountTokenFilterPolicy


**Registry summary:**

| Property | Value |
|----------|-------|
| Path | `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System` |
| Name | `LocalAccountTokenFilterPolicy` |
| Type | DWORD (32-bit) |
| Value | `1` |

*Registry Editor showing the LocalAccountTokenFilterPolicy value set to 1*

<img width="723" height="604" alt="Screenshot 2026-09-15 at 13 39 32" src="https://github.com/user-attachments/assets/2db6c82c-6354-4b28-8c41-315f5489c724" />

> **Security note:** Setting this value to `1` weakens the machine 
> against lateral movement and credential-based attacks. It is used 
> here only because the VM is isolated, and because it is required for 
> Nessus to perform a full credentialed scan against a local account.

---

## 3.5 Summary of Changes

The following table summarizes all configuration changes made to prepare 
the VM for credentialed scanning:

| Setting | Location | Change | Purpose |
|---------|----------|--------|---------|
| Remote Registry | `services.msc` | Disabled → Automatic | Allow remote registry reads |
| UAC | User Account Control Settings | Never notify | Prevent elevation blocking remote sessions |
| LocalAccountTokenFilterPolicy | `HKLM\...\Policies\System` | Created, set to `1` | Grant full admin token over the network |

Once these three changes were in place, the VM was ready to accept a 
credentialed scan from Nessus. The results of that scan are documented 
in Section 05.

---

## Navigation

- [← Back: 02 – Lab Environment Setup](./02-lab-environment-setup.md)
- [Next: 04 – Basic Network Scan →](./04-basic-network-scan.md)

