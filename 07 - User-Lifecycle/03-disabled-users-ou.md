# Organizational Unit Configuration: Disabled Users OU

This document details the purpose, structural design, and automated management baseline for the **Disabled Users** Organizational Unit (OU) within liontech.local.
---

### Purpose & Architectural Design
Following the Active Directory environment design in `OUs.jpg`, the `Disabled Users` OU acts as a centralized isolation zone at the root level of the domain tree. 

When employees exit the organization or undergo extended leaves of absence, their accounts are moved here immediately. This provides several operational advantages:
* **Administrative Clarity:** Separates inactive identities from active production departments to prevent directory clutter.
* **Security Isolation:** Allows system administrators to enforce strict Group Policy restrictions specifically designed to lock down stale user objects.
* **Compliance & Retention:** Retains historical account data, internal security IDs (SIDs), and mailbox associations for external audits without keeping accounts active.

---

### Group Policy Object (GPO) Hardening

To ensure absolute containment, a dedicated hardening GPO named **GPO_Disabled_Users_Restrictions** is linked explicitly to this OU. If a disabled account is accidentally re-enabled, this GPO completely blocks interactions with the domain network:

| Policy Setting Type | GPO State / Rule | Security Objective |
| :--- | :--- | :--- |
| **Logon Hours Restriction** | Denied 24/7 | Prevents interactive network login attempts at any hour of the day. |
| **Interactive Logon** | Deny local and RDP logons | Blocks the account from initiating console or Remote Desktop sessions on any workstation or server. |
| **Workstation Restrictions** | Log on to: `NULL` | Explicitly strips the account's permission to authenticate against any computer object in the domain. |

---

### Operational Lifecycle Management
1. **Move Target:** Accounts are dropped here via the offboarding script or manual drag-and-drop orchestration in ADUC.
2. **Retention Baseline:** Inactive accounts rest in this container for a standard corporate retention window (e.g., 90 days) to allow for data migration, mailbox delegation, or sudden re-hiring workflows.
3. **Purge Automation:** Scheduled maintenance scripts regularly evaluate objects inside this OU container and permanently delete accounts whose description timestamps exceed the retention policy timeline.

---

### Deployment Verification & Screenshots

#### 1. Directory Tree Hierarchy Mapping
Show a screenshot of Active Directory Users and Computers (ADUC) showing the `Disabled Users` OU placed directly at the root level of the `liontech.local` domain hierarchy, separate from production department OUs.
![Disabled Users OU Architecture Tree](../../images/ad-disabled-ou-structure.png)

#### 2. Isolated Object Status Verification
Show the contents of the `Disabled Users` OU container. The user account icons must explicitly display the **downward-pointing black arrow** ⬇, proving that every account residing inside this isolation folder is structurally disabled.
![Disabled Users OU Objects List](../../images/ad-disabled-ou-objects.png)
