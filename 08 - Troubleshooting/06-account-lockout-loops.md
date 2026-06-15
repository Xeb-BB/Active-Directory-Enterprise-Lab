# Troubleshooting Case Study #6: Persistent Account Lockout Loops

This entry covers the diagnostic workflow, credential vault auditing, and remediation steps applied to track down and eliminate repeating user account lockouts caused by cached network credentials.

---

### The Real-World Scenario
Liam O'Connor (`finance02`) is a Financial Analyst in the Finance department working on workstation `FIN-PC02`. After performing his mandatory 90-day corporate domain password change, Liam logs back into his computer successfully. 

However, within five minutes of working, his active Windows session freezes up, throwing an error that his account is locked out. An IT administrator unlocks his account in Active Directory, but moments later, it instantly locks out again. This loop repeats indefinitely as long as his laptop is connected to the corporate network infrastructure, rendering him completely unable to work.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-008
* **Priority:** High
* **Assigned Engineer:** `helpdesk02` (Tier 2 Support Technician)
* **Requesting User:** `finance02` (Liam O'Connor)
* **Department:** Finance Department
* **Affected Asset ID:** `FIN-PC02`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 17:45` | `helpdesk02` | **Ticket Opened.** User experiencing immediate, repeating Active Directory account lockouts right after a password rotation sequence. |
| `2026-06-05 18:02` | `helpdesk02` | **Diagnostic Run.** Analyzed Security Logs on Domain Controller `DC01` for Event ID 4740 (Account Lockout). Traced the source caller machine string back to Liam's laptop: `FIN-PC02`. |
| `2026-06-05 18:15` | `helpdesk02` | **Root Cause Analysis.** Identified a Stale Saved Credential. The user's workstation had an old mapped network drive path or intranet token cached inside the local Windows Credential Manager vault using his expired password. The operating system was continuously hammering the DC with these dead credentials in the background, triggering the lockout threshold. |
| `2026-06-05 18:30` | `helpdesk02` | **Remediation.** Disconnected the machine network link, unlocked the AD object, purged the Windows Vault entries via Control Panel, and reconnected. Ticket closed. |

---

### Technical Documentation

#### Problem
An Active Directory user account experiences an endless loop of lockout events immediately after changing their domain password. Even after an administrator resets the lockout counter manually inside Active Directory Users and Computers (ADUC), background authentication calls from the host client machine flag the security threshold rules almost instantly.

#### Cause
The root issue was identified as a **Saved Stale Credential** inside the endpoint OS architecture (referencing `image_c3c1e2.png`). When passwords are rotated, local background windows systems, mapped network shares, or browser engines cached inside the **Windows Credential Manager (Windows Vault)** may still attempt to sync data utilizing the user's expired password. Because these automated authentication loops happen rapidly in the background, they fail multiple times a second, instantly exceeding the domain's Account Lockout Policy threshold.

#### Solutions
1. Unplugged the ethernet cable and disconnected Wi-Fi on workstation `FIN-PC02` (this cuts off the bad authentication loops hitting the Domain Controller while you work).
2. Switched to the Domain Controller (`DC01`) and cleared the lockout flag under the account properties in **Active Directory Users and Computers**.
3. On the client machine, accessed the Windows Control Panel and opened **Credential Manager**.
4. Selected **Windows Credentials** to display the stored data tree.
5. Located all identity tokens mapped to the internal corporate namespace (e.g., targets matching `*.liontech.local`, `DC01`, or shared printer resources).
6. Expanded each old entry and clicked **Remove** to wipe the stale credentials from the Windows Vault cache layers.
7. Reconnected the network cable to the workstation and prompted the user to access a shared drive path to force a fresh, compliant login handshake.

---

