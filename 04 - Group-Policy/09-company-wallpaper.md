# Company Wallpaper Policy

This document details the configuration and operational baseline of the centralized Corporate Wallpaper Policy for `liontech.local`.

---

### Target Scope
* **Scope:** Domain-Wide (`liontech.local` root level)
* **Target Objects:** All standard and administrative user desktop environments.

### Why is this Important?
Setting a unified corporate background serves two enterprise purposes: **Branding Consistency** across all company-issued hardware and **Compliance/Security Messaging**. By embedding an "Authorized Use Only" or confidentiality warning directly onto the desktop image, users are continually reminded of company policy regarding asset security.

---

### GPO Configuration Details

**Path:** `User Configuration` > `Policies` > `Administrative Templates` > `Desktop` > `Desktop`

#### Wallpaper Rules
| Policy Setting | Value | Operational Purpose |
| :--- | :--- | :--- |
| **Desktop Wallpaper** | `Enabled` | Enforces a specific file path to the approved `.jpg` or `.png` asset image. |
| **Wallpaper Name** | `\\liontech.local\NETLOGON\wallpaper.jpg` | Pulls the image from the centralized domain replication share to ensure it is always accessible. |
| **Wallpaper Style** | `Fill` | Automatically resizes and stretches the image cleanly across any display resolution. |

---

### Deployment Verification & Screenshots

#### 1. Group Policy Management Editor Configuration
This screenshot verifies the wallpaper file path and style parameters as configured on the Primary Domain Controller (`DC01`).
![Company Wallpaper GPO Configuration](../images/gpo-wallpaper-config.png)

#### 2. Client-Side Enforcement Verification
A screenshot of a domain workstation desktop showing the applied corporate asset background immediately upon user authentication.
![Client Verification Output](../images/gpo-wallpaper-client-verify.png)
