# Troubleshooting Case Study #9: Workstation Trust Relationship Dropped

This entry covers the diagnostic workflow, Kerberos clock synchronization analysis, and remediation steps applied to restore secure domain communications for a client machine experiencing a broken channel trust.

---

### The Real-World Scenario
Amanda Ross (`sales04`) is a senior Account Executive in the Sales department who uses workstation `SALES-PC04`. After returning from a two-week vacation, she boots her machine and attempts to log in using her corporate domain credentials. 

Instead of showing the Windows desktop environment, the operating system stops the authentication handshake and displays a fatal login error dialog box: **"The trust relationship between this workstation and the primary domain cannot be established."** Amanda is completely blocked from logging into her computer, cutting off her access to local client utilities and corporate productivity suites.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-009
* **Priority:** High
* **Assigned Engineer:** `helpdesk01` (IT Support Specialist)
* **Requesting User:** `sales04` (Amanda Ross)
* **Department:** Sales Department
* **Affected Asset ID:** `SALES-PC04`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 15:05` | `helpdesk01` | **Ticket Opened.** User blocked from system logon due to a broken domain trust relationship validation failure. |
| `2026-06-05 15:20` | `helpdesk01` | **Diagnostic Run.** Logged in with a local administrator profile to investigate. Attempted to execute `nltest /sc_verify:liontech.local` and received access denial error due to system time differentials. |
| `2026-06-05 15:35` | `helpdesk01` | **Root Cause Analysis.** Identified a Kerberos Time Skew (referencing `image_c3c1e2.png`). The workstation's internal CMOS battery had drifted, causing the local hardware system time to lag over 8 minutes behind the domain controller clock. Kerberos security protocols drop handshakes if clock variance exceeds a 5-minute threshold. |
| `2026-06-05 15:50` | `helpdesk01` | **Remediation.** Repointed the primary Domain Controller to an external NTP stratum clock source, re-synced the client computer time parameters manually via `w32tm`, and successfully validated the trust channel. Ticket closed. |

---

### Technical Documentation

#### Problem
The workstation asset `SALES-PC04` completely lost its secure communication path verification with the Active Directory architecture. Domain authentication routines failed at the client lock screen, throwing structural alerts indicating that a secure trust validation layer could not be processed.

#### Cause
The root issue was identified as a **Kerberos Time Skew** error (referencing `image_c3c1e2.png`). Active Directory domains rely heavily on the Kerberos v5 authentication framework to prevent replay attacks. This framework dictates that the system clock on a client computer cannot differ by more than 5 minutes from the reference clock on the domain controller authenticating the request. Because the endpoint's localized time registries drifted beyond this threshold, the domain authority refused to issue valid security tickets.

#### Solutions
1. Bypassed the active domain login interface by signing into the workstation using a fallback local administrator credential account (`.\localadmin`).
2. Checked the system clock parameters against the core Domain Controller (`DC01`) clock to confirm the time disparity.
3. Logged into `DC01` and validated that the primary DC was pulling accurate time from an external NTP source using the Windows Time configuration command tool:
```cmd
   w32tm /config /manualpeerlist:"pool.ntp.org" /syncfromflags:manual /reliable:yes /update
```
---
