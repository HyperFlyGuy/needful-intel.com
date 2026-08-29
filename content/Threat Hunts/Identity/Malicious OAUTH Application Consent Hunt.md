---
title: Malicious OAUTH Application Consent Hunt
tags:
  - hunt-plan
  - hunting
  - identity
  - oauth
---

## Overview
OAuth applications are used to grant third party applications access to a user's data. When the user grants this access, the application receives an access token that grants permissions. Recently adversaries have been using phishing emails with the goal of tricking users into granting a malicious OAuth app access to their data. Once approved, the adversary can use the token to make API calls on behalf of the user. 


## Preparation
OAuth applications have been around for some time now, but it wasn't until fairly recently that we began to see them abused in the wild. We have begun to see phishing campaigns that have the goal of delivering a malicious OAuth app consent prompt. Once authorized the Adversary will have an access token that they can use to access user data, even if MFA is enforced. The attack chain observed in the wild for threats of this nature is:
1. The adversary creates a malicious cross-tenant application and configures it to request "delegated permissions" to user data.
2. A redirect URL is then create that will be sent to the user in the form of a phishing email.
3. Victim clicks the link and is redirected to the OAuth consent
4. Permissions are granted when the user clicks "Allow Access"
5. The victim is redirected to an adversary controlled page and the adversary receives an access token.
To investigate these events across providers there will be a few event names that you need to be aware of. Below you will find the event name paired with the corresponding provider.
- Microsoft Entra : "Consent to application"
- Google Workspace : "Authorize API Access"
- Okta : "app.oauth2.as.consent.grant"
- AWS : "CreateToken"/"Authorize"


### Hypothesis Statement
`Adversaries are abusing OAuth applications with the intention of gaining permissions to make API calls on behalf of the user. The permissions granted are dependant on the scope of the OAuth application.`

## Execution
Below you will want to replace the event name with ones that are relevant to you. If you are operating with multiple IDP's you may want to run one hunt for each IDP.

### Gather OAUTH Consent Events
```
frame = []
for event in CloudLogs:
  if event.eventType == "<insert relevant event name>":
    frame.append(event)
```

### Extract OAUTH Application Name, Application Owner, and Prevalency
The below will yield something that looks like a dictionary where the key is a combination of application name and owner, with a count as its value. You can hopefully use this to do some frequency analysis.

```
a_frame = {}
extraction_fields = ["Name", "Owner", "Count"]
for event in frame:
  key = Name + " " + Owner
  if a_frame[key] not in a_frame:
    a_frame[key] = 1
  else:
    a_frame[key] +=1
```

## Actions
- Limit or disable the ability for a user to consent to OAuth applications.
- Monitor and audit OAuth applications that were granted consent.
- Detection for Oauth application consent followed by unusual API calls.

## Knowledge
As noted in the Wiz article (see references) manual analysis will get you a long way. When doing your analysis you'll want to look for inconsistencies in publisher, application name, and ownership. Additionally, analysis of the reply URL and Homepage URL can sometimes give you an indicator of some suspicious activity.


## References
- https://securitylabs.datadoghq.com/cloud-security-atlas/attacks/malicious-oauth-application-consent/
- https://www.wiz.io/blog/detecting-malicious-oauth-applications
- https://hoxhunt.com/blog/oauth-phishing