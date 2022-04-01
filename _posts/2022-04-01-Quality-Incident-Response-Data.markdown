---
layout: post
title:  "Incident Response Data - How to know your systems"
date:   2022-04-01 18:55:27 +0000
categories: forensics data EDR
published: false

---

#Introduction

During incident response and normal secops operations your objective is to *know*. 
What do you need to know?

- Is there an infection or malicious actor in your network?
- Which systems are affected?
- How the infection got inside the network?
- What can do or is the malicious entity doing?

More questions that add context and quality to your investigation

- What is the assets and value of these systems?

These questions can be solved in many different ways, but I would like to illustrate different approaches measured against a scale of data quality and scalability. 

# Forensic analysis 

You have an incident! It's unclear what happen but you have a possibly infected machine. 
You start volatility  

## Metrics for the analysis

- Is the data precise?
- Is the data scalable (Can you extract from all machines easily) 
- Is the data quick to access


# SIEM 

## Few sources no automation


## Lots of sources and high context

## Automated response to events 

(Autonomic secops) 
https://chroniclesec.medium.com/new-paper-autonomic-security-operations-10x-transformation-of-the-security-operations-center-45683fa5110b

Why is this useful, from a data point of view?

Friction of processes! There is a lot of friction in secops. Which makes mantaining the systems increasingly labour intense. 
The only way to scale properly is eliminating friction at each layer, and the decision layer needs proper contextualized data.
Moreover enrichment should be done by expert systems and the people with the most context, being this rarely the SOC analyst.



## 


# Conclusions



# References

- [Google - Autonomic Security Operations](https://chroniclesec.medium.com/new-paper-autonomic-security-operations-10x-transformation-of-the-security-operations-center-45683fa5110b)
