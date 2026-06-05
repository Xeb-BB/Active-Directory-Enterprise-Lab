### Operational Execution Breakdown

#### Phase 1: Access Revocation & Hardening
When an employee leaves the organization, security demands immediate containment of the account:
* **Account Disabling:** The account status flag is flipped to **Disabled**, entirely preventing new network and workstation authentications.
* **Credential Scrambling:** The active password is overwritten with a random 36-character string, blocking unauthorized password reset exploits.

#### Phase 2: Cleansing & Relocation
* **Group Stripping:** The user is removed from their assigned security group (e.g., `GG_HR`). This guarantees that even if the account is accidentally re-enabled, it has **zero rights** to read or write to sensitive departmental network storage file shares.
* **OU Relocation:** Following the Active Directory architecture defined in `OUs.jpg`, the user object is moved entirely out of its original production department folder and safely archived into the **Disabled Users** OU.

---

### Deployment Verification & Screenshots

To verify that the offboarding system successfully completes all compliance requirements, document the run with the following system verification screenshots:

#### 1. Automation Execution Traces
Show a screenshot of the administrative console or PowerShell environment completing the execution, verifying that group stripping and relocation functions successfully closed out.
![PowerShell Offboarding Execution Output](../../images/offboarding-script-execution.png)

#### 2. Account Status Check (ADUC)
Show the account properties screen within Active Directory Users and Computers. The icon must explicitly show a **downward-pointing black arrow** ⬇ over the user avatar, confirming that the account is disabled.
![AD Disabled User Account Properties](../../images/offboarding-account-disabled.png)

#### 3. Security Group Cleansing Verification
Show the user's **Member Of** properties tab. This screenshot must prove that the individual has been completely stripped of their departmental `GG_*` roles, retaining only the default, non-privileged `Domain Users` baseline.
![AD User Group Deprovisioning Verification](../../images/offboarding-group-removal.png)

#### 4. OU Relocation Mapping
Show the final location of the user object resting inside the **Disabled Users** OU container at the root level of your domain hierarchy tree, matching the original layout architecture.
![Disabled Users OU Placement](../../images/offboarding-ou-relocation.png)
