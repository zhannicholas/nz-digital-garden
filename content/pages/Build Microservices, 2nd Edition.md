---
tags:
- Microservice
title: Build Microservices, 2nd Edition
categories:
date: 2023-04-04
lastMod: 2023-04-04
---
This in my reading notes of [Build Microservices, 2nd Edition](https://www.oreilly.com/library/view/building-microservices-2nd/9781492034018/).



# Chapter 1. What Are Microservices


  + > *Microservices* are independently releasable services that are modeled around a business domain.

  + Microservices embrace the concept of **information hiding**. Information hiding means hiding as much information as possible inside a component and exposing as little as possible via external interfaces.

  + ## Key Concepts of Microservices


    + ### Independent Deployability


      + **Independent deployability** is the idea that we can make a change to a microservice, deploy it, and release that change to our users, without having to deploy any other microservices.


        + Independent deployability is not just the fact that we can do this; it's that this is actually **how you manage deployments in your system**.

        + To ensure independent deployability, we need to make sure our microservices are **loosely coupled**: we must be able to change one service without having to change anything else.

    + ### Modeled Around a Business Domain


      + Like domain-driven design, we use this idea to define our **service boundaries**.

      + By modeling services around business domains, we can make it easier to roll out new functionality and to recombine microservices in different ways to deliver new functionality to our users.

    + ### Owning Their Own State


      + If a microservice wants to access data held by another microservice, it should go and ask that second microservice for the data.

      + Don’t share databases unless you really need to. And even then do everything you can to avoid it. In the author's opinion, sharing databases is one of the worst things you can do if you’re trying to achieve independent deployability.

    + ### Size


      + Some frequently asked questions


        + How big should a microservice be?

        + How do you measure the size of a microservice?

      + James Lewis, technical director at Thoughtworks, has been known to say that “**a microservice should be as big as my head.**”

        + The rationale behind this statement is that **a microservice should be kept to the size at which it can be easily understood.**

    + ### Flexibility


      + Another quote from James Lewis is that “microservices buy you options.”

        + Lewis was being deliberate with his words—they *buy you options*.

        + They have a cost, and you must decide **whether the cost is worth the options you want to take up**. The resulting flexibility on a number of axes—organizational, technical, scale, robustness—can be incredibly appealing.

  + ## The Monolith


    + In this book, the word *monolith* primarily refers to a unit of deployment. When all functionality in a system must be deployed together, it's a monolith.

    + ### The Distributed Monolith

      + {{< logseq/orgQUOTE >}}A distributed system is one in which the failure of a computer you didn’t even know existed can render your own computer unusable.
--- Leslie Lamport
{{< / logseq/orgQUOTE >}}

      + A distributed monolith is a system that consists of multiple services, but for whatever reason, the entire system must be deployed together.

  + ## Advantages of Microservices

    + ### Technology Heterogeneity


      + With a system composed of multiple, collaborating microservices, we can decide to use different technologies inside each one.

    + ### Robustness


      + A key concept in improving the robustness of your application is the **bulkhead**.

        + A component of a system may fail, but as long as that failure doesn’t cascade, you can isolate the problem, and the rest of the system can carry on working. **Service boundaries become your obvious bulkheads**.

    + ### Scaling

      + In a giant monolithic application, we need to handle scaling everything as a piece. With smaller services, we can scale just those services that need scaling.

    + ### Ease of Deployment

      + With microservices, we can make a change to a single service and deploy it independently of the rest of the system. This allows us to get our code deployed more quickly.

    + ### Organizational Alignment

      + Microservices allow us to better align our architecture to our organization, helping us minimize the number of people working on any one codebase to hit the sweet spot of team size and productivity.

    + ### Composability

      + One of the key promises of distributed systems and service-oriented architectures is that we open up opportunities for reuse of functionality.

# Chapter 2. How to Model Microservices

# Chapter 3. Splitting the Monolith


  + Microservices are not the goal. You don’t “win” by having microservices. **you must have a clear understanding of what you expect to achieve.**

  + Without a clear understanding as to what you are trying to achieve, you could fall into the trap of confusing activity with outcome.

  + ## What to Split First

    + Fundamentally, the decision about which functionality to split into a microservice will end up being a balance between these two forces—**how easy the extraction is** versus t**he benefit of extracting the microservice in the first place**.

  + ## Useful Decompositional Patterns

    + ### Strangler Fig Pattern

      + The [Strangler Fig Pattern](https://martinfowler.com/bliki/StranglerFigApplication.html) describes the process of wrapping an old system with the new system over time, allowing the new system to take over more and more features of the old system incrementally.

    + ### Parallel Run

      + The parallel run pattern: running both your monolithic implementation of the functionality and the new microservice implementation side by side, serving the same requests, and comparing the results.

    + ### Feature Toggle

      + A feature toggle is a mechanism that allows a feature to be switched off or on, or to switch between two different implementations of some functionality.

    + 

# Chapter 4. Microservices Communication Style


  + ## From In-Process to Inter-Process


    + Namely, calls *between* different processes across a network (inter-process) are *very* different from calls *within* a single process (in-process).

    + ### Five Types of Failure Modes


      + #### Crash failure

        + Everything was fine till the server crashed. Reboot!

      + #### Omission failure

        + You sent something, but you didn’t get a response. Also includes situations in which you expect a downstream microservice to be firing messages (perhaps including events), and it just stops.

      + #### Timing failure

        + Something happened too late (you didn’t get it in time), or something happened too early!

      + #### Response failure

        + You got a response, but it just seems wrong. For example, you asked for an order summary, but needed pieces of information are missing in the response.

      + #### Arbitrary failure

        + Otherwise known as Byzantine failure, this is when something has gone wrong, but participants are unable to agree if the failure has occurred (or why).

  + people gravitate to technology that is familiar to them, or perhaps just to the latest hot technology they learned about from a conference.


    + The problem with this is that when you buy into a specific technology choice, you are often buying into a set of ideas and constraints that come along for the ride.

    + These constraints might not be the right ones for you—and the mindset behind the technology may not actually line up with the problem you are trying to solve.

  + ## Style of Microservice Communication


    + Different styles of inter-microservice communication along with example implementing technologies.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492034018/files/assets/bms2_0401.png)

    + ### Synchronous blocking

      + A microservice makes a call to another microservice and blocks operation waiting for the response.

      + Synchronous blocking is simple. However, the main challenge with synchronous calls is the **inherent temporal coupling** that occurs.

      + The sender of the call will be blocked for a prolonged period of time waiting for a response if the downstream microservice is slow.

    + ### Asynchronous nonblocking

      + The microservice emitting a call is able to carry on processing whether or not the call is received.

      + With nonblocking asynchronous communication, the microservice making the initial call and the microservice (or microservices) receiving the call are **decoupled temporally**.
      + The main downsides of nonblocking asynchronous communication, relative to blocking synchronous communication, are the level of complexity and the range of choice.

    + ### Request-response

      + A microservice sends a request to another microservice asking for something to be done. It expects to receive a response informing it of the result.

      + All forms of request-response interaction are likely going to require some form of time-out handling to avoid issues where the system gets blocked waiting for something that may never happen.

    + ### Event-driven

      + Microservices emit events, which other microservices consume and react to accordingly. The microservice emitting the event is unaware of which microservices, if any, consume the events it emits.

    + ### Common data

      + Microservices collaborate via some shared data source.

      + This pattern is used when one microservice puts data into a defined location, and another microservice (or potentially multiple microservices) then makes use of the data.

# Chapter 5. Implementing Microservice Communication


  + Looking for the ideal technology


    + make backward compatibility easy

    + make your interface explicit

    + keep your APIs Technology Agnostic

    + make your service simple for your consumers

    + hide internal implementation detail

  + ## Technology Choices


    + ### Remote Procedure Calls

      + {{< logseq/orgTIP >}}Remote Procedure Calls (RPC) are frameworks that allow for local method calls to be invoked on a remote process. Common options include SOAP and gRPC.
{{< / logseq/orgTIP >}}

      + Most of RPC implementations require an explicit schema. In the context of RPC, the schema is often referred to as an interface definition language (IDL). The use of a separate schema makes it easier to generate client and server stubs for different technology stacks.

      + Typically, using an RPC technology means you are buying into a serialization protocol. The RPC framework defines how data is serialized and deserialized.

      + RPC frameworks that have an explicit schema make it very easy to **generate client code**. This can avoid the need for client libraries, as any client can just generate their own code against this service specification.

      + The core idea of RPC is to hide the complexity of a remote call.

      + If you’re having to support a wide variety of other applications that might need to talk to your microservices with RPC, the need to compile client-side code against a server-side schema can be problematic. In that case, some form of REST over HTTP API would likely be a better fit.

    + ### REST

      + {{< logseq/orgTIP >}}Representational State Transfer (REST) is an architectural style where you expose resources (Customer, Order, etc.) that can be accessed using a common set of verbs (GET, POST).
{{< / logseq/orgTIP >}}

      + Most important when thinking about REST is the concept of resources. How a resource is shown externally is completely decoupled from how it is stored internally.

      + Another principle introduced in REST that can help us avoid the coupling between client and server is the concept of hypermedia as the engine of application state (often abbreviated as HATEOAS).

        + {{< logseq/orgTIP >}}Hypermedia is a concept wherein a piece of content contains links to various other pieces of content in a variety of formats.
{{< / logseq/orgTIP >}}

      + The idea behind HATEOAS is that clients should perform interactions with the server (potentially leading to state transitions) via links to other resources.

    + ### GraphQL

      + GrashQL is a relatively new protocol that allows consumers to define custom queries that can fetch information from multiple downstream microservices, filtering the results to return only what is needed.

    + ### Message Brokers

      + {{< logseq/orgTIP >}}Message Brokers are middlewares that allow for asynchronous communication via queues or topics.
{{< / logseq/orgTIP >}}

      + Brokers tend to provide either **queues** or **topics**, or both.

        + Queues are typically point to point. A sender puts a message on a queue, and a consumer reads from that queue.

        + With a topic-based system, multiple consumers are able to subscribe to a topic, and each subscribed consumer will receive a copy of that message.

      + A queue allows for one consumer group. With topics, you can have multiple consumer groups.

      + Topics are a good fit for event-based collaboration, whereas queues would be more appropriate for request/response communication. This should be considered as general guidance rather than a strict rule, however.

      + **Guaranteed delivery** describes a commitment by the broker to ensure that the message is delivered.

      + One of the easier ways to provide guaranteed delivery is allowing the message to be resent. This can result in a consumer seeing the same message more than once (even if this is a rare situation).

      + Either way, if your broker of choice claims to implement **Guaranteed delivery**, then pay really careful attention to how it is implemented.

  + ## Schemas


    + Schemas can come in lots of different types, and picking a serialization format will typically define which schema technology you can use.

    + The author is in favor of having explicit schemas for microservice endpoints, for two key reasons.

      + Schemas go a long way toward being an explicit representation of what a microservice endpoint exposes and what it can accept.

      + Schemas can catch accidental breakages of microservice endpoints.

    + ### Structural Versus Semantic Contract Breakage

      + Broadly speaking, we can break contract breakages down into two categories—structural breakages and semantic breakages.

        + A **structural breakage** is a situation in which the structure of the endpoint changes in such a way that a consumer is now incompatible—this could represent fields or methods being removed, or new required fields being added.

        + A **semantic breakage** refers to a situation in which the structure of the microservices endpoint remains the same but the behavior changes in such a way as to break consumers’ expectations.

      + By using schemas and comparing different versions of schemas, we can catch structural breakages. Catching semantic breakages requires the use of testing.

  + ## Avoiding Breaking Changes


    + ### Expansion changes

      + Add new things to a microservice interface; don’t remove old things.

    + ### Tolerant reader

      + When consuming a microservice interface, be flexible in what you expect.

    + ### Right Technology

      + Pick technology that makes it easier to make backward-compatible changes to the interface.

    + ### Explicit interface

      + Be explicit about what a microservice exposes. This makes things easier for the client and easier for the maintainers of the microservice to understand what can be changed freely.

    + ### Catch accidental breaking changes early

      + Have mechanisms in place to catch interface changes that will break consumers in production before those changes are deployed.

    + 

    + 

  + ## Managing Breaking Changes


    + If a breaking change is unavoidable, we have several options to do:

      + **Lockstep deployment**

        + Require that the microservice exposing the interface and all consumers of that interface are changed at the same time.

      + **Coexist incompatible microservice versions**

        + Run old and new versions of the microservice side by side.

      + **Emulate the old interface**

        + Have your microservice expose the new interface and also emulate the old interface.

# Chapter 6. Workflow


  + Typically when we talk about database transactions, we are talking about ACID transactions.

  + ## Distributed Transactions---Two-Phase Commits

    + The *two-phase commit algorithm* (sometimes shortened to *2PC*) is broken into two phases (hence the name *two-phase commit*): a **voting** phase and a **commit** phase.


      + During the *voting phase*, a central coordinator contacts all the workers who are going to be part of the transaction and asks for confirmation as to whether or not some state change can be made. If all workers agree that the state change they are asked for can take place, the algorithm proceeds to the next phase. If any worker says the change cannot take place, perhaps because the requested state change violates some local condition, the entire operation aborts.

        + {{< logseq/orgNOTE >}}It’s important to highlight that the change does not take effect immediately after a worker indicates that it can make the change. Instead, the worker is guaranteeing that it will be able to make that change at some point in the future.
{{< / logseq/orgNOTE >}}

      + If any workers didn’t vote in favor of the commit, a rollback message needs to be sent to all parties to ensure that they can clean up locally, which allows the workers to release any locks they may be holding. If all workers agreed to make the change, we move to the commit phase. Here, the changes are actually made, and associated locks are released.

        + It’s important to note that in such a system, we cannot in any way guarantee that these commits will occur at exactly the same time.

    + Coming back to our definition of ACID, isolation ensures that we don’t see intermediate states during a transaction. But **with this two-phase commit, we’ve lost that guarantee**.

    + {{< logseq/orgCAUTION >}}The more participants you have, and the more latency you have in the system, the more issues a two-phase commit will have. 2PC can be a quick way to inject huge amounts of latency into your system, especially if the scope of locking is large, or if the duration of the transaction is large. It’s for this reason two-phase commits are typically used only for very short-lived operations. The longer the operation takes, the longer you’ve got resources locked!
{{< / logseq/orgCAUTION >}}

  + ## Sagas

    + Unlike a two-phase commit, a saga is by design an algorithm that can coordinate multiple changes in state, but avoids the need for locking resources for long periods of time.

    + A saga does this by modeling the steps involved as discrete activities that can be executed independently.

    + {{< logseq/orgCAUTION >}}A saga does not give us atomicity in ACID terms such as we are used to with a normal database transaction.
{{< / logseq/orgCAUTION >}}

    + ### Saga Failure Modes

      + With a saga being broken into individual transactions, we need to consider how to handle failure—or, more specifically, how to recover when a failure happens. The original saga paper describes two types of recovery: backward recovery and forward recovery.

      + **Backward recovery** involves reverting the failure and cleaning up afterwards—a **rollback**. For this to work, we need to define compensating actions that allow us to undo previously committed transactions.

      + **Forward recovery** allows us to pick up from the point where the failure occurred and keep processing. For that to work, we need to be able to retry transactions, which in turn implies that our system is persisting enough information to allow this **retry** to take place.

      + It’s really important to note that a saga allows us to recover from business failures, not technical failures. A **compensating transaction** is an operation that undoes a previously committed transaction. Because we cannot always cleanly revert a transaction, we say that these compensating transactions are **semantic rollbacks**.

    + ### Orchestrated sagas

      + Orchestrated sagas use a central coordinator (what we’ll call an orchestrator from now on) to define the order of execution and to trigger any required compensating action.

      + You can think of orchestrated sagas as a **command-and-control** approach: the orchestrator controls what happens and when, and with that comes a good degree of visibility into what is happening with any given saga.

    + ### Choreographed sagas

      + A choreographed saga aims to distribute responsibility for the operation of the saga among multiple collaborating services.

      + If orchestration is a command-and-control approach, choreographed sagas represent a trust-but-verify architecture.

      + Choreographed sagas will often make heavy use of events for collaboration between services.

# Chapter 7. Build


  + ## Continuous Integration (CI)

    + How do you know if you’re actually practicing CI? Jez Humble gives us three questions he asks people to test if they really understand what CI is about—it might be interesting to ask yourself these same questions:

      + Do you check in to mainline once per day?

      + Do you have a suite of tests to validate your changes?

      + When the build is broken, is it the #1 priority of the team to fix it?

  + ## Branching Models

    + **Feature branch**: create a source code branch for each feature being worked on.

      + {{< logseq/orgCAUTION >}}The problem is that when you work on a feature branch, you aren’t regularly integrating your changes with everyone else. Fundamentally, you are delaying integration. And when you finally decide to integrate your changes with everyone else, you’ll have a much more complex merge.
{{< / logseq/orgCAUTION >}}

    + **Trunked-based development**: Let everyone check in to the same "trunk" of source code. Keep changes from impacting other people.

    + Open source development is characterized by a large number of ad hoc contributions from time-poor “untrusted” committers, whose changes require vetting by a smaller number of “trusted” contributors.

  + ## Build Pipelines and Continuous Delivery

    + {{< logseq/orgTIP >}}Build a deployment artifact for your microservice once. Reuse this artifact everywhere you want to deploy that version of your microservice. Keep your deployment artifact environment-agnostic—store environment-specific configuration elsewhere.
{{< / logseq/orgTIP >}}

  + ## Mapping Source Code and Builds to Microservices

    + ### One Giant Repo, One Giant Build

      + Lump everything in together. We have a single, giant repository storing all our code, and we have a single build.

    + ### One Repository per Microservices (Multirepo)

      + The code for each microservice is stored in its own source code repository.

    + ### Monorepo

      + With a monorepo approach, code for multiple microservices (or other types of projects) is stored in the same source code repository.

# Chapter 8. Deployment


  + ## Principles of Microservice Deployment


    + **Isolation execution**

      + Run microservice instances in an isolated fashion such that they have their own computing resources, and their execution cannot impact other microservice instances running nearby.

    + **Focus on automation**

      + As the number of microservices increases, automation becomes increasingly important. Focus on choosing technology that allows for a high degree of automation, and adopt automation as a core part of your culture.

    + **Infrastructure as code**

      + Represent the configuration for your infrastructure to ease automation and promote information sharing. Store this code in source control to allow for environments to be re-created.

    + **Zero-downtime deployment**

      + Take independent deployability further and ensure that deploying a new version of a microservice can be done without any downtime to users of your service (be they humans or other microservices).

      + With a **rolling upgrade**, your microservice isn’t totally shut down before the new version is deployed, instead instances of your microservice are slowly ramped down as new instances running new versions of your software are ramped up.

    + **Desired state management**

      + Use a platform that maintains your microservice in a defined state, launching new instances if required in the event of outages or traffic increases.

      + The beauty of desired state management is that the platform itself manages how the desired state is maintained. It frees development and operations people alike from having to worry about exactly how things are being done.

# Chapter 9. Testing


  + ## Types of Tests

    + Brian Marick's testing quadrant.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492034018/files/assets/bms2_0901.png)

      + At the bottom of the quadrant, we have tests that are *technology facing*—that is, tests that aid the developers in creating the system in the first place.

      + The top half of the quadrant includes those tests that help the nontechnical stakeholders understand how your system works, which we call *business-facing* tests.

      + The ability to automate some tasks, such as verifying how something looks, was previously limited exclusively to manual exploratory testing. The maturing of tooling that enables visual assertions has allowed us to start automating tasks that were previously done manually. You shouldn’t see this as a reason to have no manual testing, though; you should instead see it as a chance to free up testers’ time to focus on less repetitive, *exploratory* testing.

      + Automation as a way of freeing up our brainpower for the things we do best.

  + ## Test Scope

    + Mike Cohn's test pyramid

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492034018/files/assets/bms2_0902.png)

      + The key thing to take away when reading the pyramid is that as we go up the pyramid, the test scope increases, as does our confidence that the functionality being tested works. On the other hand, the feedback cycle time increases as the tests take longer to run, and when a test fails, it can be harder to determine which functionality has broken.

      + As we go down the pyramid, in general the tests become much faster, so we get much faster feedback cycles. we don’t get a lot of confidence that our system as a whole works if we’ve tested only a small portion.

    + ### Unit Tests

      + Unit tests typically test a single function or method call.

      + The tests generated as a side effect of *test-driven design* (TDD) will fall into this category, as do the sorts of tests generated by techniques such as property-based testing.

      + Unit tests help us developers and thus would be technology facing, not business facing, in Marick’s terminology. They are also where we hope to catch most of our bugs.

      + Unit tests would cover small parts of the code in isolation.

      + The primary goal of these tests is to give us very fast feedback about whether our functionality is good.

      + Unit tests are also important for supporting refactoring of code, allowing us to restructure our code as we go secure in the knowledge that our small-scoped tests will catch us if we make a mistake.

    + ### Service Tests

      + Service tests are designed to bypass the user interface and test our microservices directly.

      + By running tests against a single microservice in this way, we get increased confidence that the service will behave as we expect, but we still keep the scope of the test somewhat isolated.

      + Our service tests want to test a slice of functionality across the whole microservice, and only that microservice. Thus, our service test suite needs to stub out downstream collaborators and configure the microservice under test to connect to the stub services.

      + [mountebank](http://www.mbtest.org/) is a smart stub/mock service.

    + ### End-to-End Tests

      + End-to-end tests are tests run against your entire system.

      + To implement an end-to-end test, we need to deploy multiple microservices together, and then run a test against all of them. Obviously, this test has a much larger scope, resulting in more confidence that our system works!

      + If you have tests that sometimes fail, but everyone just reruns them because they may pass again later, then you have **flaky tests**.

        + {{< logseq/orgCAUTION >}}When we detect flaky tests, it is essential that we do our best to remove them. Otherwise, we start to lose faith in a test suite that “always fails like that.”
{{< / logseq/orgCAUTION >}}

      + #### Contract Tests and Consumer-Driven Contracts (CDCs)

        + With **contract tests**, a team whose microservice consumes an external service writes tests that describe how it expects an external service will behave. This is less about testing your own microservice and more about specifying how you expect an external service to behave.

        + A good practice in CDCs is to have someone from the producer and consumer teams collaborate on creating the tests.

        + In cross-team collaboration, CDCs are an explicit reminder of Conway’s law.

        + CDCs tests are focused on how a consumer will use the service, and the trigger if they break is very different when compared with service tests.

        + With CDCs, we can identify a breaking change prior to our software going into production without having to use a potentially expensive end-to-end test.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492034018/files/assets/bms2_0910.png)

      + [Pact](https://pact.io/) is a consumer-driven testing tool.

      + In Agile, stories are often referred to as a placeholder for a conversation.

# Chapter 10. From Monitoring to Observability


  + ## Observability Versus Monitoring

    + The **observability** of a system is the extent to which you can understand the internal state of the system from external outputs.

    + In practice, the more observable a system is, the easier it will be for us to understand what the problem is when something goes wrong.

    + **Monitoring** is something we do. We monitor the system. We look at it. Things will start to go wrong if you focus on just the monitoring—the activity—without thinking about what you expect that activity to achieve.

    + We can therefore see monitoring as an activity—something we do—with observability being a property of the system.

    + Logs, events, and metrics might help you make things observable, but be sure to focus on making the system understandable rather than throwing in lots of tools.

  + ## Building Blocks for Observability

    + building blocks that can help improve the observability of your system architecture:

    + ### Log aggregation

      + > Collecting information across multiple microservices, a vital building block of any monitoring or observability solution.

      + But on the subject of log aggregation, I’ll come as close I get to giving one-size-fits-all advice: you should view implementing a log aggregation tool as a *prerequisite* for implementing a microservice architecture.

        + {{< logseq/orgQUOTE >}}Rather than just saying you *must* do X or Y, I’ve tried to give you context and guidance and explain the nuances of certain decisions—that is, I’ve tried to give you the tools to make the right choice for your context. 
{{< / logseq/orgQUOTE >}}

      + The date stamps in the log lines are generated on the machines where the microservices are running. Unfortunately, we cannot guarantee that the clocks on these different machines are in sync.

        + Fundamentally, this means we have two limitations when it comes to time in logs. We can’t get fully accurate timing information for the overall flow of calls, nor can we understand causality.

    + ### Metrics aggregation

      + > Capturing raw numbers from our microservices and infrastructure to help detect problems, drive capacity planning, and perhaps even scale our applications.

      + The secret to knowing when to panic and when to relax is to gather metrics about how your system behaves over a long-enough period of time that clear patterns emerge.

      + There are a number of ways to describe cardinality, but you can think of it as the number of fields that can be easily queried in a given data point.

      + If you are looking for systems that are able to store and manage high cardinality data, allowing for much more sophisticated observation (and questioning) of your system’s behavior, I’d strongly suggest looking at either Honeycomb or Lightstep.

    + ### Distributed tracing

      + > Tracking a flow of calls across multiple microservice boundaries to work out what went wrong and derive accurate latency information.

      + How it works

        + Local activity within a thread is captured in a span. These individual spans are correlated using some unique identifier. The spans are then sent to a central collector, which is able to construct these related spans into a single trace.

      + Getting distributed tracing up and running for your system requires a few things.

        + Firstly, you need to capture span information inside your microservices.

        + Next, you’ll need some way to send this span information to your collector

        + Lastly, of course, you need a collector able to receive this information and make sense of it all.

    + ### Are you doing OK?

      + > Looking at error budgets, SLAs, SLOs, and so on to see how they can be used as part of making sure our microservice is meeting the needs of its consumers

      + A *service-level agreement* (SLA) is an agreement reached between the people building the system and the people using the system.

        + It describes not only what the users can expect but also what happens if the system doesn’t reach this level of acceptable behavior.

      + Mapping an SLA down to a team is problematic, especially if the SLA is somewhat broad and cross-cutting. At a team level, we instead talk about *service-level objectives* (SLOs). SLOs define what the team signs up to provide.

      + To determine if we are meeting our SLOs, we need to gather real data. This is what our service-level indicators (SLI) are.

      + Error budgets are an attempt to avoid this problem by being clear about how much error is acceptable in a system.

        + Error budgets help give you a clear understanding of how well you are achieving (or not) an SLO, allowing you to make better decisions about what risks to take.

        + Error budgets are as much about giving teams breathing room to try new things as anything.

    + ### Alerting

      + What should you alert on? What does a good alert look like?

    + ### Semantic monitoring

      + Thinking differently about the health of our systems, and about what should wake us up at 3 a.m.

    + ### Testing in production

      + There are various testing in production techniques

        + **Synthetic transactions**

          + With synthetic transactions, we inject fake user behavior into our production system. This fake user behavior has known inputs and expected outputs.

        + **A/B testing**

          + With an A/B test, you deploy two different versions of the same functionality, with users seeing either the “A” or the “B” functionality. You are then able to see which version of the functionality performs best.

        + **Canary release**

          + A small portion of your user base gets to see the new release of functionality. If this new functionality works well, you can increase the portion of your user base that sees the new functionality to the point that the new version of the functionality is now used by all users.

          + On the other hand, if the new functionality doesn’t work as intended, you’ve impacted only a small portion of your user base and can either revert the change or try to fix whatever problem you have identified.

        + **Parallel run**

          + With a parallel run, you execute two different equivalent implementations of the same functionality side by side.

          + Any user request is routed to both versions, and their results can be compared.

        + **Smoke tests**

          + Used after the software is deployed into production but before it is released, smoke tests are run against the software to make sure it is working appropriately.

        + **Chaos engineering**

          + chaos engineering can involve injection of faults into a production system to ensure that it is able to handle these expected issues.

# Chapter 11. Security


  + ## Core Principles


    + The problem with security is that you’re only as secure as your least secure aspect.

    + ### Principle of Least Privilege


      + The principle of least privilege describes the idea that when granting access we should grant the minimum access a party needs to carry out the required functionality, and only for the time period they need it.

    + ### Defense in Depth


      + Having multiple protections in place to defend against attackers is vital.

      + #### Types of Security Controls


        + When considering the security controls we might put in place to secure our system, we can categorize them as:

        + **Preventative**

          + Stopping an attack from happening. This includes storing secrets securely, encrypting data at rest and in transit, and implementing proper authentication and authorization mechanisms.

        + **Detective**

          + Alerting you to the fact that an attack is happening/has happened. Application firewalls and intrusion detection services are good examples.

        + **Responsive**

          + Helping you respond during/after an attack. Having an automated mechanism to rebuild your system, working backups to recover data, and a proper comms plan in place in the wake of an incident can be vital.

    + ### Automation


      + As we have so many more moving parts with microservice architectures, automation becomes key to helping us manage the growing complexity of our system.

      + automation can help us recover in the wake of an incident. We can use it to revoke and rotate security keys and also make use of tooling to help detect potential security issues more easily. As with other aspects of microservice architecture, embracing a culture of automation will help you immensely when it comes to security.

      + 

    + ### Build Security into the Delivery Process

      + Like so many other aspects of software delivery, security is all too often considered an afterthought.

  + ## The Five Functions of Cybersecurity


    + The US National Institute of Standards and Technology (NIST) outlines a [useful five-part model](https://www.nist.gov/cyberframework/online-learning/five-functions) for the various activities involved in cybersecurity

      + **Identify** who your potential attackers are, what targets they are trying to acquire, and where you are most vulnerable.

      + **Protect** your key assets from would-be hackers.

      + **Detect** if an attack has happened, despite your best efforts.

      + **Respond** when you’ve found out something bad has occurred.

      + **Recover** in the wake of an incident.

  + ## Foundations of Application Security


    + ### Credentials


      + Credentials give a person (or computer) access to some form of restricted resource.

      + We can break the topic of credentials down into two key areas.

        + **User credentials**: the credentials of the users (and operators) of our system

          + User credentials, such as email and password combinations, remain essential to how many of us work with our software, but they also are a potential weak spot when it comes to our systems being accessed by malicious parties.

        + **secrets**: critical pieces of information that a microservice needs to operate and that are also sensitive enough that they require protecting from malicious parties.

          + the various aspects of secrets management that might require different security needs:

            + **Creation**. How do we create the secret in the first place?

            + **Distribution**. Once the secret is created, how do we make sure it gets to the right place (and only the right place)?

            + **Storage**. Is the secret stored in a way that ensures only authorized parties can access it?

            + **Monitoring**. Do we know how this secret is being used?

            + **Rotation**. Are we able to change the secret without causing problems?

    + ### Patching

      + The issue of keeping on top of patching is becoming more complex as we deploy increasingly complex systems. We need to become more sophisticated in how we handle this fairly basic concept.

    + ### Backups

      + Make sure you back up critical data, keep those backups in a system separate to your main production environment, and make sure the backups actually work by regularly restoring them.

    + ### Rebuild

      + A rootkit is a bundle of software that is designed to hide the activities of an unauthorized party, and it’s a technique commonly used by attackers who want to remain undetected, allowing them time to explore the system.

      + Being able to rebuild your microservice and recover its data in an automated fashion helps you recover in the wake of an attack and also has the advantage of making your deployments easier across the board, having positive benefits for development, test, and production operations activities.

  + ## Securing Data


    + As we break our monolithic software apart into microservices, our data moves around our systems more than before. It doesn’t just flow over networks; it also sits on disk.

    + ### Data in Transit

      + The four key concerns when it comes to data in transit

        + **Client Identity**. Is the client sending this request actually the client we expected?

        + **Visibility of data**. Can anyone see the request being sent?

        + **Manipulation of data**. Can anyone change the request being sent?

        + **Server Identity**. Is this server actually the server we expected?

    + ### Data at Rest

      + The mechanisms by which data at rest can be protected are many and varied, but there are some general things to bear in mind.

        + Go with the well known

          + If you find a need to encrypt and decrypt data in your own system, make sure you’re going with well-known and tested implementations.

        + Pick your targets

          + think about what data can be put into logfiles to help with problem identification, and the computational overhead of encrypting everything can become pretty onerous and require more powerful hardware as a result.

        + Be frugal

          + The advantages to being frugal with data collection are manifold. First, if you don’t store it, no one can steal it. Second, if you don’t store it, no one (e.g., a governmental agency) can ask for it either!

# Chapter 12. Resiliency


  + ## What is resiliency


    + David D. Woods has attempted to categorize the different aspects of resilience to help us think more widely about what resiliency actually means.1 These four concepts are:

      + **Robustness**

        + The ability to absorb expected perturbation

      + **Rebound**

        + The ability to recover after a traumatic event

      + **Graceful extensibility**

        + How well we deal with a situation that is unexpected

      + **Sustained adaptability**

        + The ability to continually adapt to changing environments, stakeholders, and demands

    + ### Robustness

      + Robustness is the concept whereby we build mechanisms into our software and processes to accommodate expected problems.

      + Robustness by definition requires prior knowledge—we are putting measures into place to deal with known perturbations.

      + One of the challenges around improving the robustness of our system is that as we increase the robustness of our application, we introduce more complexity to our system, which can be the source of new issues.

    + ### Rebound

      + How well we recover—rebound—from disruption is a key part of building a resilient system.

      + We can improve our ability to rebound from an incident by putting things into place in advance.

    + ### Graceful Extensibility

      + With rebound and robustness, we are primarily dealing with the expected. We are putting mechanisms in place to deal with problems that we can foresee. But what happens when we are surprised? Graceful Extensibility can help us.

    + ### Sustained Adaptability

      + Having sustained adaptability requires us to not be complacent.

      + To work toward sustained adaptability means that you are looking to discover what you don’t know.

  + {{< logseq/orgCAUTION >}}at scale, even if you buy the best kit, the most expensive hardware, you cannot avoid the fact that things can and will fail. Therefore, you need to assume failure can happen.
{{< / logseq/orgCAUTION >}}

  + When it comes to considering if and how to scale out your system to better handle load or failure, start by trying to understand the following requirements:


    + **Response time/latency**

      + How long should various operations take? It can be useful to measure this with different numbers of users to understand how increasing load will impact the response time. Given the nature of networks, you’ll always have outliers, so setting targets for a given percentile of the responses monitored can be useful. The target should also include the number of concurrent connections/users you will expect your software to handle. So you might say, “We expect the website to have a 90th-percentile response time of 2 seconds when handling 200 concurrent connections per second.”

    + **Availability**

      + Can you expect a service to be down? Is this considered a 24/7 service? Some people like to look at periods of acceptable downtime when measuring availability, but how useful is this to someone calling your service? Either I should be able to rely on your service responding or I shouldn’t. Measuring periods of downtime is really more useful from a historical reporting angle.

    + **Durability of data**

      + How much data loss is acceptable? How long should data be kept for? This is highly likely to change on a case-by-case basis. For example, you might choose to keep user session logs for a year or less to save space, but your financial transaction records might need to be kept for many years.

  + ## Stability Patterns

    + ### Time-Outs

      + Time-outs are incredibly useful. Put time-outs on all out-of-process calls, and pick a default time-out for everything. Log when time-outs occur, look at what happens, and change them accordingly. Look at “normal” healthy response times for your downstream services, and use that to guide where you set the time-out threshold.

      + Don’t just think about the time-out for a single service call; also think about a time-out for the overall operation, and abort the operation if this overall time-out budget is exceeded.

    + ### Retries

      + Some issues with downstream calls are temporary. Often, retrying the call can make a lot of sense.

      + You will likely need to have a delay before retrying. If the initial time-out or error was caused by the fact that the downstream microservice was under load, then bombarding it with additional requests may well be a bad idea.

    + ### Bulkheads

      + In Release It!, Michael Nygard introduces the concept of a bulkhead as a way to isolate yourself from failure.

      + Separation of concerns can also be a way to implement bulkheads. By teasing apart functionality into separate microservices, we reduce the chance of an outage in one area affecting another.

    + ### Circuit Breakers

      + With a circuit breaker, after a certain number of requests to the downstream resource have failed (due either to error or to a time-out), the circuit breaker is blown. All further requests that go through that circuit breaker fail fast while the breaker is in its blown (open) state. After a certain period of time, the client sends a few requests through to see if the downstream service has recovered, and if it gets enough healthy responses it resets the circuit breaker.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492034018/files/assets/bms2_1205.png)

    + ### Isolation

      + The more one microservice depends on another microservice being available, the more the health of one impacts the ability of the other to do its job.

    + ### Redundancy

      + Having more copies of something can help when it comes to implementing redundancy, but it can also be beneficial when it comes to scaling our applications to handle increased load.

    + ### Idempotency

      + In idempotent operations, the outcome doesn’t change after the first application, even if the operation is subsequently applied multiple times. If operations are idempotent, we can repeat the call multiple times without adverse impact.

  + ## Blame

    + Creating an organization in which people have the safety to admit when mistakes are made is essential in creating a learning culture, and in turn can go a long way toward creating an organization that is able to create more robust software, quite aside from the obvious benefits of creating a happier place to work in.

# Chapter 13. Scaling


  + When we scale our systems, we do so for one of two reasons.

    + Firstly, it allows us to improve the performance of our system, perhaps by allowing us to handle more load or by improving latency.

    + Secondly, we can scale our system to improve its robustness.

  + ## The four Axes of Scaling

    + **Vertical scaling**

      + Getting a bigger machine

    + **Horizontal duplication**

      + Having multiple things capable of doing the same work.

    + **Data partitioning**

      + Dividing work based on some attribute of the data.

      + The way data partitioning works is that we take a key associated with the workload and apply a function to it, and the result is the partition (sometimes called a shard) we will distribute the work to.

    + **Functional decomposition**

      + Separation of work based on the type, e.g., microservice decomposition.

  + {{< logseq/orgQUOTE >}}The real problem is that programmers have spent far too much time worrying about efficiency in the wrong places and at the wrong times; premature optimization is the root of all evil (or at least most of it) in programming.
--- Donald Knuth
{{< / logseq/orgQUOTE >}}



  + 

  + 
