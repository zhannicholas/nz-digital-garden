---
tags:
- Microservice
- Observability
title: The USE method
categories:
date: 2022-08-30
lastMod: 2022-08-30
---




> [The USE Method (brendangregg.com)](https://www.brendangregg.com/usemethod.html).

id:: 630eb70d-91e4-4f73-bde6-bdcacae9d3c6
{{< logseq/orgQUOTE >}}The Utilization Saturation and Errors (USE) Method is a methodology for analyzing the performance of any system. It directs the construction of a checklist, which for server analysis can be used for quickly identifying resource bottlenecks or errors. It begins by posing questions, and then seeks answers, instead of beginning with given metrics (partial answers) and trying to work backwards.
{{< / logseq/orgQUOTE >}}

The USE Method is based on three metric types and a strategy for approaching a complex system.

  + {{< logseq/orgQUOTE >}}I find it solves about 80% of server issues with 5% of the effort.
--- Brendan Gregg
{{< / logseq/orgQUOTE >}}

## Summary

  + The USE Method can be summarized as:
> **For every resource, check utilization, saturation, and errors.**

*It's intended to be used early in a performance investigation, to identify systemic bottlenecks.*

  + Terminology definitions:

    + **resource**: all physical server functional components (CPUs, disks, busses, ...)

    + **utilization**: the average time that the resource was busy servicing work

    + **saturation**: the degree to which the resource has extra work which it can't service, often queued

    + **errors**: the count of error events

  + The metrics are usually expressed in the following terms:

    + utilization: as a percent over a time interval. eg, "one disk is running at 90% utilization".

    + saturation: as a queue length. eg, "the CPUs have an average run queue length of four".

    + errors: scalar counts. eg, "this network interface has had fifty late collisions".

## Strategy

![](https://www.brendangregg.com/usemethod/usemethod_flow.png)

  + id:: 630ecaeb-3f9e-4259-9445-9b6c8e70981b


