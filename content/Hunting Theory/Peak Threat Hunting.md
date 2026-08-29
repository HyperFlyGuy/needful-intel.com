---
title: PEAK Threat Hunting
tags:
  - methodology
  - hunting
---
# Why Are We Hunting?

Over the last decade or so we have experienced technological revolution over and over again. While these revolutions provided an unrivaled boost in productivity they often introduced a new level of complexity when it comes to security. This caused security teams to introduce a proactive component to their security efforts, which was commonly termed as "Threat Hunting". Newer and more immature Threat Hunting programs will often follow the mantra of "Finding Evil" or hunt solely on atomic indicators. This approach is better than nothing, but, I think we can all agree that as our program matures we need to operationalize our hunting and bring it under a framework. A framework is necessary because it provides a blueprint that your team can use to effectively hunt your environment.

# Why Choose PEAK?

**This is not a breakdown of the entire PEAK hunt framework, just a summary on why I like it. For a full breakdown see Splunk's documentation on it [Splunk PEAK Hunting](https://www.splunk.com/en_us/blog/security/peak-threat-hunting-framework.html)**

When you look for different types of Threat Hunting Frameworks there are three main ones that you will typically come across.

- **SQRLL** - One of the original companies that focused on threat hunting until they were bought by amazon in 2018
- **TAHITI** - With a history of working at a German bank I became fairly familiar with this one. The main downfall of it in my opinion is that it requires multiple teams to contribute for it to work smoothly, which unfortunately in large corporate environments is sometimes difficult.
- **PEAK** - This hunt methodology resonates the most with me simply because I do believe it to be one of the more versatile frameworks and it resonates with my investigative style.

I want to be clear that none of these frameworks are necessarily better than the others. They can each be implemented and used to develop a successful hunting program. However, I will focus on PEAK because it is the one that I favor most when doing any form of threat hunting.

# What Types of Hunts and What Should You Choose?

While you can certainly add different types of hunts that will fit your organization. The standard PEAK hunting framework details three separate methodologies.

- **Hypothesis Based Hunting** - A classic approach where hunters come up with ideas about potential threats and activities, then determine how they would be presented in their telemetry. They then use data and analysis to prove or disprove their theories.
- **Baseline Threat Hunting** - In this hunt a baseline of normal behavior is established then hunter search for deviations that could signal malicious activity.
- **Model Assisted Threat Hunting** - This is a more complex hunt type and can be described as a data science process wrapped in a threat hunting process, and focuses on applying algorithms to help find leads for threat hunting. For example, hunters can use machine learning (ML) techniques to train models to recognize malicious or identify suspicious behavior. Think of this as a hybrid of the hypothesis-driven and baseline hunt types but with substantial automation from the ML.

We have three defined hunt types, but Hunters typically struggle to decide which hunt type to go with. Even then when they do choose one they end up veering from the established methodology which causes the hunt to deteriorate. A short cheat sheet for how to choose which hunt to go with.

** We use the term implicit complexity below. This just means that the data has a lot of variables and complicated relationships where developing an algorithm may be challenging or impractical. **

If your hunt is topic based and does not have an implicit complexity you want to lean towards a Hypothesis driven threat hunt. If your hunt is topic based and does have implicit complexity OR your hunt is data based and the data source has already been defined. These would be candidates for a model assisted threat hunt. Lastly, if you hunt is data based and the data source is new (or needs re-baselined) it would qualify for a Baseline Threat Hunt.

# Threat Hunting Deliverables:

Every managers favorite topic is what do I get out of all these man hours that I have to dedicate to Threat Hunting. The most common outputs of a hunt that you will encounter are:

- Security Incidents
- Gaps and Risks
- New or improved detections
- Additional Hunt ideas
- Hunt Documentation

A singular hunt can produce many if not all these outcomes. I must say that the wrap up of a hunt is typically my least favorite part it is a lot of administrative tasks and documentation, but I cannot emphasize enough. THIS IS THE MOST IMPORTANT PIECE. Be meticulous with your results and documentation. I promise you'll have great outcomes that have a lasting effect on your organization.