---
tags:
- Kubernetes
title: Kubernetes
categories:
date: 2022-10-14
lastMod: 2022-11-10
---


[Kubernetes](https://kubernetes.io/) is a container orchestration platform.

## Concepts

  + ### Architecture

    + [[draws/2022-10-20-22-18-55.excalidraw]]

  + Fundamentally, a Kubernetes cluster consists of two things. First, there’s a set of machines that the workloads will run on called the **nodes**. Secondly, there’s a set of controlling software that manages these nodes and is referred to as the **control plane**. These nodes could be running physical machines or virtual machines under the hood. Rather than scheduling a container, Kubernetes instead schedules something it calls a **pod**. A pod consists of one or more containers that will be deployed together.

  + In the context of Kubernetes, you can think of a service as a stable routing endpoint.

  + With a **replica set**, you define the desired state of a set of pods. A replicaset ensures that a specified number of **pod** replicas are running at any given time.

  + A **deployment** is how you apply changes to your pods and replica sets.

    + > Naming convention: when you create a Deployment, it creates a Replicaset named as: `<deployment-name>-<random-string>`. A Replicaset create a Pod named as: `<replicaset-name>-<randome-string>`

  + [Kubernetes - Pod]({{< ref "/pages/Kubernetes - Pod" >}})

  + [Kubernetes - Metrics]({{< ref "/pages/Kubernetes - Metrics" >}})

## Cluster Management

  + [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters)

    + `kubectl config set-context <your-context>`.

    + 


