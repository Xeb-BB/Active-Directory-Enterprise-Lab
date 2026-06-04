# Global Security Group: GG_SALES

This document details the identity baseline, membership criteria, and access control boundaries for the Sales global security group within liontech.local.

---

### Identity & Group Properties
Following the organizational blueprint outlined in OUs_3.jpg, this security group aggregates all frontline accounts executives, business development representatives, and sales managers responsible for driving revenue and client acquisition.

* **Group Name:** GG_SALES
* **SAM Account Name:** GG_SALES
* **Group Type:** Security
* **Group Scope:** Global
* **AD Container Location:** `liontech.local/Security Groups`

---

### Membership Baseline
Membership is assigned to all active members of the sales division. High staff turnover baselines require automated validation checks against active HR employment statuses.

#### Active Members (Per OUs_3.jpg)
* `sales.admin` (Sales Director / Department Head)
* `sales01` (Account Executive)
* `sales02` (Account Executive)
* `sales03` (Business Development Representative)

---

### Access Control Boundaries & Permissions
This group isolates high-velocity customer and pipeline information, ensuring sales staff have the required storage space to manage client files without revealing background corporate data:

| Resource Type | Target Object | Assigned Permission | Business Justification |
| :--- | :--- | :--- | :--- |
| **File Share** | `\\liontech.local\Shares\Sales` | Read / Write (Modify) | Management of active client proposals, pitch decks, contracts, and revenue targets. |
| **Cross-Dept Share** | `\\liontech.local\Shares\General` | Read / Write (Modify) | Collaboration on cross-department marketing assets and public templates. |
| **Local Security** | Workstation Local Groups | None (Standard User) | Enforces non-administrative execution on local endpoints (`SALES-PCs`) to match strict GPO baseline controls. |

---

### Lifecycle & Auditing
* **Provisioning:** Triggered by internal department hiring notification from `sales.admin`. Accounts are automatically provisioned and dropped into this group context upon deployment.
* **Deprovisioning:** Upon employee departure, the user token is cleared of this group affiliation within 1 hour to protect competitive company data and client leads. The object is then routed to the `Disabled Users` OU.
* **Auditing:** Group additions and extractions generate standard identity event logs (Event ID `4728` / `4729`) monitored by security baselines to prevent unauthorized privilege escalation.

---

### Deployment Verification & Screenshots

#### 1. Security Group Object Properties
This screenshot verifies the object attributes, global scope, and security group type within Active Directory Users and Computers (ADUC) for the Sales tier.
![GG_SALES Object Configuration](../../images/ad-group-sales-properties.png)

#### 2. Member Attribute Enumeration
A view of the Members tab verifying that the full sales pipeline roster from the design blueprint is properly mapped.
![GG_SALES Active Members List](../../images/ad-group-sales-members.png)
