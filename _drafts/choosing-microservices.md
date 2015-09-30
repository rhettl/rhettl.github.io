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
 
 
### Solution 1: Grouping by type

First, I just processed some reports using Node.js scripts. Then I added some similar reports to the existing scripts, 
utilizing the existing functions to make the new reports. 

This approach worked for a few reports, but I found that the code would tangle more for each report I added, despite my 
best efforts. These were only dealing with reports of relatively similar type, source, format, and output. Adding other 
reports of different type would could potentially confound the code beyond usability.
 
Due to the amount of similarities vs the amount of differences between reports, making functions to deal with many or 
all of the reports was in many areas undoable. I ended up creating different programs to handle different types of 
reports, grouping reports as I could. This created redundancy in the codee, but made it easier to modify one report 
without damaging another.
  
