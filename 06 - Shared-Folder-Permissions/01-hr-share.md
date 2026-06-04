# Network Share Configuration: HR Share

This document details the deployment parameters, access controls, and storage compliance rules for the secure Human Resources department file share.

---

### Infrastructure Provisioning
* **Share Name:** HR Share
* **Network Path (UNC):** `\\DC01\HR-Share$`
* **Storage Volume Location:** `E:\Shares\HR-Share`
* **Visibility:** Hidden Share (Appended with `$` to prevent directory browsing by non-HR personnel)

---

### Access Control Matrix (Overlapping Permissions)
To guarantee strict data confidentiality and fulfill compliance regulations, the share combines explicit Share-level and NTFS file system permissions.

| Identity Principal | Share Permission | NTFS Permission | Effective Right | Operational Role |
| :--- | :--- | :--- | :--- | :--- |
| **GG_HR** | Change | Modify | **Read, Write, Modify** | Full day-to-day data management. |
| **Domain Admins** | Full Control | Full Control | **Full Control** | Backup execution and permission inheritance fixes. |
| **Everyone / Others** | None (No entry) | None (Inheritance Stripped) | **No Access** | Blocked entirely from discovering or opening data. |

---

### FSRM File Screening Restrictions
Managed by the File Server Resource Manager (FSRM) role to enforce a clean storage baseline and prevent unauthorized execution environments or media hosting.

* **Screening Type:** Active Screening (Blocks file creation and modifications)
* **Targeted Block Groups:** `Executables` & `Audio/Video`
* **Enforced Extensions:** `.exe`, `.msi`, `.mp3`, `.mp4`
* **Trigger Action:** Event ID logged to System Security Journal; user receives an "Access Denied / Permissions Required" prompt if saving restricted extensions.

---

### Deployment Verification & Screenshots

#### 1. Overlapping Security Properties (Share vs. NTFS)
This view confirms that inheritance has been broken on the `E:\Shares\HR-Share` folder path and explicitly locked down to `GG_HR` and `Domain Admins`.
![HR Share NTFS Security Settings](../images/fs-hr-ntfs-permissions.png)

#### 2. FSRM Active Screen Policy Enforcement
A screenshot from the File Server Resource Manager dashboard verifying the active blockade of installer assets and multimedia files.
![FSRM HR Screening Rule](../images/fs-hr-fsrm-screen.png)
