# Remove Run as Administrator Policy

This document details the configuration and security baseline of the Remove Run as Administrator Policy for liontech.local.

---

### Target Scope
* Scope: Workstations Organizational Unit (OU)
* Target Objects: All standard domain user accounts operating on network endpoints.

### Why is this Important?
Allowing standard users to easily invoke elevation options encourages attempts to bypass system controls or install unauthorized software. By stripping the context menu options for administrative execution, the system enforces the principle of least privilege, reduces user confusion, and prevents accidental modifications to the underlying operating system.

---

### GPO Configuration Details

Path: User Configuration > Policies > Administrative Templates > Start Menu and Taskbar

#### Context Menu Restriction Rules
| Policy Setting | Value | Security Purpose |
| :--- | :--- | :--- |
| **Remove "Run as administrator" from Start** | `Enabled` | Hides the elevated run command context option from the Windows Start Menu, preventing standard users from attempting credential elevation on built-in utilities or applications. |

---

### Deployment Verification & Screenshots

#### 1. Group Policy Management Editor Configuration
This screenshot verifies the start menu restriction policy parameter configured on the Primary Domain Controller (DC01).
![Remove Run As Admin GPO Configuration](../images/gpo-removeadmin-config.png)

#### 2. Client-Side Enforcement Verification
Right-clicking an application or shortcut on a standard client machine (e.g., `SALES-PC01`) shows that the "Run as administrator" option is completely removed from the context menu interface.
![Client Verification Output](../images/gpo-removeadmin-client-verify.png)
