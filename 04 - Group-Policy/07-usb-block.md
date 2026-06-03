# USB Storage Restriction Policy

This document details the configuration and security baseline of the USB Storage Restriction Policy for `liontech.local`.

---

### Target Scope
* **Scope:** Finance and HR Organizational Units (OUs)
* **Target Objects:** All standard user accounts within the Finance and HR departments.

### Why is this Important?
Unrestricted USB ports present two massive enterprise vulnerabilities: **Data Leakage** (insider threats copying sensitive company financial logs or HR PII onto a thumb drive) and **Malware Ingress** (users plugging in infected or malicious drives from home). This policy prevents unauthorized data movement on our most sensitive departments.

---

### GPO Configuration Details

**Path:** `User Configuration` > `Policies` > `Administrative Templates` > `System` > `Removable Storage Access`

#### Restriction Rules
| Policy Setting | Value | Security Purpose |
| :--- | :--- | :--- |
| **Removable Disks: Deny read access** | `Enabled` | Blocks users from opening or reading files from unauthorized external storage. |
| **Removable Disks: Deny write access** | `Enabled` | Prevents users from copying sensitive corporate data or PII to external devices. |

---

### Deployment Verification & Screenshots

#### 1. Group Policy Management Editor Configuration
This screenshot verifies the policy parameters as configured on the Primary Domain Controller (`DC01`) targeting the Finance and HR OUs.
![USB Storage Block GPO Configuration](../images/gpo-usb-config.png)

#### 2. Client-Side Enforcement Verification
Attempting to plug in a USB flash drive on an HR or Finance workstation throws an "Access is denied" error window when the user tries to open it.
![Client Verification Output](../images/gpo-usb-client-verify.png)
