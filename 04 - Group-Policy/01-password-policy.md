# Domain Password & Account Lockout Policy

This document details the configuration and security baseline of the domain-wide Password Policy for `liontech.local`.

---

### Target Scope
* **Scope:** Domain-Wide (`liontech.local` root level)
* **Target Objects:** All standard and administrative domain user accounts.

### Why is this Important?
Default Windows settings allow weak passwords and unlimited login attempts. This policy neutralizes **Brute-Force** and **Dictionary Attacks** by forcing strong credential hygiene and automatically freezing accounts after multiple failed attempts.

---

### GPO Configuration Details

**Path:** `Computer Configuration` > `Policies` > `Windows Settings` > `Security Settings` > `Account Policies`

#### 1. Password Rules
| Policy Setting | Value | Security Purpose |
| :--- | :--- | :--- |
| **Minimum password length** | `12 Characters` | Prevents short, easily guessable passwords. |
| **Password complexity requirements** | `Enabled` | Requires uppercase, lowercase, numbers, and symbols. |
| **Enforce password history** | `24 Remembered` | Stops users from immediately reusing old passwords. |
| **Maximum password age** | `90 Days` | Limits the lifespan of a silently leaked credential. |

#### 2. Lockout Rules
| Policy Setting | Value | Security Purpose |
| :--- | :--- | :--- |
| **Account lockout threshold** | `5 Attempts` | Freezes the account before automated scripts can guess the password. |
| **Account lockout duration** | `30 Minutes` | The amount of time the account remains locked. |
| **Reset lockout counter after** | `30 Minutes` | Clears the failed attempts history after a safe period. |

---

### Deployment Verification & Screenshots

#### 1. Group Policy Management Editor Configuration
This screenshot verifies the policy parameters as configured on the Primary Domain Controller (`DC01`).
![Password Policy GPO Configuration](../images/gpo-password-config.png)

#### 2. Client-Side Enforcement Verification (`net accounts`)
Running `net accounts` on a domain workstation proves the local machine is pulling and enforcing the security policy.
![Client Verification Output](../images/gpo-password-client-verify.png)
