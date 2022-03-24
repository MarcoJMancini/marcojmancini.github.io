---
layout: post
title:  "Some thoughs on the Okta compromise"
date:   2020-01-11 21:00:27 +0000
categories: SOC open-source TheHive Cortex WalkOFF MISP
published: false
---


- Introduction
- How Okta works
- LAPSUS$ Tactics
- Third Party Breach Review
  - Scope
  - Signals
  - Improvements
- References


#Introduction

An Okta support engineer was compromised which allowed a threat actor to weaken and access services and assets that required having an identity that was protected by Okta.

This is a simple fact, on the 22nd of March the Threat Actor Lapsus$ released this information and Okta after denying there was a problem admitted that 2.5% of their clients could have been compromised and Okta will contact these clients.

This blog post mains to aggregate the fantqastic analysis the security community has done and give guidance on how to review the system logs that Okta offers.
Moreover I will sure some thoughts on SSO and the recommended mitigations for attacks against the identity providers.

# How Okta works

The offering of Okta is very simple:
> A service that centralizes and simplifies identity managament.

This means, a third party (Okta) has all the identities and scopes for another service. The user is able to use advanced security features like SSO and MFA using Okta as a security bridge to another service.

[img]  https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fdeveloper.okta.com%2Fassets-jekyll%2Fblog%2Foauth%2Foidc-flow-3f640eb4b5d82151643a1d7bc2d55cfab1c1c6e14b6c2ad501660216c6d227c8.png&f=1&nofb=1
[img]https://www.okta.com/sites/default/files/images/white-paper/inline/Okta_Service_MFA_Screenshot1.png

For this incident, what this means. A complete compromise of Okta may mean a complete access to the services that Okta is using to authenticated. For the traditional security scenario it would be similar to a compromise of Active Directory. The Identity and permissions systems will always be the most sought after target for an attacker. As these systems are able to effectively give an attacker access to any electronic key in the company.

This particular attack was on a support enginner. According to Okta this engineer has the following priviledes.

- Reset passwords
- Reset MFA
- Impersonate users (TO BE CORROBORATED)

This doesn't mean a complete control of the infrastructure. But gives attackers the possibility of debilitate the identity protections offered by Okta.

https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth
https://developer.okta.com/docs/concepts/how-okta-works/
https://www.okta.com/resources/whitepaper/mfa-moving-beyond-user-names-passwords/
https://www.okta.com/resources/whitepaper-how-okta-integrates-applications/

# LAPSUS$ Tactics

Microsoft published an excellent report on LAPSUS$ (Called alternative DEV-0537). The following statement is a good summary:

''''
The actors behind DEV-0537 focused their social engineering efforts to gather knowledge about their target’s business operations. Such information includes intimate knowledge about employees, team structures, help desks, crisis response workflows, and supply chain relationships. Examples of these social engineering tactics include spamming a target user with multifactor authentication (MFA) prompts and calling the organization’s help desk to reset a target’s credentials.
''''

Let me list all the tactics from this threat actor:

-
-
-
-

This threat actor is very well versed in social engineer and how technology companies operate, which means the threat actor has a lot of overall with insider threat tactics and using third party trust to compromise other targets.

All this begs 2 questions.

- What can engineers do to compromise your company infrastructure and the clients you serve?
- What is the shortest path for an attacker to compromise the identity of a high privileged user.

A partial or incomplete implementation of zero-trust can lead to an scenario like the following:
- Engineer uses single sign on with MFA disabled.
- A password spray attack is able to compromise their SSO
- SSO account can log in into Github from any IP or device.  
- From private repositories on Github keys to a cloud provider is found
- Keys destroy the company's cloud infrastructure including backups.

The user may have the option to enable MFA but that was not enforced. Or what the Okta attack allows, is for the MFA to be disabled/weaken. But each step of this chain is an opportunity to detect and stop the attack. In the same way internal network authentication with LDAP required a graph based thinking for defenders. Single Sign on provides a centralised authentication identity and excellent controls but require security teams to still think in graphs of actions possible by an attacker.



https://www.microsoft.com/security/blog/2022/03/22/dev-0537-criminal-actor-targeting-organizations-for-data-exfiltration-and-destruction/


# Third Party Breach Review

Okta's response to the whle incident has been pretty poor. Their clients has been mostly kept in the dark and given little guidance has to how to analyse and verify if their have been compromised.

Ultimately there are some recommendations here that woudl require Okta to verify the assumptions. But let me do a humble suggestion about what steps and analysis could be applied to verify if your organization may be affected.

## Scope and possible impact

The first step seems simple but vital. The objective is to identify all Okta organizations and assets that may be affected.
- Identify all Okta organizations in your company.
- Identify services managed by Okta
- Identify all admins of these organizations.

If you have all logs being collected and analysed on a SIEM. Excellent you can skip to the signals part. :)
If you don't have everything already collected, as soon as you can collect all the audit logs using the admin view.
These can be found on system > system audit logs
The format will be csv but it's always good practice to store this information. Once the logs are located we can go to the next step.

## Signals

Audit logs have 2 interesting columns for this event.

> event_type

These are the actions captured by the system. The following events should be filtered and reviewed carefully.

[ADD EVENTS LIKE impersonate password reset MFA]

> user

This is a conjecture, but we would expect any action from Okta support to be performed with a @okta account. (Beware the system@okta.com is the identity the backend takes for a lot of non-support related actions)

Ideally you can use the following datasets to surfaces more suspcious actions.
[Share sigma dataset]
[Share Elastic dataset]


## Improvements

- Talk about disabling Support access
- Ingest audit events
- Review access
- Review MFA, FIDO policies  (Protect against downgrade attacks)
- Have notifications for sensitive actions (Like disable MFA)






## References

https://www.microsoft.com/security/blog/2022/03/22/dev-0537-criminal-actor-targeting-organizations-for-data-exfiltration-and-destruction/
https://www.elastic.co/blog/okta-and-lapsus-what-you-need-to-know
https://www.okta.com/blog/2022/03/oktas-investigation-of-the-january-2022-compromise/
https://blog.cloudflare.com/cloudflare-investigation-of-the-january-2022-okta-compromise/

https://krebsonsecurity.com/2022/03/a-closer-look-at-the-lapsus-data-extortion-group/
https://twitter.com/jonoberheide/status/1506280347306188805?s=21
https://twitter.com/alexstamos/status/1506356575614644227?t=K3rG1CK48q_cCd8RhUwqpw&s=08
https://twitter.com/AlvieriD/status/1506342893727805440?t=P_YbpROeOB8nYZ0fj8We2A&s=19
https://twitter.com/InvictusIR/status/1506193035784294404?t=Iox81ZB_8kwkxH30mWccWA&s=08
https://twitter.com/Understudy77/status/1506275089213538323?t=WUKkdK7uT-J-skIdGuK3hg&s=08
https://twitter.com/blueteamblog/status/1506176104985411587?t=h0Q67QExsPEhNUEmI6iMMQ&s=08
https://news.ycombinator.com/item?id=30762520
