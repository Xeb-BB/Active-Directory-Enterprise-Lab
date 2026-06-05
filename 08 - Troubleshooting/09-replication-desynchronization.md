# Troubleshooting Case Study #9: Domain Controller Replication Desynchronization

This entry covers the diagnostic workflow, replication topology analysis, and remediation steps applied to resolve policy sync disparities across multiple domain controllers.

---

### The Real-World Scenario
Liam Miller (`it.staff03`) is an IT Support Specialist who just updated a critical firewall restriction GPO on the primary domain controller (`DC01`). Shortly after, a remote employee working on client computer `Sales-PC04` reboots their machine to receive the update. 

Instead of processing the newly modified rules, the workstation continues to execute old, unsecure environmental parameters. Liam runs a directory health check and notices that while workstations communicating directly with `DC01` get the update instantly, any workstations hitting our secondary backup controller (`DC02`) are being served outdated configurations. The two domain controllers are out of sync.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-009
* **Priority:** High
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** `it.staff03` (Liam Miller)
* **Department:** IT Department
* **Affected Asset ID:** `DC02` (Secondary Domain Controller Replication Link)

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 18:45` | `sysadmin01` | **Ticket Opened.** Group Policy changes made on `DC01` are completely missing from client endpoints authenticating against `DC02`. |
| `2026-06-05 19:05` | `sysadmin01` | **Diagnostic Run.** Executed active directory health check tool `repadmin /replsummary`. Discovered a fatal communication breakdown between `DC01` and `DC02` displaying an RPC replication block failure code. |
| `2026-06-05 19:22` | `sysadmin01` | **Root Cause Analysis.** Identified a **Replication Desynchronization** error. A secondary firewall software update on `DC02` had blocked RPC dynamic network communication port ranges (`135` and high ephemeral ports), preventing the File Replication Service / DFSR engine from syncing the `SYSVOL` folder contents. |
| `2026-06-05 19:40` | `sysadmin01` | **Remediation.** Corrected the firewall routing rules on `DC02` to allow Active Directory RPC traffic, then forced an immediate directory re-sync via `repadmin /syncall`. Ticket closed. |

---

### Technical Documentation

#### Problem
Group Policy updates, object modifications, and security configurations deployed on the primary Domain Controller (`DC01`) fail to replicate out to the secondary Domain Controller (`DC02`). As a result, workstations authenticating against the secondary controller receive outdated user environments and stale security policies.

#### Cause
The root issue was identified as a **Replication Desynchronization** error between the directory nodes (referencing `image_c3c1e2.png`). A localized firewall security change on `DC02` had accidentally blocked dynamic Remote Procedure Call (RPC) port boundaries. Because the Distributed File System Replication (DFSR) service relies on these pathways to monitor and duplicate data transitions, the automated `SYSVOL` synchronization routine failed completely.

#### Solutions
1. Logged directly into the secondary backup Domain Controller (`DC02`).
2. Opened the **Windows Defender Firewall with Advanced Security** management console.
3. Inspected the Inbound Rules tree and re-enabled the pre-configured system rules for **Active Directory Domain Services (RPC / DFSR traffic)**.
4. Opened an administrative Command Prompt terminal on `DC01` to check the replication status dashboard:
```cmd
   repadmin /replsummary
```
#### Evidence

----
### Deployment Verification & Screenshots
To properly display evidence of your fix in your GitHub project, capture and name the following snapshots:

#### 1. Active Directory Manual Syncall Tracing
What to capture: Open an administrative Command Prompt on your primary Domain Controller and run the command repadmin /syncall /A /e.

Focus: Ensure the terminal output clearly lists a successful cascade of callbacks ending with the statement string: "Syncall terminated with no errors."

File Save Path: images/troubleshoot-09-sync-success.png

#### 2. GPO Unique ID System Matching (SYSVOL Validation)
What to capture: Open Windows File Explorer side-by-side on both DC01 and DC02. Browse directly to the specific GPO file path container within the shared folders: \\liontech.local\SYSVOL\liontech.local\Policies\.

Focus: Ensure that the folder modification timestamps and exact GUID folder list strings match perfectly on both servers, proving the files are securely synchronized.

File Save Path: images/troubleshoot-09-sysvol-match.png
