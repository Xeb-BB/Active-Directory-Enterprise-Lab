# Troubleshooting Case Study #4: Folder Access Denied After Promotion

This entry covers the diagnostic workflow, Kerberos token evaluation, and remediation steps applied to resolve an access denial issue immediately following a user's security group modification.

---

### The Real-World Scenario
Sarah Jenkins (`hr.staff03`) was recently promoted to an HR Management role. To grant her access to confidential personnel files, the IT administrator added her account to the privileged `GG_HR_Managers` security group in Active Directory. 

Immediately following the change, Sarah attempts to open the restricted `\\DC01\HR-Private$` network share from her workstation (`HR-PC03`). Instead of seeing the contents, Windows pops up an explicit network error: **"Windows cannot access \\DC01\HR-Private$. You do not have permission to access this folder."** The administrator verifies that the folder's access control lists (ACLs) are correct, yet Sarah is blocked completely.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-004
* **Priority:** Medium
* **Assigned Engineer:** `helpdesk02` (Tier 2 Support Technician)
* **Requesting User:** `hr.staff03` (Sarah Jenkins)
* **Department:** Human Resources
* **Affected Asset ID:** `HR-PC03`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 13:15` | `helpdesk02` | **Ticket Opened.** User receiving "Access Denied" errors on a secure network share despite being added to the correct group (`GG_HR_Managers`). |
| `2026-06-05 13:30` | `helpdesk02` | **Diagnostic Run.** Instructed user to run `whoami /groups` in the terminal. Confirmed that the user's active session security token is completely missing the newly assigned group SID. |
| `2026-06-05 13:42` | `helpdesk02` | **Root Cause Analysis.** Identified a Stale Kerberos Token. The user's active Windows security ticket was generated during morning logon and does not dynamically update to reflect group membership changes without a new security handshake. |
| `2026-06-05 13:50` | `helpdesk02` | **Remediation.** Forced a Kerberos ticket purge using `klist purge` and had the user sign out and back in. Group token successfully attached. Ticket closed. |

---

### Technical Documentation

#### Problem
An employee who was newly assigned to a privileged security group was met with an explicit "Access Denied" error window when attempting to view or write to a secure network share allocated specifically to that group. 

#### Cause
The root issue was determined to be a **Stale Kerberos Token** (referencing `image_c3c1e2.png`). When a user logs into a Windows client workstation within an Active Directory domain, the local Local Security Authority (LSA) requests a Kerberos Ticket Granting Ticket (TGT). This ticket contains a compilation of the user's group SIDs at *that exact moment*. Because Sarah did not close her active Windows session after the administrator modified her account properties, her existing access token lacked the new group identifier required to clear the file share's security descriptor rules.

#### Solutions
1. Instructed the user to save all open projects and active documents.
2. Opened an administrative Command Prompt terminal on the client workstation (`HR-PC03`).
3. Ran a targeted ticket flush command to strip cached authorization fields:
```cmd
   klist purge
```
