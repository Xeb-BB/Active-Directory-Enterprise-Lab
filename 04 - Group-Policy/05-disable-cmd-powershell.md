# Disable CMD and PowerShell Policy

This document details the configuration and security baseline of the Disable CMD and PowerShell Policy for liontech.local.

---


### Target Scope
* Scope: All Standard User Organizational Units (HR, Finance, Sales)
* Target Objects: All non-IT standard domain user accounts.

### Why is this Important?
Standard business users have no operational reason to access command-line environments. Leaving command prompts open to standard staff enables **Internal System Exploration** and vulnerability hunting, allowing curious users or malicious actors to run scripts, enumerate network resources, or bypass system restrictions. Restricting these utilities enforces strict environment hardening.

---

### GPO Configuration Details

Path: User Configuration > Policies > Administrative Templates > System

#### Shell Restriction Rules
| Policy Setting | Value | Security Purpose |
| :--- | :--- | :--- |
| **Prevent access to the command prompt** | `Enabled` | Disables standard user access to `cmd.exe`. |
| **Disable the command prompt script processing also?** | `Yes` | Prevents the user from execution workarounds using batch (`.bat` or `.cmd`) files. |
| **Don't run specified Windows applications** | `Enabled` | Custom block list explicitly containing `powershell.exe` and `powershell_ise.exe` to prevent switching to the advanced shell environment. |

---

### Deployment Verification & Screenshots

#### 1. Group Policy Management Editor Configuration
This screenshot verifies the shell restriction configurations and application block lists configured on the Primary Domain Controller (DC01).
![CLI Restriction GPO Configuration](../images/gpo-cliblock-config.png)

#### 2. Client-Side Enforcement Verification
Attempting to launch the Command Prompt or PowerShell on a standard client machine instantly displays an error message stating, "The command prompt has been disabled by your administrator," and automatically terminates the session.
![Client Verification Output](../images/gpo-cliblock-client-verify.png)
