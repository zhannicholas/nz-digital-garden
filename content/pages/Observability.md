---
tags:
- Observability
title: Observability
categories:
date: 2022-08-30
lastMod: 2022-10-23
---


Observability is a superset of both monitoring and testing; it provides not only high-level overviews of the system's health but also information about **unpredictable** failure modes that couldn’t be monitored for or tested.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492033431/files/assets/dsob_0201.png)

Monitoring is best suited to report the overall health of systems and to derive alerts.

Three Pillars of Observability

  + **Metrics** are a measurement captured at regular intervals that represent an important element to the overall platform health.

  + **Logs** are granular details of processing from a given component and its format influences the utility of searching and processing of log data significantly.


    + It is often useful to think of logs in two different types, journal and diagnostics.


      + A **journal** allows the capturing of important transactions/events within the system and is used sparingly.
      + **Diagnostics** are more concerned with failures in the processing and any unexpected errors outside of a journal-based event.

  + **Traces** enable the tracking of each request through all the components interacted with in the architecture. Tracing works by adding a unique header as close to the origination of the request as possible, this header is propagated in all subsequent processing of a given request.



# Reference

  + [Distributed Systems Observability (oreilly.com)](https://learning.oreilly.com/library/view/distributed-systems-observability/9781492033431/).

  + [The USE method]({{< ref "/pages/The USE method" >}}) of Brendan Gregg

  + [The RED method]({{< ref "/pages/The RED method" >}}) of Tom Wilkie


