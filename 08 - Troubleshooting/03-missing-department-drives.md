# Troubleshooting Case Study #3: Missing Department Share Drives

This entry covers the diagnostic workflow, security filtering analysis, and remediation steps applied to resolve an issue where users were missing their assigned departmental network storage drives.

---

### The Real-World Scenario
Marcus Vance (`sales01`) logs into his assigned domain workstation `SALES-PC01` after a scheduled corporate password update. He opens Windows File Explorer to pull up his active tracking pipelines, but the `S:\ Drive` (Sales Share) is missing from the interface. 

The IT Helpdesk verifies that Marcus is an active member of the `GG_SALES` security group and that his account rests inside the correct Organizational Unit. Other users within the same department report the exact same issue: their localized shortcuts and shared storage network drives have vanished completely.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-003
* **Priority:** High
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** `sales01` (Marcus Vance)
* **Department:** Sales Department
* **Affected Asset ID:** `SALES-PC01` (Multiple Department Endpoints)

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 11:20` | `sysadmin01` | **Ticket Opened.** Multiple Sales users reporting that their mapped `S:\ Drive` network storage target has disappeared. |
| `2026-06-05 11:45` | `sysadmin01` | **Diagnostic Run.** Analyzed client Event Viewer application logs. Found a Group Policy processing error (Event ID 1085) stating that the drive mapping GPO failed to apply due to lack of read permissions. |
| `2026-06-05 12:10` | `sysadmin01` | **Root Cause Analysis.** Microsoft security update MS16-072 changed how GPOs retrieve user settings; computer objects now require read permissions to user GPOs. This GPO only explicitly listed `GG_SALES` under Security Filtering. Added `Domain Computers` with Read access to the Delegation tab. Ticket closed. |

---

### Technical Documentation

#### Problem
Departmental shared storage drives mapped via Group Policy Preferences (GPP) failed to execute or render within the end-user desktop interface across multiple endpoints. Despite running manual policy syncs, the target network shares remained completely unreachable via their standard letter-mapped paths (`S:\ Drive`).

#### Cause
The root issue was identified as a **GPO Security Filtering** error (referencing `image_c3c1e2.png`). The drive-mapping GPO used explicit security filtering to restrict processing solely to the `GG_SALES` user group. However, following modern Windows security updates, Group Policy processing engines evaluate user-side policies utilizing the host machine's system security token. Because the **Domain Computers** group lacked read permissions to evaluate the policy object, the client engine dropped processing operations entirely.

#### Solutions
1. Opened the **Group Policy Management Console** (`gpmc.msc`) on the domain controller (`DC01`).
2. Selected the specific drive mapping policy object: `GPO_Sales_Drive_Mappings`.
3. Navigated away from the Scope tab and clicked on the **Delegation** tab at the top of the interface.
4. Clicked **Add...** and searched for the **Domain Computers** identity group.
5. Set the permissions matrix rule for Domain Computers to **Read** (leaving "Apply Group Policy" unchecked).
6. Switched to the client terminal (`SALES-PC01`) and triggered an administrative policy refresh block:
```cmd
   gpupdate /force
```
### Deployment Verification & Screenshots
To properly display evidence of your fix in your GitHub project, capture and name the following snapshots:

#### 1. Group Policy Delegation Configuration Panel
What to capture: Open gpmc.msc on the Domain Controller, highlight your target drive-mapping policy object, and select the Delegation tab.

Focus: Ensure the panel explicitly displays the Domain Computers group listed in the permissions summary list with Read access verified.

File Save Path: images/troubleshoot-03-delegation-fix.png

#### 2. Network Mapping Verification (File Explorer)
What to capture: Open This PC or File Explorer on the target user's desktop workstation after running the manual policy pull.

Focus: Capture the "Network locations" pane showing both the common storage volume and the newly mounted department secure drive mapping (S:) pointing directly to \\DC01\SALES-Share$.

File Save Path: images/troubleshoot-03-drives-restored.png
