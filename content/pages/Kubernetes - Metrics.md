---
tags:
- Kubernetes
title: Kubernetes - Metrics
categories:
date: 2022-11-11
lastMod: 2022-11-11
---


## 查看 CPU 和内存使用情况

  + 使用 [`kubectl top`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)

    + > `kubectl top` 获取到的 metric 来自 API Server ([Resource metrics Pipeline](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/))。

    + node 级别：`kubectl top node [NAME]`

    + pod级别：`kubectl top pod [NAME]`

      + container 级别：`kubectl top pod [POD-NAME] --containers`

    + {{< logseq/orgTIP >}}`kubectl top pod` 使用的是 memory working set，所以我们可以将得到值与在 Grafana 上看到的 `container_memory_working_set_bytes` 相比较，二者会很接近。 
{{< / logseq/orgTIP >}}

  + 使用 `kubectl get --raw`:

    + [Resource metrics Pipeline](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)

  + 使用 Grafana 或其它的监控工具

    + [Grafana](https://grafana.com/docs/grafana-cloud/kubernetes-monitoring/configuration/) 默认通过 [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) 爬取 metrics.

    + 

[kube-state-metrics vs. metrics-server](https://github.com/kubernetes/kube-state-metrics#kube-state-metrics-vs-metrics-server).

  + @@html: <iframe src="https://github.com/kubernetes/kube-state-metrics#kube-state-metrics-vs-metrics-server" width="100%"></iframe>@@

查看资源分配情况：`kubectl describe`。

## 一些博客

  + [Out-of-memory (OOM) in Kubernetes – Part 3: Memory metrics sources and tools to collect them](https://mihai-albert.com/2022/02/13/out-of-memory-oom-in-kubernetes-part-3-memory-metrics-sources-and-tools-to-collect-them/).

  + [How to Prevent ‘Out of Memory’ Errors in Java-Based Kubernetes Pods](https://www.cybereason.com/blog/how-to-prevent-out-of-memory-in-java-based-kubernetes-pods).

  + 
