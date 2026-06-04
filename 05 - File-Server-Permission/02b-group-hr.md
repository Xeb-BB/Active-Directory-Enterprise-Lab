# Global Security Group: GG_HR

This document details the identity baseline, membership criteria, and access control boundaries for the Human Resources global security group within liontech.local.

---

### Identity & Group Properties
Following the organizational structure outlined in OUs_3.jpg, this security group aggregates all personnel with data-handling responsibilities regarding employee PII, payroll administration, and hiring workflows.

* **Group Name:** GG_HR
* **SAM Account Name:** GG_HR
* **Group Type:** Security
* **Group Scope:** Global
* **AD Container Location:** `liontech.local/Security Groups`

---

### Membership Baseline
Membership is strictly restricted to authenticated personnel assigned to the Human Resources department. Cross-departmental membership is prohibited to prevent privilege creep.

#### Active Members
* `hr.admin` (Department Head / Object Owner)
* `hr.staff01` (HR Specialist)
* `hr.staff02` (HR Specialist)

---

### Access Control Boundaries & Permissions
Unlike Group Policy Objects that dictate system behaviors, this group is used exclusively as a security principal for Access Control Lists (ACLs) across the domain network:

| Resource Type | Target Object | Assigned Permission | Business Justification |
| :--- | :--- | :--- | :--- |
| **File Share** | `\\liontech.local\Shares\HR` | Read / Write (Modify) | Full management of onboarding docs, performance reviews, and personnel files. |
| **Active Directory** | `Human Resources` OU | Read All Properties | Allows HR staff to look up user attributes within their own department directory. |
| **Local Security** | Workstation Local Groups | None (Standard User) | Members retain non-administrative access on local endpoints (`HR-PCs`) to protect host integrity. |

---

### Lifecycle & Auditing
* **Provisioning:** New members must be formally approved by the HR Department Head before being added to the group by IT Service Desk staff.
* **Deprovisioning:** Upon termination or department transfer, users are immediately stripped of this group membership and moved to the `Disabled Users` OU.
* **Auditing:** Membership modifications trigger Event ID `4728` (A member was added to a security-enabled global group) and Event ID `4729` (A member was removed) in the Windows Security Log for compliance tracking.

---

### Deployment Verification & Screenshots

#### 1. Security Group Object Properties
This screenshot verifies the object attributes, scope, and container location within Active Directory Users and Computers (ADUC).
![GG_HR Object Configuration](../../images/ad-group-hr-properties.png)

#### 2. Member Attribute Enumeration
A view of the Members tab verifying that only the authorized HR accounts identified in the system blueprint are populated.
![GG_HR Active Members List](../../images/ad-group-hr-members.png)
