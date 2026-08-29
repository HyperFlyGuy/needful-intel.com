---
title: "Business Email Compromise: Rule Creation Hunt"
tags:
  - hunting
  - identity
  - email
  - hunt-plan
---

## Overview
Business Email Compromise (BEC) is the successor to your traditional mass phishing email campaigns (though mass campaigns certainly still exist). BEC phishing takes a more targeted approach to victims, often times the adversary will attempt to establish some form of rapport with the victim before proceeding. The actor will then use a form of social engineering by either impersonating someone from within the victim organization or establishing a sense of urgency within their message. In most case we will see a BEC evolve in the following manner:

1. Adversary launches a AiTM Campaign or phishing campaign to gain access to mailboxes
2. Once the email account is compromised they will sometimes try to establish persistence by adding an Adversary controlled MFA device
3. The adversary will perform discovery to uncover sensitive information
4. Data exfiltration is possible at this phase if the adversary discovered something of interest. It could come in the form of syncing the mailbox with an email client, sending the information from the compromised account, or direct file download from Sharepoint.
5. At this step the adversary may create inbox rules in an attempt to cover their tracks (its worth noting that this could happen earlier in the chain). 
6. This could be considered the action on objectives or final phase of the attack chain. At this point we typically observe the adversary doing 1 of 3 things. They will either target the owner of the compromised inbox to fulfill a fraudulent invoice. They can target a customer of the victim in a similar manner. Lastly, they may choose to do additional credential harvesting by sending phishing emails from the compromised inbox.


## Preparation
Given the above attack chain there are a few avenues that we could take in terms of hunting. However, to keep this hunt properly scoped we are going to focus on the email rule creation piece of the attack chain. Through open source reporting we discovered two characteristics that we see when observing adversary behavior.
- Email rules typically do not exceed 3 characters and more often than not it is 1-2 characters
- Email rules typically do not contain alpha-numeric characters
- Email rules are typically moving emails to lesser used folders like "RSS", "Deleted Items", or "Junk". This is more something I would do during your analysis and would not filter out other rule conditions right of the bat.
The above characteristics are observed mostly when the compromise is automated through some form of a phishing kit. More targeted campaigns will need to be uncovered through correlation of multiple alert sources paired with other hunting/investigative leads.

The two most popular email providers are Google and Microsoft at the time of this writing, the event names that indicate a new email rule was created are "New-InboxRule" (M365) and "Create filter" (Google). If your organization supports both I would recommend splitting this hunt into two separate hunts just to limit your scope and make sure you are more effective in your analysis.


### Hypothesis Statement

`Adversaries will target victims with specialized phishing emails to take over their account and create email rules to facilitate fraudulent wire transfers, credential theft, and data exfiltration.`

## Execution

### Logic for gathering email rules
```
frame = []
for event in EmailEvents:
  if event.operation == "<your rule creation event name>":
    frame.append(event)
```

### Logic for filtering rules
```
analysis_q = []
for rule in frame:
  if (len(rule)<=3 and does not contain [a-zA-Z0-9]):
    analysis_q.append(rule)
```

Here we would begin our analysis. I would start by looking for the following:
- Email rule conditions that send emails to external mailboxes
- Email rule conditions that move incoming emails to uncommon folders as mentioned above.

## Actions
- Create a detection or hunting lead for Email rule creation where one or many of the following conditions are met 
  - Email rule is less than or equal to 3 characters
  - Email rule is consisting only of special character
- Any accounts that were discovered to have been compromised should have sessions revoked, passwords reset, and MFA devices audited
- Create a hunt that can search for AiTM activity
- Create a hunt that looks for Session Hijacking/Account Takeover

## Knowledge
One false positive I will commonly come across for this hunt is the email rule length. Around the 3 character mark is when I begin to see false positives introduced, you will have to get a feel for this within your environment to determine if the observed activity is expected or not.

There is still a known gap that is not addressed by this hunt. If the email rule creation is more tailored to the victim (not special characters and exceeds 3 characters), we will not observe it with our current logic.

It is worth noting, there is also some temporal correlation that you can do here to back up any potential findings. If a user has alerted or reported clicking a phishing link shortly before the email rule creation, it could indicate that they are currently the victim of a BEC.



## References 
- https://www.microsoft.com/en-us/security/business/security-101/what-is-business-email-compromise-bec
- https://www.proofpoint.com/us/threat-reference/business-email-compromise
- https://www.paloaltonetworks.com/cyberpedia/what-is-business-email-compromise-bec-tactics-and-prevention
- https://www.truesec.com/hub/blog/understanding-the-threat-what-is-business-email-compromise
- https://www.crowdstrike.com/en-us/cybersecurity-101/threat-intelligence/business-email-compromise-bec/