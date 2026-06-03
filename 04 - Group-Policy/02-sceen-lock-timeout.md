# Screen Lock Timeout Policy

#### This document details the configuration and security baseline of the automated Screen Lock Timeout Policy for liontech.local.
---

### Target Scope
* Scope: Domain-Wide (All Organizational Units)
* Target Objects: All interactive domain user sessions on workstation endpoints.

### Why is this Important?
Unattended, unlocked workstations are a massive risk for **Physical Insider Threats** and unauthorized data access. If an employee leaves their desk without locking their computer, anyone walking by can view sensitive records, modify system settings, or masquerade as that user. Forcing an automatic screen lock mitigates this exposure.

---

### GPO Configuration Details

Path: User Configuration > Policies > Administrative Templates > Control Panel > Personalization

#### Screen Lock Rules
| Policy Setting | Value | Security Purpose |
| :--- | :--- | :--- |
| **Enable screen saver** | `Enabled` | Activates the subsystem required to trigger automated lock actions. |
| **Password protect the screen saver** | `Enabled` | Forces the user to re-enter their domain credentials to unlock the display. |
| **Screen saver timeout** | `900 Seconds (15 Minutes)` | Automatically locks the terminal after exactly 15 minutes of user inactivity. |
| **Force specific screen saver** | `scrnsave.scr` | Enforces the standard default Windows blank screen saver asset. |

---

### Deployment Verification & Screenshots

#### 1. Group Policy Management Editor Configuration
This screenshot verifies the personalization parameters and time limits configured on the Primary Domain Controller (DC01).
![Screen Lock GPO Configuration](../images/gpo-screenlock-config.png)

#### 2. Client-Side Enforcement Verification
Opening the local Screen Saver settings on a domain workstation shows the configurations greyed out and enforced with a message stating, "Some settings are managed by your organization."
![Client Verification Output](../images/gpo-screenlock-client-verify.png)
