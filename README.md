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




### Groups
Created a security group, `IT-Support`, with **Assigned** membership. Added `jsmith` and `tlee` as members, and left agarcia out on purpose to test policy scoping against.



### Licensing
Assigned Business Premium licenses to `jsmith` and `tlee`, since Conditional Access only applies to licensed users.




## Security Configuration

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

### Least-privilege admin role

Gave `tlee` the Helpdesk Administrator role instead of Global Administrator. It covers the usual help desk work — password resets, license management — without handing over the whole tenant. This is the role I'd actually be assigned in a real help desk job, so it was the obvious one to practice with.





## Lifecycle Management

### Offboarding & Deprovisioning

Simulated a full offboarding process for `jsmith` to ensure access is genuinely revoked, not just partially removed.

**1. Blocked sign-in first.** This is the step that matters most, since it stops all future logins. Blocking also revokes refresh tokens, so active sessions die out as their access tokens expire (within the hour). Unless it's a security incident where someone needs to be out *right now*, that's good enough without a separate manual session revocation.

**2. Removed group memberships.** Pulled `jsmith` out of `IT-Support`. A blocked account isn't dangerous on its own, but if someone ever unblocks it by mistake — a wrong click on a bulk action, a bad script — leftover group memberships mean instant full access again. Removing them turns an accidental unblock into an empty shell instead of a live security problem.

**3. Removed the license.** Unassigned Business Premium to free the seat and cut the Exchange/Teams/SharePoint access tied to it.

   
## Troubleshooting Notes

- **Developer Program rejection:** Common right now due to tightened eligibility. Business Premium trial is a reliable fallback that supports every step in this lab.
- **Security Defaults vs. Conditional Access conflict:** Entra requires Security Defaults to be disabled before a Conditional Access policy can be enabled or evaluated, even in Report-only mode.
- **Sign-in logs:** Conditional Access results only appear under completed *interactive* sign-ins, not non-interactive/background sign-in events.
- **Microsoft preset policies:** Disabling Security Defaults automatically creates baseline Conditional Access policies. These need to be reviewed and adjusted when testing custom policies to avoid overlapping results.

## Skills Demonstrated

- Microsoft Entra ID tenant provisioning
- User and group management
- License assignment
- MFA configuration (Security Defaults)
- Conditional Access policy design, Report-only testing, and enforcement
- Least-privilege administrative role assignment
- Identity troubleshooting (policy conflicts, sign-in log analysis)
- Offboarding and deprovisioning workflow
