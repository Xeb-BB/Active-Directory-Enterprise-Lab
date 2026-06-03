# Project Objectives

The primary objective of this enterprise lab is to demonstrate a comprehensive understanding of Windows Server administration, network security hardening, and operational efficiency within a corporate domain. 

### Key Technical Milestones

* **Architect a Scalable AD DS Hierarchy:** Establish a clean, industry-standard Organizational Unit (OU) structure that separates administrative accounts, standard users, service accounts, and workstations.
* **Implement Role-Based Access Control (RBAC):** Use Global Security Groups (`GG_`) to map user identities to specific departmental file shares, ensuring users only access data necessary for their jobs.
* **Enforce Operating System Hardening via GPO:** Deploy restrictive Group Policies to protect endpoints against common insider threats and accidental malware execution (e.g., blocking CLI tools, disabling local admin escalation, enforcing screen locks).
* **Optimize Storage with FSRM:** Deploy File Server Resource Manager policies to actively screen and block unnecessary or dangerous file extensions from clogging corporate storage shares.
* **Eliminate Human Error via Automation:** Standardize operational workflows by scripting repeatable processes like user onboarding and offboarding.
