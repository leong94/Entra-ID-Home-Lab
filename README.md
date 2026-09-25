# Entra-ID-Home-Lab

A hands-on lab covering Microsoft Entra ID (formerly Azure AD) — the cloud identity platform most businesses now use. Built in a Microsoft 365 Business Premium trial tenant, this project covers user and group management, licensing, MFA, Conditional Access, least-privilege admin roles, and full offboarding/deprovisioning.

## Environment

| Component | Details |
| --- | --- |
| Platform | Microsoft Entra ID |
| Tenant type | Microsoft 365 Business Premium (trial) |
| Domain | LabCorpIT.onmicrosoft.com |
| License | Entra ID P1 (included with Business Premium) |

Tenant provisioned through a Microsoft 365 Business Premium trial after getting the message of not qualifying for the free Developer Program sandbox signup.


  
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
Confirmed Security Defaults was already enabled by default, which forces MFA registration.  Signed in as jsmith to confirm and got the Microsoft Authenticator setup prompt. Worked as expected.





### Conditional Access Policy
Built a custom policy, `Require MFA for IT-Support Group`:
- Scoped to the `IT-Support` group only (not all users)
- Applied to all cloud apps
- Grants access only with MFA

Ran it in Report-only mode first to confirm the targeting before enforcing anything. Had to disable Security Defaults first, since Entra doesn't allow both to run active at the same time.



**Verification:**
- `jsmith` (in the group) was required to complete MFA on sign-in
- `agarcia` (not in the group) signed in without an MFA prompt


### Least-privilege admin role

Gave `tlee` the Helpdesk Administrator role instead of Global Administrator. It covers the usual help desk work — password resets, license management — without handing over the whole tenant. This is the role I'd actually be assigned in a real help desk job, so it was the obvious one to practice with.





## Lifecycle Management

### Offboarding & Deprovisioning

Simulated a full offboarding process for `jsmith` to ensure access is fully revoked, not just partially removed.

**1. Blocked sign-in first.** This is the step that matters most, since it stops all future logins. Blocking also revokes refresh tokens, so active sessions die out as their access tokens expire (within the hour).

**2. Removed group memberships.** Pulled `jsmith` out of `IT-Support`. Removing them prevents a security problem if they were to get unblocked by mistake.

**3. Removed the license.** Unassigned Business Premium to free the seat and cut the Exchange/Teams/SharePoint access tied to it.

   
## Troubleshooting Notes

- **Developer Program rejection:** Common right now due to tightened eligibility. Business Premium trial is a reliable fallback that supports every step in this lab.
- **Security Defaults vs. Conditional Access conflict:** Entra requires Security Defaults to be disabled before a Conditional Access policy can be enabled or evaluated, even in Report-only mode.
- **Sign-in logs:** Conditional Access results only appear under completed *interactive* sign-ins, not non-interactive/background sign-in events.
- **Microsoft preset policies:** Disabling Security Defaults automatically creates baseline Conditional Access policies. These need to be reviewed and adjusted when testing custom policies to avoid overlapping results.

## Skills Demonstrated

- Microsoft Entra ID tenant provisioning
- User and group management
- Licenses assignments to users
- Multi-factor authentication (MFA) configurations enabled and tested
- Conditional Access policy created and verified
- Least-privilege administrative role assignment
- Offboarding and deprovisioning workflow
