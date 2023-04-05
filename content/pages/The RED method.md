---
tags:
- Microservice
- Observability
title: The RED method
categories:
date: 2022-08-30
lastMod: 2022-08-30
---




> [The RED Method: key metrics for microservices architecture (weave.works)](https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/).

The RED Method refers to the three key metrics you should measure for every microservice in your architecture. Those metrics are:

  + (Request) **R**ate - the number of requests, per second, you services are serving.

  + (Request) **E**rrors - the number of failed requests per second.

  + (Request) **D**uration - distributions of the amount of time each request takes.

the RED method is *100% based on Google SRE. Google calls it their [“The Four Golden Signals”](https://landing.google.com/sre/book/chapters/monitoring-distributed-systems.html)*. Tom uses the first three metrics, which is latency (duration), errors and traffic (rate) to form his RED method.

  + {{< logseq/orgNOTE >}}The four golden signals are: latency, errors, traffic, and saturation.
{{< / logseq/orgNOTE >}}


