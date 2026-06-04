# Global Security Group: GG_FINANCE

This document details the identity baseline, membership criteria, and access control boundaries for the Finance global security group within liontech.local.

---

### Identity & Group Properties
Following the organizational blueprint outlined in OUs_3.jpg, this security group aggregates all personnel responsible for company accounting, payroll execution, tax compliance, and financial reporting.

* **Group Name:** GG_FINANCE
* **SAM Account Name:** GG_FINANCE
* **Group Type:** Security
* **Group Scope:** Global
* **AD Container Location:** `liontech.local/Security Groups`

---

### Membership Baseline
Membership is strictly limited to authenticated accounting and finance professionals. Because this group handles sensitive financial data, strict credential verification and segregation of duties are maintained.

#### Active Members (Per OUs_3.jpg)
* `fin.admin` (Finance Controller / Accounting Manager)
* `fin.staff01` (Senior Accountant)
* `fin.staff02` (Payroll Specialist)

---

### Access Control Boundaries & Permissions
This group acts as the primary security barrier for financial data, restricting sensitive ledgers and banking information from other departments while providing necessary operational access to its members:

| Resource Type | Target Object | Assigned Permission | Business Justification |
| :--- | :--- | :--- | :--- |
| **File Share** | `\\liontech.local\Shares\Finance` | Read / Write (Modify) | Secure access to company ledgers, payroll spreadsheets, balance sheets, and tax fillings. |
| **Cross-Dept Share** | `\\liontech.local\Shares\General` | Read-Only | General employee template directory access. |
| **Local Security** | Workstation Local Groups | None (Standard User) | Enforces least privilege on local endpoints (`FIN-PCs`) to minimize malware risks to financial tools. |

---

### Lifecycle & Auditing
* **Provisioning:** Requests for group addition require written sign-off from `fin.admin` and verification of background compliance protocols before execution by IT.
* **Deprovisioning:** Upon department transfer or termination, members are immediately stripped of this identity token, and the account is moved directly to the `Disabled Users` OU to avoid stale permission exploitation.
* **Auditing:** Due to strict accounting regulations, membership shifts generate critical event logs (Event ID `4728` / `4729`) that are captured for regular external auditing and compliance checks.

---

### Deployment Verification & Screenshots

#### 1. Security Group Object Properties
This screenshot verifies the object attributes, global scope, and security group type within Active Directory Users and Computers (ADUC).
![GG_FINANCE Object Configuration](../../images/ad-group-finance-properties.png)

#### 2. Member Attribute Enumeration
A view of the Members tab confirming that only the designated finance accounts from the domain blueprint are populated.
![GG_FINANCE Active Members List](../../images/ad-group-finance-members.png)
