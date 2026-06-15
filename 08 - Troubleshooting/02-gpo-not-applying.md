# Troubleshooting Case Study #2: Sales Department Network Drive Failure

This entry covers the diagnostic workflow, policy processing analysis, and security permission remediation applied to resolve an infrastructure deployment failure where the **Sales-Folder** network drive failed to map onto user endpoints.

---

### The Real-World Scenario
As part of the production rollout for **LionTech Solutions Pte Ltd**, a centralized Group Policy Object (GPO) named `Map Drive` was deployed to automatically mount the shared **Sales-Folder** to the `S:` drive for all Sales personnel. 

However, when users like `sales01` log into workstation `SALES`, the deployment fails. For some users, the **S:** drive is completely missing from File Explorer. For others, the drive appears but displays a critical **Red X** icon, throwing an *"Access is Denied"* error when clicked. The Sales team is currently unable to access centralized files, halting business operations.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-002
* **Priority:** High
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** Sales Team
* **Department:** Sales
* **Affected Asset ID:** `SALES`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-15 09:15` | `sysadmin01` | **Ticket Opened.** Users report the **Sales-Folder** (`S:`) drive is either missing or inaccessible with a Red X error. |
| `2026-06-15 09:45` | `sysadmin01` | **Diagnostic Run.** Analyzed GPO Preferences. Found that Item-Level Targeting was checking for the **Sales OU** instead of the **GG_SALES** security group, causing the mapping to fail for users in different OU paths. |
| `2026-06-15 10:15` | `sysadmin01` | **Root Cause Analysis.** Identified that while the share was created, the **Security (NTFS)** tab on the server was missing the **GG_SALES** group, resulting in the "Access Denied" Red X. |
| `2026-06-15 11:00` | `sysadmin01` | **Remediation.** Updated targeting to reference the **GG_SALES** Security Group and added **Modify** NTFS permissions to the physical folder. Verified fix on client. Ticket closed. |

---

### Technical Documentation

#### Problem
The **Sales-Folder** failed to map correctly to user profiles on `SALES`. The system either skipped the mapping entirely or displayed it as a broken connection (Red X).

#### Cause
1. **Targeting Mismatch:** The GPO used **Item-Level Targeting** based on the user's location in the **Sales OU**. Because the GPO was linked to the **Workstations OU** (a computer container), evaluating cross-OU user paths caused the mapping logic to fail.
2. **Missing NTFS Permissions:** The physical folder on the server lacked the necessary **Modify** permissions for the **GG_SALES** group on the **Security** tab, even though the network **Sharing** tab was configured correctly.

#### Solutions
1. Opened the `Map Drive` GPO on the **Domain Controller (DC01)**.
2. Navigated to `User Configuration` > `Preferences` > `Windows Settings` > `Drive Maps`.
3. Edited the **Sales-Folder** properties and went to the **Common** tab > **Targeting...**.
4. Removed the old **Organizational Unit** rule and replaced it with a **Security Group** rule targeting **`LIONTECH\GG_SALES`**.
5. On the file server, right-clicked the physical **Sales-Folder**, went to the **Security** tab, and added **GG_SALES** with **Modify** permissions.
6. Instructed the user to **Sign Out** and **Sign In** to refresh their security tokens and pull the updated GPO configuration.

---

### Deployment Verification Evidence

To verify successful remediation of the network storage mapping architecture, the following engineering artifacts were captured from the deployment environment:

#### 1. Corrected Item-Level Targeting Configuration
The mapping preference logic was updated to evaluate user identity tokens (Security Group Memberships) rather than folder structural boundaries.

![GPO Drive Map Security Group Targeting](images/troubleshoot-02-targeting-fix.png)

#### 2. Successful Drive Mount & Data Visibility
Upon user session initialization, the client workstation properly evaluates the Active Directory group token, maps the assigned letter path, and securely opens the file structure with no permission conflicts.

![Successful Shared Drive Deployment](images/troubleshoot-02-sales-drive-success.png)
