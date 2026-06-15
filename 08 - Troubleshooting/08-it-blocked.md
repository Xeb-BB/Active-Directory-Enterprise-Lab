# Troubleshooting Case Study #8: IT Staff Blocked from CMD/PowerShell

This entry covers the diagnostic workflow, GPO security filtering analysis, and remediation steps applied to restore command-line utility access for administrative staff following a global policy enforcement error.

---

### The Real-World Scenario
Sarah Connor (`it.admin02`) is a Systems Administrator in the IT Department working on her administrative workstation `IT-ADM02`. To troubleshoot a remote deployment, she attempts to launch an elevated Command Prompt session and a PowerShell terminal. 

Instead of opening the command interfaces, both applications immediately close, throwing a Windows restrictions dialog box error: **"This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator."** The policy intended to lock down standard corporate end-users has accidentally locked out the internal domain engineers, crippling the IT department's ability to manage the environment.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-008
* **Priority:** Urgent
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** `it.admin02` (Sarah Connor)
* **Department:** IT Department
* **Affected Asset ID:** `IT-ADM02` (All IT Administrative Endpoints)

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 14:05` | `sysadmin01` | **Ticket Opened.** IT staff reporting they are globally blocked from launching `cmd.exe` and `powershell.exe` due to an active user restriction policy. |
| `2026-06-05 14:20` | `sysadmin01` | **Diagnostic Run.** Reviewed the Group Policy Management Console. Found that a newly deployed workstation lockdown policy (`GPO_User_Interface_Restrictions`) was linked at the root domain level and applied to 'Authenticated Users' without administrative exemptions. |
| `2026-06-05 14:35` | `sysadmin01` | **Root Cause Analysis.** Identified a GPO Scope Error (referencing `image_c3c1e2.png`). The restriction policy was processed globally across the entire directory structure, catching the IT engineering group (`GG_IT`) because an explicit override rule was missing from the delegation matrix. |
| `2026-06-05 14:50` | `sysadmin01` | **Remediation.** Modified the GPO's advanced delegation properties. Added an explicit Read-Only, Deny Apply Group Policy exception for `GG_IT`. Forced an immediate policy synchronization. Ticket closed. |

---

### Technical Documentation

#### Problem
Members of the domain engineering and IT support teams were completely blocked from executing command-line interpreters (`cmd.exe`) and scripting hosts (`powershell.exe`). This restriction prevented the administration team from using essential management utilities, scripts, or troubleshooting command strings.

#### Cause
The root issue was identified as a **GPO Scope Error** (referencing `image_c3c1e2.png`). A security enforcement policy designed to harden standard client desktops was linked globally to the root domain container. Because the policy was filtered to target the broad "Authenticated Users" identity block, it inadvertently applied to all administrative user objects as well. Without an explicit exemption rule, the operating system treated the IT staff as unprivileged endpoints.

#### Solutions
1. Logged into the primary Domain Controller (`DC01`) using the break-glass Domain Administrator account.
2. Opened the **Group Policy Management Console** (`gpmc.msc`).
3. Selected the policy object causing the block: `GPO_User_Interface_Restrictions`.
4. Navigated away from the Scope layout and clicked directly on the **Delegation** tab at the top.
5. Clicked on the **Advanced...** button in the lower right-hand corner to display the explicit Access Control Lists (ACL).
6. Clicked **Add...**, searched for the IT department's global security group: **GG_IT**.
7. Highlighted `GG_IT` within the permissions box, scrolled down the list to **Apply Group Policy**, and checked the box under the **Deny** column.
*(Note: In Active Directory permissions architecture, an explicit Deny rule takes absolute precedence over an implicit or inherited Allow rule).*
8. Clicked **Apply** and confirmed the security exception warning dialog.
9. Switched back to the administrative client machine (`IT-ADM02`), launched a terminal, and forced a configuration reload:
```cmd
   gpupdate /force
```
