# Network Share Configuration: Sales Share

This document details the deployment parameters, access controls, and storage compliance rules for the corporate sales and pipeline file share.

---

### Infrastructure Provisioning
* **Share Name:** Sales Share
* **Network Path (UNC):** `\\DC01\SALES-Share$`
* **Storage Volume Location:** `E:\Shares\SALES-Share`
* **Visibility:** Hidden Share (`$`)

---

### Access Control Matrix (Overlapping Permissions)

| Identity Principal | Share Permission | NTFS Permission | Effective Right | Operational Role |
| :--- | :--- | :--- | :--- | :--- |
| **GG_SALES** | Change | Modify | **Read, Write, Modify** | Management of proposals, contracts, and decks. |
| **Domain Admins** | Full Control | Full Control | **Full Control** | Core volume health retention. |
| **Everyone / Others** | None | None | **No Access** | Segmented away from sales targets. |

---

### FSRM File Screening Restrictions
Mitigates typical phishing vector delivery risks by denying common storage configurations used to wrap or execute software payloads.

* **Screening Type:** Active Screening
* **Targeted Block Groups:** `Executables` & `Zip Files`
* **Enforced Extensions:** `.exe`, `.msi`, `.zip`, `.rar`
* **Justification:** Prevents sales agents from processing incoming compressed containers locally on the server volume, bypassing external gate scanners.

---

### Deployment Verification & Screenshots

#### 1. File Share Definition Property Settings
Confirms that the hidden configuration parameter is applied properly to the SMB share infrastructure on the primary file pool host.
![Sales SMB Properties](../images/fs-sales-smb-share.png)
#### 2. FSRM File Screen Violation Event
Workstation output documenting an absolute transfer error window when dropping a compressed file format payload.
![Sales FSRM Enforced Catch](../images/fs-sales-fsrm-block.png)
