---
layout: post
title: Microservice Interface
summary: Defining how my microservices will be structured, before building any of them
author: Rhett Lowe
thumbnail: map-o
tags: 
  - microservices
  - nodejs
  - rest
  - specifications
  - interface
  - guidelines
category: microservices

---

In beginning to plan out my microservices, I started thinking about how they would communicate, how to help myself plan 
ahead, and what I should remember in designing each service. Here are my thoughts.

#### Think Small

This seems simple on the surface, but reminding myself of this is helping me to net get too big, or try to add too much 
functionality to each service.

To me this reminds me that the service doesn't need to be large and it doesn't need to do anything special or unique. 
Each service only needs to do the one thing it was intended to do and nothing more. Where this is a line as to what is 
*too* small, reminding myself to *think small* helps me to keep things simple as I can.


#### Do Only One Thing
##### ...Not Two

This really goes with *think small*. It too is really to keep me in the right mind set. 

As I decide that a service should fulfill a task, I should remember that a task is one thing. It isn't two things, and 
it isn't three things. If the server is to clean tabular data, like CSV or XLSX data, it should only *clean* the data. 
When starting to design the *clean* service, I realized I would be working with XLSX, CSV, TSV, and many more tabular 
data formats and started adding conversion to the list if its abilities. 

*Do Only One Thing* is a way to remind myself that the conversion from hassle-some data formats like TSV, XLS, XML, and 
XLSX isn't the job of this server; Its one thing is *clean*. I restricted *clean*'s input to CSV, renamed it 
*clean-csv*, and started writing a *to-csv* server which takes in any of the tabular formats and converts to CSV. 


#### Extract Commonalities

Basically, Don't Repeat Yourself (DRY). Obviously I will have some repetition between services, but if it looks like two 
services will have a component which is the same, that component should be extracted into another service, or possible 
just a module if it's functions aren't required by anything else or they aren't big enough to warrant a full service.

In the case of the *clean-csv* and *to-csv* services, the functions to convert to CSV were extracted and I could have 
made that a module, but I chose to make it a server because the ability to convert files to CSV can be useful in almost 
any of my applications including front and back end. This way, I don't have to care about which systems need it as a 
dependence. 

Also, with services, I don't need to worry as much about updates. If I have one or more instances running on one server 
I can make the update to the repo, push to github, then pull the source for the instances once. After the pull, I can 
use the instance manager, in this case [PM2][5], to restart all instances. PM2 will use the new source and all updates 
are done.

Were I to use modules and install it in all places that need it, I have to know which services use which modules and, 
for each service, update their `package.json`, commit and push to github, pull the changes, `npm install`, and restart 
PM2. In the end, this makes for more man-hours. 


#### Pre-Define The Service

Before writing the service, start by writing down some key information:

- Service name
- All expected inputs
- All outputs
- List all options
- Modules/Services on which it will rely. (Primarily I only care to reference my modules and services) 

This process helps to construct a clear idea of where the service fits into the web of other services, and helps to 
identify if the service should be broken into multiple, smaller services, and if there is a way to reduce the number of 
allowable options.


#### Limit Possible Options

The more options that exist, the more complicated the program, the more to debug or break. If there is no way to reduce 
the number of options, maybe consider splitting into multiple services.


#### Always Output Something

This might be considered redundant, but it is my choice that all services should either error or produce something on 
success. In cases which don't have an output, like a service which saves files to a filesystem or writes logs should 
produce a success message or object and not just a `200` ([HTTP Status Codes][6]) with an empty body. 

To be honest I am willing to drop this requirement if I can find reason that it is better to not send anything.


#### ReST using HTTP and JSON

Each microservice should expect that it will be contacted using [HTTP][4] requests and should expect to output 
[JSON][3], unless expressly offered and stated otherwise, and all communication should follow [ReST][1]-ful best 
practices or [HAPI][2] best practices where ReST is unclear or otherwise lacking.

Some services, like the *to-csv* service, which converts any tabular data file to CSV text, will output by default to 
CSV. Services like this should correctly identify, using HTTP headers, that the content type is `text/csv` and should 
have an optional output of JSON when the requester explicitly states they want JSON, either through an HTTP URI or in an 
`accept` header.








[1]: https://en.wikipedia.org/wiki/Representational_state_transfer
[2]: https://en.wikipedia.org/wiki/HATEOAS
[3]: https://en.wikipedia.org/wiki/JSON
[4]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol
[5]: https://www.npmjs.com/package/pm2
[6]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html