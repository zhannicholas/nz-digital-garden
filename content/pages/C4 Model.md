---
tags:
- Software Architecture
title: C4 Model
categories:
date: 2022-08-30
lastMod: 2022-08-30
---


[The C4 model for visualizing software architecture](https://c4model.com/).

The C4 model was created as a way to help software development teams (primarily architects and developers) describe and communicate software architecture, at different levels of detail, telling different stories to different types of audience, when doing up front design or retrospectively documenting an existing codebase.

![](https://c4model.com/img/c4-overview.png)

The C4 model considers the static structures of a **software system** in terms of **containers**, **components** and **code**. And **people** use the software systems that we build.

![](https://c4model.com/img/abstractions.png)

  + Person: one of the human users of our software system.

  + Software System: the highest level of abstraction and describes something that delivers value to its users, whether they are human or not.

  + Container: something that needs to be running in order for the overall software system to work. In C4 model, a container represents an *application* or a *data store*.

    + {{< logseq/orgQUOTE >}}A container is essentially a context or boundary inside which some code is executed or some data is stored. And each container is a separately deployable/runnable thing or runtime environment, typically (but not always) running in its own process space. Because of this, communication between containers typically takes the form of an inter-process communication.
{{< / logseq/orgQUOTE >}}

  + Component: a grouping of related functionality encapsulated behind a well-defined interface.

    + {{< logseq/orgCAUTION >}}In the C4 model, components are not separately deployable units.
{{< / logseq/orgCAUTION >}}

## Level 1: System Context Diagram

  + The intention of this diagram is to set context for both a technical and non technical audience. Many architecture conversations dive straight into the low level detail and miss setting the context of the high-level interactions.

![](https://c4model.com/img/bigbankplc-systemcontext.png)

## Level 2: Container Diagram

  + A container diagram helps describe the technical breakout of the major participants in the architecture. A container in C4 is defined as “something that needs to be running in order for the overall system to work”.

![](https://c4model.com/img/bigbankplc-containers.png)

## Level 3: Component Diagram

  + The C4 component diagram helps to define the roles and responsibilities within each container, along with the internal interactions.

![](https://c4model.com/img/bigbankplc-components.png)

## Level 4: Code

  + This (optional) level shows how a component is implemented as code.

![](https://c4model.com/img/bigbankplc-classes.png)


