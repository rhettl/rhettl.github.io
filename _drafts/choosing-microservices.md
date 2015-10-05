---
layout: post
title: Choosing Microservices
summary: Why I chose the microservices paradigm for my report generation
author: Rhett Lowe
thumbnail: server
tags: 
  - microservices
  - nodejs
  - rest
  - reports
  - experiment
category: microservices

---

I have a large number of reports to produce and for that last year I have been looking for a way to stand up a system to
replace me in their production.


## The Reports; The Problem

The reports I generate are needed quarterly, semiannually, annually, and sporadically. Their data is provided to me by 
computer or human via download or email at various intervals in formats ranging from XML, JSON, XLSX, XLS, CSV, TSV, and 
more. These reports need to be outputted in CSV, Text, and PDF. Finally, the reports need to be available to only those 
who need the data and protected from all others.

Suffice to say, this is a many, to many, to many, to many relationship. Much of the functionality for generating these 
reports overlap. Much of the functionality is similar, but needs to be selectively customized for the specific report 
or data at hand. Much of the data from some report is beneficial to other reports and would be nice if accessible for 
them.

For a year, I tried many different solutions.
 
 
### Solution 1: Single System

First, I just processed some reports using Node.js scripts. Then I added some similar reports to the existing scripts, 
utilizing the existing functions to make the new reports. 

This approach worked for a few reports of similar type, but I found that the code would tangle more for each report I 
added, despite my best efforts. I realized quickly I wouldn't be able to make reports of different types coexist. I 
didn't want to make a new script for each as that would create too much redundancy and be a nightmare to update.


### Solution 2: Grouping By Type
 
I started grouping by type of report, input, and output type to, as best possible, reduce redundancy and maintain 
partial flexibility. I found that even among the reports that are similar, enough differences existed to be problematic 
and updates which were painful when needed across multiple groups.


### My New Solution: Microservers

While sitting in a talk at UCCSC 2015 on a case study for AWS Lambda, it occurred to me I was thinking too large and 
and needed to think smaller -- a lot smaller. I would make each main component a service with as few input, outputs, and 
options as possible. 

I took apart multiple service workflows until each was a chain of tasks. Many tasks showed up on multiple workflows. 
These are my first targets for making into a microservice.
 
I also realized that if they were standalone services, which don't rely on other systems to be active, I could possibly 
better utilize event driven services, like AWS Lambda. 



## More in Part 2

In [part 2][1], I'll talk about my data's flow from data-source to to finished state and where microservices will be 
able to help me first.


[1]: /microservices/2015/10/05/choosing-microservices-part-2