# Troubleshooting Case Study #2: GPO Changes Not Applying

This entry covers the diagnostic workflow, policy processing analysis, and remediation steps applied to resolve a failure where new Group Policy settings were not taking effect on a user endpoint.

---

### The Real-World Scenario
An IT Administrator has configured a critical security wallpaper policy to apply across the enterprise. The change is intended for John Smith (`finance01`), a Senior Accountant working in the Finance department on workstation `FIN-PC01`. 

Despite several reboots and hours of waiting, John reports that his desktop environment remains unchanged and still shows the default Windows background. The administrator attempts to manually trigger the policy pull from the client machine, but the security workspace updates refuse to take effect. The system baseline appears to be ignoring the new compliance policies completely.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-002
* **Priority:** Low
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** `finance01` (John Smith)
* **Department:** Finance Department
* **Affected Asset ID:** `FIN-PC01`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 10:15` | `sysadmin01` | **Ticket Opened.** User reports corporate security wallpaper updates are not reflecting on `FIN-PC01`. |
| `2026-06-05 10:40` | `sysadmin01` | **Diagnostic Run.** Generated a Group Policy results report (`gpresult /h`). Found that the Wallpaper Policy GPO is being completely ignored by the client workstation during evaluation. |
| `2026-06-05 11:05` | `sysadmin01` | **Root Cause Analysis.** Discovered that the GPO was mistakenly linked to the Computers OU, even though the policy itself contains User Configuration settings. Relinked the object to the Finance Users OU and forced a policy update. Ticket closed. |

---

### Technical Documentation

#### Problem
New environmental configurations applied via Group Policy Objects (GPOs) were completely failing to deploy to the end-user profile on workstation `FIN-PC01`. Manually attempting to pull down active directory changes on the terminal interface resulted in a success message, yet the localized environment properties remained entirely unmodified.

#### Cause
The issue was identified as a **Targeting Mismatch** (referencing `image_c3c1e2.png`). The Group Policy Object was linked directly to a Computer Organizational Unit (OU) container. However, the policy itself contained only configurations modified under the **User Configuration** node (Desktop Wallpaper management). Because computer objects do not process User configuration trees from GPOs linked directly to their own structural containers, the policy was effectively orphaned and bypassed by the evaluation engine.

#### Solutions
1. Opened the **Group Policy Management Console** (`gpmc.msc`) on the domain controller (`DC01`).
2. Located the misconfigured link under the `Computers` OU structure.
3. Right-clicked the rogue link and selected **Delete** (removing the link location, not the GPO itself).
4. Navigated down the structure to the target **Finance Users OU** where the active user account (`finance01`) resides.
5. Right-clicked the OU container, selected **Link an Existing GPO**, and mapped the target wallpaper management policy object.
6. Switched to the client machine (`FIN-PC01`), opened an administrative Command Prompt, and forced an instant background policy refresh:
```cmd
   gpupdate /force
```
#### Evidence
PICTURE

---
### Deployment Verification Screenshots
To properly display evidence of your fix in your GitHub project, capture and name the following snapshots:

#### 1. Group Policy Management Console Alignment
What to capture: Open gpmc.msc on your Domain Controller. Expand your forest and domain trees.

Focus: Capture the navigation hierarchy pane clearly showing your target Group Policy Object linked safely beneath your User-based Organizational Unit (e.g., Finance or Human Resources users OU) instead of a Computer OU container.

File Save Path: images/troubleshoot-02-gpo-link.png

#### 2. Client Side GPResult Execution Trace
What to capture: Open a Command Prompt window on the client machine after running your update sequence. Type gpresult /r and press Enter.

Focus: Scroll to the "Applied Group Policies" header under the User Configuration section. Capture the terminal screen proving that your target policy object is now explicitly listed as successfully applied.

File Save Path: images/troubleshoot-02-gpresult-success.png
