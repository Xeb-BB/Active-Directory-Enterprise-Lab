# Operational Execution Breakdown
## Phase 1: Identity Provisioning
When a user profile is created via our onboarding automation script (or manual administrative input), it enforces our enterprise identity baseline:

Naming Convention: firstinitial+lastname or department.role (e.g., hr.staff03).

Initial Password Complexity: A strong, temporary 12-character alphanumeric password is auto-generated (LionTech2026!).

Account Security Options: "User must change password at next logon" is explicitly checked.

## Phase 2: Structural Routing & Inheritance
OU Placement: The user account is placed directly in its respective department Organizational Unit (e.g., Human Resources).

Access Assignment: The account is nested inside the matching Global Security Group (e.g., GG_HR).

Policy Absorption: Upon the next workstation login, Group Policies evaluate the group token and instantly map target storage shares (H:\ Drive), configure local profile behaviors, and enforce restricted environment parameters.

## Deployment Verification & Screenshots
To verify that the onboarding pipeline successfully fires from start to finish, document the execution using the following system screenshots:

1. Automation Workflow Execution
Show a screenshot of your PowerShell ISE or console running the script above, proving the successful execution outputs and the generation of the account.

2. Identity Account Properties (ADUC)
Show the newly created user object properties screen in Active Directory Users and Computers. Make sure the screenshot captures the Account options pane proving that the "User must change password at next logon" flag is active.

3. Group Membership Allocation
Show the Member Of tab for your freshly provisioned user, verifying that they instantly inherited their departmental Global Group (GG_*) identity tag without manual sysadmin interference.

4. Client Endpoint Initial Logon Experience
Show a screenshot of a client machine (e.g., HR-PC02) during the user's first login attempt. The screen should explicitly display the Windows security notice: "The user's password must be changed before signing in."

5. Automated Environment Mapping Verification
Show the Windows File Explorer interface on the user's workstation after they complete their password reset. This proves that their specific network drives (like the hidden department share and the common share) mapped perfectly based on the security groups we designed.
