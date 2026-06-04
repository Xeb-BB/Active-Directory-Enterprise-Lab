# Global Security Group: GG_IT

This document details the identity baseline, membership criteria, and access control boundaries for the Information Technology global security group within liontech.local.

---

### Identity & Group Properties
Following the organizational blueprint outlined in OUs_3.jpg, this security group aggregates all tier-1 helpdesk personnel, systems administrators, and network engineers responsible for maintaining core domain infrastructure.

* **Group Name:** GG_IT
* **SAM Account Name:** GG_IT
* **Group Type:** Security
* **Group Scope:** Global
* **AD Container Location:** `liontech.local/Security Groups`

---

### Membership Baseline
Membership is limited strictly to verified technical operations staff. Because members of this group handle administrative tools and deployment shares, strong account monitoring is enforced.

#### Active Members (Per OUs_3.jpg)
* `sysadmin01` (Senior Systems Administrator)
* `helpdesk01` (IT Support Specialist)

---

### Access Control Boundaries & Permissions
This group serves as the primary gateway for corporate technical resources. It separates internal IT administration tools and configuration data from standard corporate environments:

| Resource Type | Target Object | Assigned Permission | Business Justification |
| :--- | :--- | :--- | :--- |
| **File Share** | `\\liontech.local\Shares\IT-Internal` | Full Control | Hosting of deployment packages, automated scripts, network topologies, and standard operating procedures. |
| **Cross-Dept Share** | `\\liontech.local\Shares\General` | Read / Write (Modify) | Maintaining corporate templates and updating company-wide software deployment folders. |
| **Group Policy** | GPO Exclusions / Filters | Read / Apply (Custom) | Applied to Item-Level Targeting constraints to bypass user environment restrictions (e.g., keeping CMD and PowerShell open for IT staff). |

---

### Lifecycle & Auditing
* **Provisioning:** New IT staff addition requests must come directly from the IT Director. Due to the high level of network access inherited, accounts require immediate enrollment in multi-factor tracking protocols.
* **Deprovisioning:** In the event of offboarding or role changes, IT accounts are stripped of this group token within minutes. Session revocation commands are pushed domain-wide, and the object is archived in the `Disabled Users` OU.
* **Auditing:** This group is subject to intense operational surveillance. Any modifications generate priority security event logs (Event ID `4728` / `4729`), triggering immediate alerts to security tracking mirrors.

---

### Deployment Verification & Screenshots

#### 1. Security Group Object Properties
This screenshot verifies the technical team object attributes, global scope, and security group type within Active Directory Users and Computers (ADUC).
![GG_IT Object Configuration](../../images/ad-group-it-properties.png)

#### 2. Member Attribute Enumeration
A view of the Members tab confirming that the authorized systems engineering and service desk accounts from the infrastructure blueprint are active.
![GG_IT Active Members List](../../images/ad-group-it-members.png)
