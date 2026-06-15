# Troubleshooting Case Study #7: FSRM Blocking Legitimate Files

This entry covers the diagnostic workflow, file screening pattern analysis, and remediation steps applied to resolve an issue where legitimate business documents were blocked by the file server's security filters.

---

### The Real-World Scenario
Chloe Bennet (`hr.staff02`) is a Human Resources Assistant working on workstation `HR-PC02`. She receives an email application from a candidate containing an updated resume package named `Resume_Draft.exe.pdf`. 

When Chloe attempts to copy this document into the centralized onboarding repository located at `\\DC01\HR-Share$`, the operation fails with a filesystem error: **"An unexpected error is keeping you from copying the file... Error 0x80070005: Access is denied."** Chloe checks other standard files and can upload them without issues, but this specific file format variant is blocked, stalling the departmental recruitment pipeline.

---

### Ticketing System Log

**LionTech IT Support Portal — Service Ticket**

* **Ticket ID:** TS-2026-007
* **Priority:** Low
* **Assigned Engineer:** `sysadmin01` (Senior Systems Administrator)
* **Requesting User:** `hr.staff02` (Chloe Bennet)
* **Department:** Human Resources
* **Affected Asset ID:** `HR-PC02`

| Timestamp | Submitter | Log / Action Notes |
| :--- | :--- | :--- |
| `2026-06-05 16:45` | `sysadmin01` | **Ticket Opened.** User blocked from uploading a legitimate candidate document (`Resume_Draft.exe.pdf`) to the network file share. |
| `2026-06-05 17:02` | `sysadmin01` | **Diagnostic Run.** Inspected the Windows Event Viewer 'Applications and Services Logs -> Microsoft -> Windows -> FileServerResourceManager' channel on `DC01`. Found Event ID 8215 showing a file screen block triggered by the anti-ransomware rule group. |
| `2026-06-05 17:20` | `sysadmin01` | **Root Cause Analysis.** Identified an Aggressive Pattern Matching bug. The FSRM anti-executable block rule was configured using a loose wildcard pattern (`*.exe*`). Because of this, it flagged the middle of the string `.exe.pdf` as a banned executable file format, dropping the block action despite the file's true extension being a valid PDF. |
| `2026-06-05 17:35` | `sysadmin01` | **Remediation.** Modified the FSRM file group rule syntax to strictly target trailing executable patterns (`*.exe`). Flushed filesystem cache configurations. Ticket closed. |

---

### Technical Documentation

#### Problem
Users were hit with an access-denied error when uploading legitimate administrative business documents to network shared folders. The system dropped the connection exclusively for files containing a specific naming convention structure, despite verifying that the user possessed full NTFS write privileges.

#### Cause
The root issue was identified as **Aggressive Pattern Matching** within File Server Resource Manager (referencing `image_c3c1e2.png`). An anti-ransomware/executable file block rule was applied to the storage volumes. The matching mask was built using the permissive string pattern `*.exe*`. If a file name contained a double extension signature (such as a PDF mistakenly or intentionally named `document.exe.pdf`), the filter engine caught the center string and falsely classified it as a malicious executable, dropping the connection request.

#### Solutions
1. Logged directly into the primary Domain Controller / File Server (`DC01`).
2. Opened **File Server Resource Manager** (`fsrm.msc`).
3. Expanded the **File Screening Management** tree node and highlighted the **File Groups** item.
4. Opened the properties window for the custom executable security filter group (e.g., `Blocked Executables`).
5. Identified the problematic file match rule string: `*.exe*`.
6. Removed the aggressive string rule and replaced it with a restrictive rule targeted strictly at trailing executable strings:
7. Clicked **OK** to commit the template pattern across active file screen pathways.
8. Instructed the user to re-attempt the upload transaction.

