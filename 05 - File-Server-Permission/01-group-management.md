# Global Security Group: GG_MANAGEMENT

This document details the identity baseline, membership criteria, and access control boundaries for the Executive Management global security group within liontech.local.

---

### Identity & Group Properties
Following the organizational blueprint outlined in OUs_3.jpg, this security group aggregates all high-level executives and operations managers who require domain-wide visibility and high-tier data access.

* **Group Name:** GG_MANAGEMENT
* **SAM Account Name:** GG_MANAGEMENT
* **Group Type:** Security
* **Group Scope:** Global
* **AD Container Location:** `liontech.local/Security Groups`

---

### Membership Baseline
Membership is strictly reserved for C-suite executives and core operational directors. Due to the high sensitivity of data available to this group, entry requires explicit provisioning authorization.

#### Active Members (Per OUs_3.jpg)
* `ceo.admin` (Chief Executive Officer)
* `manager.ops` (Operations Manager)

---

### Access Control Boundaries & Permissions
This group serves as a primary security principal across the domain's access control lists to guarantee high-level managerial visibility while maintaining separation from raw IT infrastructure control:

| Resource Type | Target Object | Assigned Permission | Business Justification |
| :--- | :--- | :--- | :--- |
| **File Share** | `\\liontech.local\Shares\Management` | Full Control | Complete access to corporate strategies, board reviews, and legal documentation. |
| **Cross-Dept Share** | `\\liontech.local\Shares\Finance` & `\Sales` | Read-Only | Allows high-level monitoring of corporate revenue, pipeline trajectories, and performance metrics. |
| **Active Directory** | Entire Domain Tree | Read All Properties | Provides high-tier directory audit capabilities without giving destructive write privileges. |

---

### Lifecycle & Auditing
* **Provisioning:** Membership changes can only be initiated by the CEO or a designated board member, executed exclusively by the Senior Systems Administrator.
* **Deprovisioning:** Upon executive offboarding, membership is instantly revoked, access tokens are invalidated, and the account is moved directly to the `Disabled Users` OU.
* **Auditing:** This group is subject to high-priority auditing. Any modifications generate immediate security event triggers (Event ID `4728` / `4729`) within the centralized log infrastructure.
