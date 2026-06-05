# Troubleshooting Case Study #6: Department Share Permission Denied

This entry covers the diagnostic workflow, group nesting analysis, and remediation steps applied to resolve a folder access denial stemming from incorrect departmental security group assignments.

---

### The Real-World Scenario
Alex Rivera (`fin.staff03`) is a newly onboarded Junior Accountant in the Finance department working on workstation `FIN-PC03`. During his first week, Alex attempts to access the primary departmental data repository located at `\\DC01\Finance-Share$` to update quarterly expense ledgers. 

Instead of seeing the folder contents, Windows displays an explicit network error window: **"Windows cannot access \\DC01\Finance-Share$. You do not have permission to access this folder."** The file share maps successfully via Group Policy, but Alex is completely locked out of the underlying directories, stalling his departmental onboarding tasks.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-006
* **Priority:** Medium
* **Assigned Engineer:** `helpdesk01` (IT Support Specialist)
* **Requesting User:** `fin.staff03` (Alex Rivera)
* **Department:** Finance Department
* **Affected Asset ID:** `FIN-PC03`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 15:30` | `helpdesk01` | **Ticket Opened.** User reports "Access Denied" errors when interacting with the main corporate `\\DC01\Finance-Share$` file repository. |
| `2026-06-05 15:52` | `helpdesk01` | **Diagnostic Run.** Validated security properties on the physical share folder. Audited user account tokens in Active Directory and verified active user security groups using `whoami /groups`. |
| `2026-06-05 16:15` | `helpdesk01` | **Root Cause Analysis.** Identified a Security Group Assignment mismatch. The folder's NTFS security list restricts access to `GG_FINANCE`. However, the user account was accidentally assigned to the standard `GG_HR` group during an administrative onboarding typo. |
| `2026-06-05 16:30` | `helpdesk01` | **Remediation.** Stripped `GG_HR` group membership from the user account, added them to `GG_FINANCE`, and performed an endpoint session logoff/logon to refresh tokens. Ticket closed. |

---

### Technical Documentation

#### Problem
The end-user profile `fin.staff03` was met with an explicit "Access Denied" structural block when trying to open or read data within the departmental secure file network share paths. 

#### Cause
The root issue was identified as an **Incorrect Security Group Assignment** (referencing `image_c3c1e2.png`). The storage volume's NTFS Access Control Lists (ACLs) restrict Modify/Read permissions exclusively to the departmental global group `GG_FINANCE`. Due to an administrative entry error during the account's initial provisioning sequence, the user object was dropped into the `GG_HR` global group instead. Because the user token lacked the correct department security identifier (SID), the server dropped the communication handshake.

#### Solutions
1. Opened **Active Directory Users and Computers** (`dsa.msc`) on the primary Domain Controller (`DC01`).
2. Located the user object `fin.staff03` inside the `Finance` Organizational Unit container.
3. Opened the account properties window and selected the **Member Of** tab.
4. Highlighted the incorrect `GG_HR` group assignment and clicked **Remove**.
5. Clicked **Add...**, searched for the correct **GG_FINANCE** security group, and saved the changes.
6. Switched to the client workstation (`FIN-PC03`), opened a Command Prompt, and ran a token verification tool to verify structural synchronization:
```cmd
   gpupdate /force
 ```
### Evidence
picre
---

Deployment Verification & Screenshots
To properly display evidence of your fix in your GitHub project, capture and name the following snapshots:

1. Directory Group Assignment Properties
What to capture: Open Active Directory Users and Computers on the Domain Controller, display the properties of the user account (fin.staff03), and highlight the Member Of tab interface pane.

Focus: Verify that the screen cleanly displays GG_FINANCE listed as an active group membership rule, and that GG_HR has been removed.

File Save Path: images/troubleshoot-06-group-fix.png

2. Restored Folder Path Access
What to capture: Open Windows File Explorer on the target user's desktop client (FIN-PC03) and double-click the \\DC01\Finance-Share$ path layout string.

Focus: Show the active interior window structure displaying the finance directories and spreadsheet assets without throwing any security warnings or permission alerts.

File Save Path: images/troubleshoot-06-share-success.png
