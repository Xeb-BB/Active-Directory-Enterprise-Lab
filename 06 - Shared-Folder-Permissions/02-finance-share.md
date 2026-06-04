# Network Share Configuration: Finance Share

This document details the deployment parameters, access controls, and storage compliance rules for the secure accounting and finance file share.

---

### Infrastructure Provisioning
* **Share Name:** Finance Share
* **Network Path (UNC):** `\\DC01\FIN-Share$`
* **Storage Volume Location:** `E:\Shares\FIN-Share`
* **Visibility:** Hidden Share (`$`)

---

### Access Control Matrix (Overlapping Permissions)

| Identity Principal | Share Permission | NTFS Permission | Effective Right | Operational Role |
| :--- | :--- | :--- | :--- | :--- |
| **GG_FINANCE** | Change | Modify | **Read, Write, Modify** | Ledger and ledger asset manipulation. |
| **Domain Admins** | Full Control | Full Control | **Full Control** | Database recovery and management. |
| **Everyone / Others** | None | None | **No Access** | Blocked from auditing trails. |

---

### FSRM File Screening Restrictions
Protects the financial records environment against payload injection, unexpected file corruption, or accidental local image archives.

* **Screening Type:** Active Screening
* **Targeted Block Groups:** `Executables` & `Image Files`
* **Enforced Extensions:** `.exe`, `.msi`, `.jpg`, `.png`
* **Justification:** Financial auditing files must remain structured; blocks local camera assets or desktop backgrounds from wasting volume storage space.

---

### Deployment Verification & Screenshots

#### 1. Advanced Security Auditing Configuration
Verifies that access monitoring policies are bound to the `GG_FINANCE` security principal for regulatory data tracking.
![Finance Security ACL Properties](../images/fs-fin-acl-properties.png)

#### 2. FSRM File Screen Validation Block
An execution trace proving that dropping an unauthorized `.exe` onto the file mount is instantly dropped by the server system.
![FSRM Finance Screen Execution](../images/fs-fin-fsrm-block.png)
