---
layout: post
title: Choosing Microservices - Part 2
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

In [part one][1] of this explanation on how I got to microservices, I discussed what solutions I have tried up until now.
Here is a description of how I process some of my data and how microservices could help simplify and improve my workflow 
and output.


## Examining The Data Flow

I took one flow of data, for processing classroom evaluations, from data-source to human readable PDF, and broke it into 
some steps:

1. Get data from server via download
2. Convert data from Excel(XLSX) to usable CSV
3. Clean/normalize data to be *standard*
4. Parse lines to produce usable objects
5. Save objects into database
6. Make requests to database based on the specific type of report needed
7. Do math on data and prepare for template compilation
8. Compile with HTML template for this report
9. Run CSS and compiled HTML through webkitToPDF generator to make PDF
10. Save PDF to meaningful location.


### The First Servers

I know that step 2 is needed in every report as I get data in many sources, including XLS, XLSX, XML, TSV, CSV, XML as 
XLS, TSV as XLS, XLS as XLSX, and then some. So a service which determines content type based on content buffers, 
instead of filename/extension, and converts to standard CSV is needed.

Step 3 is important in most reports to one degree or another. For example many data sets need to be converted to number 
scales instead of texts of delineated values (i.e. Very Good => 5, Good => 4, Neutral => 3, Etc). In most, blank, empty, 
N/A, or 0 answers need to be `""` (empty string). So a service which accepts CSV and returns the CSV *cleaned* using the 
necessary subset of cleaning for that report is needed.

Steps 8 and 9 are used in all places PDFs are needed. Also, I have many other places PDF generation is needed and the 
simple ability to hand CSS and HTML to a service and receive a PDF is invaluable. A service to hand data and a template 
to and get an HTML document is just as useful.


### Building Supporting Servers

After conceiving of these services, I realized that others might be useful. First one I thought of was a log server. It 
could take log reports from other servers and either log them locally or notify via email ot text based on the severity 
of the log. 

Next, I wanted to make sure services could run on any server and as many on a server as was comfortable. This means they 
all need to know where each is located and on what port to reach it. A registry/DNS-like service would be beneficial.


## Why Microservices over Modules?

There is a fuzzy, gray line separating where a module is better than a microservice. For one, modules only work if the 
dependee is of the same language. While I don't intend to use other languages, I like the idea that I can use Golang, 
Ruby, Node.js, client side JavaScript, Python, or any other language to communicate with these microservices. 

Some of these services, specifically the PDF generator, are machine specific and aren't as easily placed as a dependency 
and would be easier to place on a machine, be made perfect as possible, and never touched again.

Some services may be better off load balanced in the future. Designing each service as a stateless independent server 
allows for easier load balancing and horizontal growth, whereas making each strictly a module means I would have to add 
the server to it in the future.

It is easily possible to set up each server as a module, with full testing, and have the web server be additional, 
utilizing the existing, tested handlers to fulfill requests. This allows for these services to be used as modules if I 
decide later that there is no reason to keep it as a standalone service.


## Standing It All Up

At the moment I am successfully processing all of these reports -- it just takes a lot of manual work. So one of the 
benefits of this paradigm is that I can stand up servers as I have time to program them and they are most useful to me.

Each year as I do these reports, I can choose a new component which is common and make it a service which can stand on 
its own. This way I still make the reports on time, but slowly reduce the amount of time I spend on each reports.

The eventual hope is to use the extra time to make a website, with proper security control, which will allow the people 
who use these reports to generate them on their own on demand or on intervals. The goal is to eventually take me out of 
the mix altogether. At that time I am free to work on other projects more.



## Conclusion

I am sure there are other options, and I know I will still run into many of the same issues, but this solution seems to 
be elegant enough and seems like it will be a little easier to maintain than a single collective of code.

I have a number of ideas to help unify the services and keep them comfortably talking to each other and other systems. 
I'll post them in another post soon.




[1]: /microservices/2015/10/05/choosing-microservices