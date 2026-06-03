# Project Overview: 50-User Active Directory Enterprise Lab

This project simulates the end-to-end design, implementation, and deployment of a secure corporate network infrastructure tailored for a mid-sized organization of 50 employees (LionTech Solutions). 

Built entirely in a virtualized environment, this lab bridges the gap between theoretical network administration and enterprise-level production requirements. The infrastructure features a highly structured Active Directory Domain Services (AD DS) hierarchy, centralized Group Policy management, role-based data access controls, and automated user lifecycle management.

### Core Infrastructure Architecture

* **Domain Name:** `liontech.local`
* **Network Size:** 50 Users distributed across 5 core Organizational Units (OUs).
* **Primary Domain Controller (DC01):** Windows Server 2022 (Handling AD DS, DNS, DHCP, and FSRM File Services).
* **Workstations:** Windows 11 Pro client machines joined to the domain.

### Infrastructure Directory
The network is broken down into functional departments to enforce data isolation and targeted security policies:
* **Management:** Executive leadership with high-level corporate access.
* **Human Resources (HR):** Protectors of sensitive employee data and PII.
* **Finance:** Accounting and financial record managers.
* **Sales:** Front-facing client pipelines and marketing data.
* **IT Department:** Technical administrators managing infrastructure operations.
