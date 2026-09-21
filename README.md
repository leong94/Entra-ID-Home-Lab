# Entra-ID-Home-Lab

A hands-on lab covering Microsoft Entra ID (formerly Azure AD) — the cloud identity platform most businesses now use. Built in a Microsoft 365 Business Premium trial tenant, this project covers user and group management, licensing, MFA, Conditional Access, and least-privilege admin roles.

## Environment

| Component | Details |
|-----------|---------|
| Platform | Microsoft Entra ID |
| Tenant Type | Microsoft 365 Business Premium (trial) |
| Domain | LabCorpIT.onmicrosoft.com |
| License | Entra ID P1 (included with Business Premium) |

## What Was Built

### Tenant Setup
- Attempted the free Microsoft 365 Developer Program sandbox, but was rejected with a "does not qualify" message (a known, common issue with tightened eligibility rules)
- Used the Business Premium trial as a working fallback — creates a fully functional tenant with the same licensing tier (Entra ID P1) needed for Conditional Access

### Users
Created test users in the tenant:
- jsmith (Jordan Smith)
- agarcia (Ana Garcia)
- tlee (Tom Lee)

### Groups
Created a security group, `IT-Support`, with **Assigned** membership. Added `jsmith` and `tlee` as members, intentionally leaving `agarcia` out to later test policy scoping.

### Licensing
Assigned Business Premium licenses to `jsmith` and `tlee`, since Conditional Access only applies to licensed users.

### MFA — Security Defaults
Enabled Security Defaults as a baseline, which forces MFA registration tenant-wide. Verified by signing in as `jsmith`, who was prompted to set up Microsoft Authenticator.

### Conditional Access Policy
Built a custom policy, `Require MFA for IT-Support Group`:
- Scoped to the `IT-Support` group only (not all users)
- Applied to all cloud apps
- Required MFA on grant

Tested in **Report-only** mode first to confirm correct targeting before enforcing it. Had to disable Security Defaults first, since Entra doesn't allow both to run active at the same time.

**Verification:**
- `jsmith` (in the group) was required to complete MFA on sign-in
- `agarcia` (not in the group) signed in without an MFA prompt

One extra troubleshooting step: disabling Security Defaults auto-created several Microsoft preset Conditional Access policies, including one requiring MFA for all users. That policy had to be set to Report-only so the test against `agarcia` would accurately reflect only the custom policy's scope.

### Least-Privilege Admin Role
Assigned the **Helpdesk Administrator** role to `tlee` instead of Global Administrator — a role scoped to tasks like password resets and license management, without tenant-wide control.

## Troubleshooting Notes

- **Developer Program rejection:** Common right now due to tightened eligibility. Business Premium trial is a reliable fallback that supports every step in this lab.
- **Security Defaults vs. Conditional Access conflict:** Entra requires Security Defaults to be disabled before a Conditional Access policy can be enabled or evaluated, even in Report-only mode.
- **Sign-in logs:** Conditional Access results only appear under completed *interactive* sign-ins, not non-interactive/background sign-in events.
- **Report-only results location:** Report-only policy results appear under a separate "Report-only" tab in the sign-in log details, not the main "Conditional Access" tab (which shows enforced policies only).
- **Microsoft preset policies:** Disabling Security Defaults automatically creates baseline Conditional Access policies. These need to be reviewed and adjusted when testing custom policies to avoid overlapping results.

## Skills Demonstrated

- Microsoft Entra ID tenant provisioning
- User and group management in a cloud identity platform
- License assignment and its relationship to feature availability
- MFA configuration (Security Defaults)
- Conditional Access policy design, Report-only testing, and enforcement
- Least-privilege administrative role assignment
- Real-world troubleshooting of identity and access configuration conflicts
