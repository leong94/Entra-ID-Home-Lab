# Entra-ID-Home-Lab

A hands-on lab covering Microsoft Entra ID (formerly Azure AD) — the cloud identity platform most businesses now use. Built in a Microsoft 365 Business Premium trial tenant, this project covers user and group management, licensing, MFA, Conditional Access, least-privilege admin roles, and full offboarding/deprovisioning.

## Environment

| Component | Details |
| --- | --- |
| Platform | Microsoft Entra ID |
| Tenant type | Microsoft 365 Business Premium (trial) |
| Domain | LabCorpIT.onmicrosoft.com |
| License | Entra ID P1 (included with Business Premium) |

Tenant provisioned through a Microsoft 365 Business Premium trial after the free Developer Program sandbox rejected the signup. Same P1 licensing either way, so nothing in the lab depended on which path worked.


  
## Identity Setup

### Users
Created three accounts to test policies against.
- jsmith (Jordan Smith)
- agarcia (Ana Garcia)
- tlee (Tom Lee)

<img width="477" height="146" alt="image" src="https://github.com/user-attachments/assets/c2a7da04-2885-467e-a8a2-e06408ea9718" />


### Groups
Created a security group, `IT-Support`, with **Assigned** membership. Added `jsmith` and `tlee` as members, and left agarcia out on purpose to test policy scoping against.

<img width="707" height="271" alt="image" src="https://github.com/user-attachments/assets/1454b5cd-13ce-4281-9b95-bcab42814bd4" />

### Licensing
Assigned Business Premium licenses to `jsmith` and `tlee`, since Conditional Access only applies to licensed users.

<img width="522" height="59" alt="image" src="https://github.com/user-attachments/assets/3e7a459d-af74-432d-9efb-689108ac45bd" />


## Security Configuration

### MFA — Security Defaults
Enabled Security Defaults as a baseline, which forces MFA registration tenant-wide. Verified by signing in as `jsmith`, who was prompted to set up Microsoft Authenticator.

<img width="692" height="87" alt="image" src="https://github.com/user-attachments/assets/627e9f7c-7f63-463f-8ddf-aab72037cde8" />

<img width="263" height="206" alt="image" src="https://github.com/user-attachments/assets/34395920-95bf-43b1-95d9-720e3245713c" />



### Conditional Access Policy
Built a custom policy, `Require MFA for IT-Support Group`:
- Scoped to the `IT-Support` group only (not all users)
- Applied to all cloud apps
- Required MFA on grant

Tested in **Report-only** mode first to confirm correct targeting before enforcing it. Had to disable Security Defaults first, since Entra doesn't allow both to run active at the same time.

<img width="342" height="444" alt="image" src="https://github.com/user-attachments/assets/a6713a45-081e-4084-ace1-678fa617693b" />

**Verification:**
- `jsmith` (in the group) was required to complete MFA on sign-in
- `agarcia` (not in the group) signed in without an MFA prompt

One extra troubleshooting step: disabling Security Defaults auto-created several Microsoft preset Conditional Access policies, including one requiring MFA for all users. That policy had to be set to Report-only so the test against `agarcia` would accurately reflect only the custom policy's scope.

### Least-Privilege Admin Role
Assigned the **Helpdesk Administrator** role to `tlee` instead of Global Administrator — a role scoped to tasks like password resets and license management, without tenant-wide control.

<img width="491" height="129" alt="image" src="https://github.com/user-attachments/assets/1380b121-ba1b-45be-9d1f-425b80bcaba9" />


## Lifecycle Management

### Offboarding & Deprovisioning

Simulated a full offboarding process for `jsmith` to ensure access is genuinely revoked, not just partially removed.

**Blocked sign-in** — the first and most important step, since it stops all future login attempts. Learned that blocking also automatically signs a user out of all active sessions within 60 minutes, so a separate manual session revocation isn't strictly necessary unless immediate cutoff is required (e.g., a security incident).



**Removed group membership** — removed `jsmith` from `IT-Support` so he's no longer in scope for the Conditional Access policy, even if the account were ever unblocked by mistake later.



**Removed the license** — unassigned Business Premium to free the seat and cut off Exchange/Teams/SharePoint access tied to it.



**Why this order matters:** blocking sign-in should happen alongside or before other cleanup steps, but revoking active sessions specifically needs to happen *before* blocking, if done manually — some tenants remove the "Sign out of all sessions" option once an account is already blocked, since blocking already handles session cleanup within the hour.

**Why remove access beyond just blocking:** a blocked account is not a security risk on its own, but leaving licenses and group memberships in place means an accidental unblock (a wrong click on a bulk action, a scripting error) would instantly restore full access. Removing everything else limits that risk — an accidentally-unblocked account with no license and no groups is effectively an empty shell rather than a live security exposure
## Troubleshooting Notes

- **Developer Program rejection:** Common right now due to tightened eligibility. Business Premium trial is a reliable fallback that supports every step in this lab.
- **Security Defaults vs. Conditional Access conflict:** Entra requires Security Defaults to be disabled before a Conditional Access policy can be enabled or evaluated, even in Report-only mode.
- **Sign-in logs:** Conditional Access results only appear under completed *interactive* sign-ins, not non-interactive/background sign-in events.
- **Microsoft preset policies:** Disabling Security Defaults automatically creates baseline Conditional Access policies. These need to be reviewed and adjusted when testing custom policies to avoid overlapping results.
- **"Sign out of all sessions" missing:** This option disappears once an account is already blocked, since blocking already triggers automatic session cleanup within 60 minutes. Revoke sessions manually before blocking if immediate session termination is required.

## Skills Demonstrated

- Microsoft Entra ID tenant provisioning
- User and group management in a cloud identity platform
- License assignment and its relationship to feature availability
- MFA configuration (Security Defaults)
- Conditional Access policy design, Report-only testing, and enforcement
- Least-privilege administrative role assignment
- Real-world troubleshooting of identity and access configuration conflicts
- Offboarding and deprovisioning workflow, including sign-in blocking, session management, and group/license removal
