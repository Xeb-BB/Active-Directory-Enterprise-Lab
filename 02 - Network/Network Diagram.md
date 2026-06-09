# Network Topology & Architecture Breakdown

This document provides a detailed breakdown of the current laboratory network diagram and analyzes how this setup functions.

---

## Detailed Analysis of the Current Diagram

The lab is deployed using a physical **Star Topology**, where all endpoints, servers, and administrative devices connect to a single central network switch. 

### 1. The Core Services Engine (DC01)
* **IP Address:** `192.168.1.10` (Static IP)
<p align="center">
  <img src="images/server-static.jpg" alt="Static-IP" width="90%">
</p>
* **Roles:** Active Directory Domain Services (AD DS), DNS Server, and DHCP Server.
* **Function:** DC01 acts as the brain of the network. Because Active Directory relies completely on name resolution to find services, DC01 handles all local DNS queries. It also features a DHCP scope running from `192.168.1.100` to `192.168.1.200` to automatically hand out IP addresses to workstations as they boot up.
<p align="center">
  <img src="images/dhcp-scope.jpg" alt="DHCP" width="90%">
</p>

### 2. The Network Perimeter & Management
* **Home Router (`192.168.1.1`):** Acts as the default gateway, providing internet access and basic routing for the lab environment.
* **Admin PC (`192.168.1.50`):** A dedicated workstation used by the IT administrator to configure the server and oversee network tasks.

### 3. Domain Client Endpoints
The workstations simulate separate corporate departments on a unified network scheme:
* **SALES-PC01 (`192.168.1.101 - 115`)** — Assigned to Human Resources.
* **FIN-PC01 (`192.168.1.116 - 120`)** — Assigned to Finance.
* **HR-PC01 (`192.168.1.121 - 125`)** — Assigned to Sales.
* **MGMT-PC01 (`192.168.1.130 - 135`)** — Assigned to IT Support.
* **IT-PC01 (`192.168.1.140 - 145`)** — Assigned to IT Support.

<p align="center">
  <img src="../Resources/diagram.png" alt="Network-Diagram" width="50%">
</p>
