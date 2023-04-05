---
tags:
- Reactive Programming
title: Project Reactor
categories:
date: 2022-07-28
lastMod: 2022-08-23
---


Project Reactor implements Reactive Steams and abstracts steam definitions into two primary types: `Flux` and `Mono`.

# Reactor Core Features

  + Both `Flux` and `Mono` are implementations of Reactive Streams' `Publisher`.

  + ### Flux[N]

    + A `Flux<T>` is a Reactive Streams `Publisher` which can emit 0 to n `<T>` elements (`onNext` event) then either completes or errors (`onComplete` and `onError` terminal events). If no terminal event is triggered, the Flux is infinite.

![](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/doc-files/marbles/flux.svg)

  + ### Mono[0|1]

    + A `Mono` is a specialization of `Flux` that can emit at most 1 `<T>` element: a `Mono` is either valued (complete with element), empty (complete without element) or failed (error).

![](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/doc-files/marbles/mono.svg)

    + 

# Learn Materials

  + [Project Reactor Home](https://projectreactor.io/).

  + [Daily Reactive](https://bsideup.github.io/posts/daily_reactive).
