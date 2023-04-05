---
tags:
- Observability
- SRE
title: Alerting
categories:
date: 2022-09-01
lastMod: 2022-09-01
---


# Best Practice for Alerting

  + Monitoring data should at all times provide a bird’s-eye view of the overall health of a distributed system by recording and exposing high-level metrics over time across all components of the system.

  + Monitoring data should at all times provide a bird’s-eye view of the overall health of a distributed system by recording and exposing high-level metrics over time across all components of the system.

  + In the event of a failure, monitoring data should immediately be able to provide visibility into the impact of the failure as well as the effect of any fix deployed.

  + For the on-call experience to be humane and sustainable, all alerts (and monitoring signals used to derive them) need to *actionable*.

# What Monitoring Signals to Use for Alerting?

  + A good set of metrics used for monitoring purposes are the USE metrics ([The USE method]({{< ref "/pages/The USE method" >}})) and the RED metrics ([The RED method]({{< ref "/pages/The RED method" >}})). In the book Site Reliability Engineering (O’Reilly), Rob Ewaschuk proposed the four golden signals (latency, errors, traffic, and saturation) as the minimum viable signals to monitor for alerting purposes.

  + 

# References

  + [Distributed Systems Observability (oreilly.com)](https://learning.oreilly.com/library/view/distributed-systems-observability/9781492033431/).
