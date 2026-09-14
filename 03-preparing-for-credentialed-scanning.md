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

*Screenshot: Remote Registry service set to Automatic and running*

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

*Screenshot: UAC slider set to "Never notify"*

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
