---
tags:
- Observability
title: Prometheus
categories:
date: 2022-09-19
lastMod: 2022-09-19
---


[Promethues](https://prometheus.io/) is an open-source systems monitoring and alerting toolkit.

# Promethues Operator

  + [The Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator) provides [Kubernetes](https://kubernetes.io/) native deployment and management of [Prometheus](https://prometheus.io/) and related monitoring components.

  + ## Design

    + The  `ServiceMonitor`  custom resource definition (CRD) allows to declaratively define how a dynamic set of services should be monitored.

    + The  `PodMonitor`  custom resource definition (CRD) allows to declaratively define how a dynamic set of pods should be monitored.

    + [PodMonitor vs ServiceMonitor what is the difference?](https://github.com/prometheus-operator/prometheus-operator/issues/3119)

    + 
