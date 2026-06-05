# Troubleshooting Case Study #5: User Desktops Showing Black Screens

This entry covers the diagnostic workflow, GPO path resolution analysis, and remediation steps applied to resolve an issue where users were greeted with completely black desktop backgrounds instead of the corporate wallpaper.

---

### The Real-World Scenario
David Miller (`it.staff03`) is an IT Support Specialist in the IT Department working on workstation `IT-PC03`. Following a newly pushed group policy change intended to enforce the company branding, multiple users across various departments begin calling the helpdesk complaining that their desktop wallpaper has suddenly disappeared, leaving them with a solid, unchangeable black background. 

David runs a quick policy check and confirms that the GPO is actively applying to the client machines. However, the operating system fails to render the image asset, disrupting the standardized user environment baseline.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-005
* **Priority:** Medium
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** `it.staff03` (David Miller)
* **Department:** IT Department
* **Affected Asset ID:** `IT-PC03` (Global Environment Issue)

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 14:10` | `sysadmin01` | **Ticket Opened.** Multiple user endpoints reporting solid black desktop backgrounds after corporate wallpaper policy deployment. |
| `2026-06-05 14:35` | `sysadmin01` | **Diagnostic Run.** Inspected client registry paths under `HKCU\Control Panel\Desktop`. Found that the `Wallpaper` string is pointing to `C:\Users\Administrator\Desktop\Wallpaper.jpg`. |
| `2026-06-05 14:50` | `sysadmin01` | **Root Cause Analysis.** Identified a File Share Permissions error. The GPO wallpaper path pointed to a local directory on the administrator's account which standard domain computer accounts and users have no permissions to access or read over the network. |
| `2026-06-05 15:05` | `sysadmin01` | **Remediation.** Relocated the image asset to the public `SYSVOL` replication share on `DC01` and updated the GPO source path to a valid UNC string. Forced policy refresh. Ticket closed. |

---

### Technical Documentation

#### Problem
Instead of displaying the corporate logo branding background, multiple client workstations displayed a broken, solid black background. While shortcuts and the taskbar functional systems operated normally, the desktop wallpaper environment element failed to load entirely.

#### Cause
The root issue was identified as a **File Share Permissions** error (referencing `image_c3c1e2.png`). The administrative engineer who built the Group Policy Object pointed the source path of the desktop image background directly to a folder within a local administrator's profile path (`C:\Users\Administrator\...`). Because this path is strictly local to that administrative workstation and completely inaccessible to unprivileged domain user sessions over the network, the client-side shell engine threw an access-denial error and defaulted to a black space template.

#### Solutions
1. Logged directly into the primary Domain Controller (`DC01`).
2. Copied the target corporate background file (`Wallpaper.jpg`) into the public Active Directory replication container:
*(Note: Placing the file here ensures it automatically replicates across all Domain Controllers and provides universal Read access to the "Authenticated Users" group).*
3. Opened the **Group Policy Management Editor** (`gpmc.msc`) and modified the failing policy: `GPO_Corporate_Wallpaper_Security`.
4. Navigated to **User Configuration -> Policies -> Administrative Templates -> Desktop -> Desktop -> Desktop Wallpaper**.
5. Changed the local file path string to the universal UNC network string:
6. Switched to the client terminal (`IT-PC03`), opened a Command Prompt, and pulled down the architectural structural fix:
```cmd
gpupdate /force
```
#### Evidence
PICTURE

---
### Deployment Verification & Screenshots
To properly display evidence of your fix in your GitHub project, capture and name the following snapshots:

#### 1. Group Policy Path Modification Panel
What to capture: Open the Group Policy Management Editor on the Domain Controller showing the properties of the Desktop Wallpaper setting.

Focus: Ensure the "Wallpaper Name" text field clearly displays the updated, valid UNC path pointing to the SYSVOL share directory rather than a local drive letter.

File Save Path: images/troubleshoot-05-path-fix.png

#### 2. Environment Rendering Verification (Desktop View)
What to capture: Take a screenshot of the entire desktop view of a client workstation after running the manual policy pull.

Focus: Show that the solid black screen has been successfully replaced by the rendered corporate brand wallpaper image, alongside active desktop icons.

File Save Path: images/troubleshoot-05-wallpaper-restored.png
