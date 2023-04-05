---
tags:
- Software Architecture
title: Fundamentals of Software Architecture
categories:
date: 2022-08-26
lastMod: 2022-08-30
---
This is my reading notes from [Fundamentals of Software Architecture](https://www.oreilly.com/library/view/fundamentals-of-software/9781492043447/).



## Preface. Invalidating Axioms


  + {{< logseq/orgQUOTE >}}**Axiom**: A statement or proposition which is regarded as being established, accepted, or self-evidently true.
{{< / logseq/orgQUOTE >}}

  + 数学家基于公理建立数学理论，软件架构师也是如此。但是软件世界与数学世界有一点很大的不同：软件世界中的基础会不断变化（包括建立软件架构理论所依赖的公理），这个变化速度还非常快。

  + 从质变到量变。

    + The software ecosystem changes chaotically: one small change causes
another small change, when repeated hundreds of times, it generates a new ecosystem.

  + Each new era requires new practices, tools, measurements, patterns,
and a host of other changes.

  + As a developer, it's easy to become enamored with a particular technology or approach. But architects must always soberly assess the good, bad, and ugly of every choice, and virtually nothing in the real worlds offers convenient binary choices --- **everything is a
trade-off**. This is why ***trade-off analysis*** is critically important.

## Chapter 1. Introduction


  + ### What is "software architecture"?


    + 关于软件架构师，软件行业并没有一个通用的定义。Martin Fowler 写过一篇著名的白皮书 [“Who Needs an Architect?”](https://martinfowler.com/ieeeSoftware/whoNeedsArchitect.pdf)，他拒绝对架构师进行定义，而是引用了 Ralph Johnson 的话：

{{< logseq/orgQUOTE >}}Architecture
is about the important staff…whatever that is.
--- Ralph Johnson
{{< / logseq/orgQUOTE >}}
所以，不同公司可能会给出自己关于软件架构和架构师的定义。

    + Why is there no path for software architectures?


      + The industry doesn't have a good definition of software architecture itself.

      + The role of software architect embodies a massive amount and scope of responsibility that continues to expand.

      + The software architecture is a constantly moving target because of the rapidly evolving software development ecosystem.

      + Much of the material about software architecture has only historical relevance.

    + The scope of software architecture isn't the only part of the development world that constantly changes, there are new technologies, techniques, capabilities…

    + Software architects must make decisions within this constantly
changing development ecosystem. Because everything changes, including foundations upon which we make decisions, **architects
should reexamine some core axioms that informed earlier writing about software architecture**.

  + ### Defining Software Architecture


![](/assets/fundamentals-of-software-architecture/fosa_0102.png)

    + The definition of "Software Architecture" in this book: Software Architecture consists of the ***structure*** combined with ***architecture characteristics("ilities")***, ***architecture decisions***, and ***design principles***.

      + The ***structure*** of the system, refers to the type of architecture style(s) the system is implemented in (such as microservices, layered, or microkernel).


![](/assets/fundamentals-of-software-architecture/fosa_0103.png)
      + The ***architecture characteristics*** define the success criteria of a system, which is generally **orthogonal** to the functionality

of the system. Notice that all of the characteristics listed do not require knowledge of the functionality of the system, yet they are required in order for the system to function properly.

![](/assets/fundamentals-of-software-architecture/fosa_0104.png)

      + The ***architecture decisions*** define the rules for how a system should be constructed. Architecture decisions form the constraints of the system and direct the development team on what is and what is not allowed.


![](/assets/fundamentals-of-software-architecture/fosa_0105.png)

      + A ***design principle*** differs from an architecture decision in that a design principle is **a guideline rather than a hard-and-fast rule**.


![](/assets/fundamentals-of-software-architecture/fosa_0106.png)

  + ###  Expectations of an Architect


    + Defining the role of a software architect presents as much as difficulty as defining software architecture. It ranges! It's better to focus on the expectations of an architect.

    + There are eight core expectations placed on a software architect:

      + Make architecture decisions


        + An architecture is expected to define the architecture decisions and design principles used to guide technology decisions within the team, the department, or across the enterprise.

        + An architect should guide rather than specify technology choices. The key to making effective architectural decisions is asking whether the architecture decision is helping to guide teams in making the right technical choice to whether the architecture decision makes the technical choice for them.

      + Continually analyze the architecture


        + An architect is expected to continually analyze the architecture and current technology environment and then recommend the solutions for improvement.

        + An architect must holistically analyze changes in technology and problem domains to determine the soundness of architecture.

      + Keep current with latest trends


        + *An architect is expected to keep current with the latest technology and industry trends.*

        + Developers must keep up to date on the latest technologies they use on a daily basis to remain relevant (and to retain a job!). An architect has an even more critical requirement to this, the decisions an architect makes tend to be long-lasting and difficult to change. So, understanding the key trends helps the architect prepare for the future and make the correct decision.

      + Ensure compliance with decisions


        + *An architect is expected to ensure compliance with architecture decisions and design principles.*

        + Ensuring compliance means that the architect is continually verifying that development teams are following the architecture decisions and design principles defined by the architect.

      + Diverse exposure and experience


        + *An architect is expected to have exposure to multiple and diverse technologies, frameworks, platforms, and environments.*

        + This expectation doesn't mean an architect must be an expert in everything, but rather that an architect must at least be familiar with a variety of technologies.

        + {{< logseq/orgTIP >}}One of the best way of mastering this expectation is for the architect to stretch their comfort zone. Focus on technical breadth rather than technical depth.  
{{< / logseq/orgTIP >}}

      + Have business domain knowledge


        + *An architect is expected to have a certain level of business domain expertise.*

        + Without business domain knowledge, it is difficult to understand the business problem, goals, and requirements, making it difficult to design an effective architecture to meet the requirement of the business.

        + {{< logseq/orgTIP >}}The most successful architects we know are those have broad, hands-on technical knowledge coupled with a strong knowledge of a particular domain.
{{< / logseq/orgTIP >}}

      + Possess interpersonal skills


        + *An architect is expected to possess exceptional interpersonal skills, including teamwork, facilitation, and leadership.*

        + {{< logseq/orgTIP >}}No matter what they tell you, it's always a people problem.
{{< / logseq/orgTIP >}}

      + Understand and navigate politics


        + *An architect is expected to understand the political climate of the enterprise and be able to navigate the politics.*

        + Almost every decision an architect makes will be challenged, challenged by product owners, project managers, and business stakeholders, even developers.

  + ### Laws of Software Architecture


    + {{< logseq/orgQUOTE >}}Everything in software architecture is a trade-off.
	--- First Law of Software Architecture
{{< / logseq/orgQUOTE >}}

      + Nothing exists on a nice, clean spectrum for software architects. Every decision must take into account many opposing factors.

      + {{< logseq/orgQUOTE >}}If an architect thinks they have discovered something that isn't not a trade-off, more likely they just haven't identified the trade-off yet.
	--- Corollary 1
{{< / logseq/orgQUOTE >}}

    + {{< logseq/orgQUOTE >}}Why is more important than how.
	--- Second Law of Software Architecture  
{{< / logseq/orgQUOTE >}}

## Chapter 2. Architectural Thinking


  + There are four main aspects of thinking like an architect:

    + It's understanding the difference between architecture and design and knowing how to collaborate with development teams to make architecture work.

    + It's about having a wide breadth of technical knowledge while still maintaining a certain level of technical depth, allowing the architect to see solutions and possibilities that others don't see.

    + It's about understanding, analyzing, and reconciling trade-offs between various solutions and technologies.

    + It's about understanding the importance of business drivers and how to translate to architectural concerns.

  + ### Architecture Versus Design


    + To make architecture work, both the physical and virtual barriers that exist between architects and developers must be broken down, thus forming a strong **bidirectional** relationship between architects and development teams.

    + Architecture and design are both part of the circle of life with a software project and must always be kept in synchronization with each other in order to succeed.

  + ### Technical Breadth


    + Unlike a developer, who must have a significant amount of *technical depth* to perform their job, a software architect must have a significant amount of *technical breadth* to think like an architect and see things with an 
architecture point of view.

    + As shown in figure below (The pyramid representing all knowledge), any individual can partition all their knowledge into three sections:* stuff you know*, *stuff

you know you don't know*, *stuff you don't
know you don't know*.

![](/assets/fundamentals-of-software-architecture/fosa_0203.png)

    + Developers must maintain expertise to retain it. The *stuff you know *is also the *stuff you must maintain*---nothing is static in the software world. The things at the pyramid require time investment to maintain expertise. Ultimately, the size of the top of an individual's pyramid is *technical depth*. The most important parts of the pyramid for architects are the top and middle sections.


![](/assets/fundamentals-of-software-architecture/fosa_0205.png)

    + As an architect, *breadth* is more important than *depth*.

    + The ***Frozen Caveman Anti-Pattern*** describes an architect who always reverts back to their pet irrational concern for every architecture.

    + Understanding the difference between genuine(真正的) versus perceived(表面上看起来是的) technical risk is part of the ongoing learning process for architecture. Thinking like an architect requires overcoming these "frozen caveman" ideas and experiences, seeing other solutions, and asking more relevant questions.

  + ### Analyzing Trade-offs


    + *Architecture is the stuff you can't Google*. Everything in architecture is a trade-off, which is why the famous answer to every architecture question in the universe is "***it depends.***".

    + There are no right or wrong answers in architecture---only trade-offs. Programmers know the benefits of
everything and the trade-offs of nothing. Architects need to understand both.

    + Thinking architecturally is looking at the benefits of an given solution, but also analyzing the negatives, or trade offs, associated with a solution.

  + ### Understanding Business Drivers


    + Thinking like an architect is understanding the business drivers that are required for the success of the system and translating those requirements into architecture characteristics. This challenging task requires the architect to have some level of business domain knowledge and healthy, collaborative relationships with key business stakeholders.

  + ### Balancing Architecture and Hands-on Coding


    + This is a difficult task for architects. The first tip is striving for a balance between hands-on coding and

being an architect is avoid the ***bottleneck trap***.

      + The *bottleneck trap* occurs when the architect has taken ownership of code with the critical path of a project and become bottleneck of the team. One way to avoid the bottleneck trap is to delegate the critical path to others.

    + If an architect is not able to develop code with development team, there're several basic ways to remain hands-on and maintain some level of technical depth:


      + Do frequent proof-of-concepts or POCs. A good advice is, whenever possible, write the best production-ready code as possible as they can.

      + Tackle some of the technical debt stories or architecture stories (usually low priority), freeing the development team up to work on the critical functional user stories.

      + Working on bug fixes with an iteration.

      + Leveraging automation by creating simple command-line tools and analyzers to help the development team with their day-to-day tasks.

      + Do frequent code reviews.

## Chapter 3. Modularity


  + {{< logseq/orgQUOTE >}}95% of the words [about software architecture] are spent extolling the benefits of "modularity" and that little, if anything, is said about how to achieve it.
--- Glenford J. Myers(1978)
{{< / logseq/orgQUOTE >}}

  + Different platforms offer different reuse mechanisms for code, but all support some way of grouping related code together into ***modules***. Modularity is an organizing principle.

  + Architects must be aware of how developers package things because it has important implications in architecture.

  + ### Measuring Modularity

    + Researchers have created a variety of language-agnostic metrics to help architects understand modularity. This book focus on three key concepts: *cohesion*, *coupling* and *connascence*.

    + #### Cohesion


      + ***Cohesion(内聚) *** refers to what extent the parts of a module should be contained within the same module. In other words, it's a measure of how related the parts are to one another.

      + Computer scientists have defined a range of cohesion measures, listed them from best to worst:

        + **Functional cohesion(****功能内聚****)**: Every parts of a module is related to the other, ans the module contains everything essential to function.

        + **Sequential cohesion(****顺序内聚****)**: Two modules interact, where one outputs data that becomes the input for the other.

        + **Communicational cohesion(****通信内聚****)**: Two modules forms a communication chain, where each operates on information and/or contributes to some output.

        + **Procedural cohesion(****程序性内聚****)**: Two modules must execute code in a particular order.

        + **Temporal cohesion(****时间内聚****)**: Modules are related based on timing dependencies.

        + **Logical cohesion(****逻辑内聚****)**: The data within modules is related logically but not functionally.

        + **Coincidental cohesion(****巧合内聚****)**: Elements in a module are not related other than being in the same source file.

    + #### Coupling


      + *Afferent coupling *measures the number of incoming connections to a code artifact.

      + *Efferent coupling *measures the outgoing connections to other code artifacts.

    + ### Abstractness, Instability, and Distance from the Main Sequence


      + *Abstractness* is the ratio of abstract artifacts (interfaces or abstract classes) to concrete artifacts (nonabstract classes). It represents a measure of abstractness versus implementation.

      + *Instability *is defined as the ratio of efferent coupling to the sum of both efferent and afferent coupling, it determines the volatility of a code base.

      + *Distance from the main sequence* is a derived metric based on instability and abstractness.

` 
D = | A + I - 1|
`

        + In the equation above, A = abstractness, I = instability.

      + Note that both *abstractness *and *instability* are fractions whose results will always fall between 0 and 1. The distance metric imagines an ideal relationship between abstractness and instability.


![](/assets/fundamentals-of-software-architecture/fosa_0303.png)

      + In the figure below, the closer to the line, the better balanced the class. Classes that fall too far into the upper righthand corner enter into what architects call the ***zone of useless***: code that is too abstract becomes difficult to use. Conversely, code that falls into the lower-left hand corner enter the ***zone of pain***: code with too much implementation and not enough abstractness becomes brittle and hard to maintain.


![](/assets/fundamentals-of-software-architecture/fosa_0304.png)

    + #### Connascence


      + {{< logseq/orgQUOTE >}}Two components are connascent if a change in one would require the other to be modified in order to maintain the overall correctness of the system.
--- Meilir Page-Jones
{{< / logseq/orgQUOTE >}}

      + **Static connascence** refers to source-code-level coupling. In the opposite, the **dynamic connascence** refers to executing-time coupling.

## Chapter 4. Architecture Characteristics Defined


  + A software  solution consists of both ***business requirements*** and ***architectural characteristics.***

  + An architecture characteristic meets three criteria:

    + Specifies a nondomain design consideration

    + Influences some structural aspect of the design

    + Is critical or important to application success

  + Architecture characteristics could be *implicit* or *explicit.* Implicit ones rarely appear in requirements, yet they are necessary for project success. Explicit ones appear in requirements documents or other specific instructions.


![](/assets/fundamentals-of-software-architecture/fosa_0402.png)

    + 

  + ### Some Architecture Characteristics


    + Availability: How long the system will need to be available (if 24/7, steps need to be in place to allow the system to be up and running quickly in case of any failure).

    + Continuity: Disaster recovery capability.

    + Performance: Includes stress testing, peak analysis, analysis of the frequency of functions used, capacity required, and response times. Performance acceptance sometimes requires an exercise of its own, taking months to complete.

    + Recoverability: Business continuity requirements (e.g., in case of a disaster, how quickly is the system required to be on-line again?). This will affect the backup strategy and requirements for duplicated hardware.

    + Reliability/safety: Assess if the system needs to be fail-safe, or if it is mission critical in a way that affects lives. If it fails, will it cost the company large sums of money?

    + Robustness:  Ability to handle error and boundary conditions while running if the internet connection goes down or if there’s a power outage or hardware failure.

    + Scalability: Ability for the system to perform and operate as the number of users or requests increases.

  + ### Operational Architecture Characteristics


    + > Operational architecture characteristics heavily overlap with operations and DevOps concerns, forming the intersections of those concerns in many software projects.|

    + Configurability: Ability for the end users to easily change aspects of the software’s configuration (through usable interfaces).

    + Extensibility: How important it is to plug new pieces of functionality in.

    + Installability: Ease of system installation on all necessary platforms.

    + Leverageability/reuse: Ability to leverage common components across multiple products.

    + Localization:  Support for multiple languages on entry/query screens in data fields; on reports, multibyte character requirements and units of measure or currencies.

    + Maintainability:  How easy it is to apply changes and enhance the system?

    + Portability:  Does the system need to run on more than one platform? (For example, does the frontend
need to run against Oracle as well as SAP DB?

    + Supportability: What level of technical support is needed by the application? What level of logging and other facilities are required to debug errors in the system?

    + Upgradeability: Ability to easily/quickly upgrade from a previous version of this application/solution to a newer version on servers and clients.

    + 

    + Architects must be concern themselves with code structure.

  + ### Cross-Cutting Architecture Characters


    + Accessibility: Access to all your users, including those with disabilities like colorblindness or hearing loss.

    + Archivability: Will the data need to be archived or deleted after a period of time? (For example, customer accounts are to be deleted after three months or marked as obsolete and archived to a secondary database for future access.)

    + Authentication: Security requirements to ensure users are who they say they are.

    + Authorization: Security requirements to ensure users can access only certain functions within the application (by use case, subsystem, webpage, business rule, field level, etc.).

    + Legal: What legislative constraints is the system operating in (data protection, Sarbanes Oxley, GDPR, etc.)? What reservation rights does the company require? Any regulations regarding the way the application is to be built or deployed?

    + Privacy: Ability to hide transactions from internal company employees (encrypted transactions so even DBAs and network architects cannot see them).

    + Security: Does the data need to be encrypted in the database? Encrypted for network communication between internal systems? What type of authentication needs to be in place for remote user access?

    + Supportability: What level of technical support is needed by the application? What level of logging and other facilities are required to debug errors in the system?

    + Usability/achievability: Level of training required for users to achieve their goals with the application/solution. Usability requirements need to be treated as seriously as any other architectural issue.

    + 

    + Any list of architecture characteristics will necessarily be an incomplete list; any software may invent their own import characteristics. In addition, many terms are imprecise and ambiguous, many definitions are overlapped.

  + ### Trade-offs and Least Worst Architecture


    + Applications can't support all architecture characteristics. First, each of the supported characteristics requires design and perhaps structural support. Second, the bigger problem lies with the fact that each architecture characteristics often has impact on others, the impact may be negative sometimes.

    + ***Never shoot for the best architecture, but rather the least worst architecture.***

    + Architects should strive to design architecture to be as **iterative** as possible.

## Chapter 5. Identifying Architectural Characteristics


  + Identifying the driving architecture characteristics is one of the first steps in creating an architecture or determining the validity of an existing architecture.

  + 要找出正确的架构特性，架构师不仅需要理解领域问题，还需要与领域利益相关者（domain stakeholders）紧密合作，找出那些它们关注的（重要的）特性。

  + ### Extracting Architecture Characteristics from Domain Concerns


    + An architect must be able to translate domain concerns to identify
the right architectural characteristics.

    + One tip when collaborating with domain stakeholders to define the driving architecture characteristics is to **work hard to keep the final list as short as possible**.

    + 常见的反模式——通用架构（Generic architecture: a architecture that supports all the architecture characteristics）.

    + Typically, architects and stakeholders speak two different language, so there're problems in communication. These problems must be solved.

  + ### Extracting Architecture Characteristics from Requirements


    + Some architecture characteristics comes from explicit statements in
requirements documents.

    + ***There are no wrong answers in architecture, only expensive ones.***

    + There is no best design in architecture, only a least worst collections of trade-offs.

## Chapter 6. Measuring and Governing Architecture Characteristics


  + Governance is an important responsibility of the architect role. The scope of architecture governance covers any aspect of the software development process that architect s want to exert an influence upon.

  + **Fitness function**: an object function used to assess how close the output comes to the achieving the aim.

  + **Architecture fitness function**: *any mechanism* that provides an objective integrity assessment of some architecture characteristics or combinations of architecture characteristics.

  + Architects must ensure that developers understand the purpose of fitness function before imposing it on them.

  + Chaos engineering offers an interesting new perspective on architecture: *it's not a question of if something will eventually break, but when*.

## Chapter 7. Scope of Architecture Characteristics


  + When evaluating many operational architecture characteristics, an architect must consider dependent components outside the code base that will impact those characteristics.

  + **Architecture quantum**: An independently deployable artifact that with high functional cohesion and synchronous connascence.

  + **Independent deployable**: an architecture quantum includes all the necessary components to function independently from other parts of the architecture.

  + **High functional cohesion**: cohesion in component design refers to how well the contained code is unified in purpose. High functional cohesion implies that an architecture quantum does something purposeful.

  + **Synchronous connascence**: this implies synchronous calls within an application context or between distributed services that form this architecture quantum.

## Chapter 8. Component-Based Thinking


  + Components offer a language-specific mechanism to group artifacts together, often nesting them to create stratification.

  + ### Architect Role


    + Typically, the architect defines, refines, manages, and governs components within an architecture.

    + An architect must identify components as one of the first tasks on a new project. But before an architect can identify components, they must know how to partition the architecture.

    + #### Architecture Partitioning


      + The First Law of Software Architecture states that everything in software is a trade-off, including how architects create components in an architecture.

      + Because components represent a general containership mechanism, an architect can build any type of partitioning they want, such as layered partitioning or modular partitioning.

      + ***Conway's Law***: Organizations which design systems … are constrained to produce designs which are copies of the communication structures of these organizations.

      + Organizing architecture based on technical capabilities like the layered monolith represents ***technical top-level partitioning***. Additionally, there's another architectural variation called ***domain partitioning***, it's inspired by DDD (Domain-Driven Design).

![](/assets/fundamentals-of-software-architecture/fosa_0804.png)

      + DDD is a modeling technique for decomposing complex software systems. In DDD, the architect identifies domains or workflows independent and decoupled from each other.

  + ### Developer Role


    + Developers typically take components, jointly designed with the architect role, and further subdivided them into classes, functions, or subcomponents.

    + Developers should never take components designed by architects as the last word; all software design benefits from iteration.

  + ### Component Identification Flow


    + Component identification works best as an **iterative** process, producing candidates and refinements through **feedback**.

    + The figure below describes a genetic architecture exposition cycle. Certain specialized domains may insert other steps in this process or change it altogether.

![](/assets/fundamentals-of-software-architecture/fosa_0808.png)

  + ### Component Design


    + Finding the proper granularity for components is one of an architect's most difficult tasks. Too fined-grained design leads to too much communication between components. Too coarse-grained design encourage high internal coupling, leads to difficulties in deployability and testability.

    + No accepted "correct" way exists to design components. Rather, a wide range of techniques exists, all with various trade-offs.

    + While there's no one true way to ascertain components, a common anti-pattern lurks: the *entity trap*. This anti-pattern arises when an architect incorrectly identifies the database relationships as workflows in the application, it indicates lack of thought about the actual workflows of the application.

    + The *actor/actions* approach is popular way that architects used to map requirements to components. In this approach, architects identify actors who perform activities with the application and the actions those actors may perform.

    + In *Event Storming*, a component discovery technique comes from DDD, the architect assumes the project will use messages and/or events to communicate between the various components. It can help the team find out which events may occur in the system.

    + The *workflow approach* models the components around workflows, identifies the key roles, determines the kinds of workflows these roles engage in, and builds components around the identified activities.

## Chapter 9. Foundations (architecture styles)


  + Architecture styles, sometimes called architecture patterns, describes a named relationship of components covering a variety of architecture characteristics.

  + ### Fundamental Patterns


    + #### Big Ball of Mud


      + Architects refer to the absence of any discernible architecture structure as ***Big Ball of Mud***.

      + In general, architects want to avoid this type of architecture at all costs. The lack of structure makes change increasing difficult. It also suffers from problems in deployment, testability, scalability and performance.

![](/assets/fundamentals-of-software-architecture/fosa_0901.png)

    + #### Client/Server

      + ***Client/server***, or ***two-tier*** separates technical functionality between frontend and backend. There're several variants of this architecture style, such as Desktop + database server, Browser + database server, or Three-tier.

  + ### Monolith Versus Distributed Architectures


    + Architecture styles can be classified into two main types: ***monolithic*** (single deployment unit of all code) and ***distributed*** (multiple deployment units connected through remote access protocols).

    + Typical monolithic architecture styles include:

      + Layered architecture

      + Pipeline architecture

      + Microkernel architecture

    + Some distributed architecture styles:

      + Service-based architecture

      + Event-driven architecture

      + Space-based architecture

      + Service-oriented architecture

      + Microservice architecture

    + The first issues facing all distributed architectures are described in the [fallacies of distributed computing](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing). A

fallacy (谬论) is something that is believed or assumed to be true but is not. All eight of the fallacies of distributed computing apply to distributed architectures today.

      + **Fallacy #1: The Network Is Reliable**

        + While networks have become more reliable over time, the fact of the matter is that network still remain generally unreliable. The more a system relies on the network, the potentially less reliable it becomes.

      + **Fallacy #2: Latency Is Zero**

        + Local in-memory method call is measured in nanoseconds, but remote access call, such as REST is measured in milliseconds. Latency in any distributed architecture is not zero, yet most architects ignore this fallacy, insisting they have fast networks.

        + When using any distributed architecture, architects must know the **average latency**. It's the only way of determining whether a distributed architecture is feasible, particularly in microservices. Knowing the average latency is important, but even more important is also knowing the **95th **and **99th** percentile. Usually the "long tail" latency will kill performance in  a distributed architecture.

      + **Fallacy #3: Bandwidth Is Infinite**

        + Bandwidth is usually not a concern in monolithic architecture, but when a systems are broken apart into smaller deployment units (services), these services significantly utilizes bandwidth, causing networks to slow down, thus impact latency (fallacy #2) and reliability (fallacy #1).*

        + Stamp coupling* occurs when modules share a composite data structure and use parts of it, often consumes significant amount of bandwidth. The best way to address this fallacy is to ensuring the minimal amount of data is passed between services.

      + **Fallacy #4: The Network Is Secure**

        + Even VPN, trusted networks and firewalls are used, security still becomes much more challenging in a distributed architecture.

      + **Fallacy #5: The Topology Never Changes**

        + The network topology includes routers, hubs, switches, firewalls, networks, and appliances used within the overall network. Architects assume that the topology is fixed and never changes. *Of course it changes, it changes all the time*.

      + **Fallacy #6: There Is Only One Administrator**

        + Architects all the time fall into this fallacy, assuming they only need to collaborate and communicate with on administrator. In fact, there're many network administrators.

      + **Fallacy #7: Transport Cost Is Zero**

        + Transport cost here refers to money associated with making a remote call.

      + **Fallacy #8: The Network Is Homogeneous**

        + Many architects and developers assume a network is homogeneous---made up by only one network hardware vendor. The truth is that most companies have multiple network hardware vendors in their infrastructure, if not more.

## Chapter 10. Layered Architecture Style


  + The *layered architecture*, also known as the *n-tiered* architecture style, is one of the most common architecture styles.

  + ### Topology


    + Components within the layered architecture style are organized into logical horizontal layers, with each layer performing a specific role within the application.

![](/assets/fundamentals-of-software-architecture/fosa_1002.png)

    + The layered architecture is a *technically partitioned* architecture.

  + ### Layers of Isolation


    + Each layer in the layered architecture style can be both *closed* or *open*.


![](/assets/fundamentals-of-software-architecture/fosa_1003.png)

![](/assets/fundamentals-of-software-architecture/fosa_1005.png)

    + The *layers of isolation* concept means that changes made in one layer of the architecture generally don't impact or affect components in other layers, providing the contracts between those layers remain unchanged. This philosophy also allows any layer in the architecture to be replace without impacting any other layer.

  + ### Other Considerations


    + One thing to watch out for with the layered architecture is the *architecture sinkhole *anti-pattern, which occurs when requests move from layer to layer as simple pass-through processing with no business logic performed within each layer.

  + ### Architecture Characteristics Ratings


![](/assets/fundamentals-of-software-architecture/fosa_1006.png)

## Chapter 11. Pipeline Architecture Style


  + *Pipeline* architecture, also known as *pipes and filters* architecture. As soon as developers and architects split functionality into discrete parts, this pattern followed.

  + ### Topology

    + The Topology of the pipeline architecture consists of pipes and filters.


![](/assets/fundamentals-of-software-architecture/fosa_1101.png)

    + #### Pipes

      + **Pipes** in this architecture form the communication channel between filters. Each pipe is typically unidirectional and point-to-point (rather than broadcast) for performance reasons.

    + #### Filters

      + **Filters** are self-contained, independent from other filters, and generally stateless. Filters should only perform one task only. Four types of filters exists in this architecture style:

        + **Producer**: The starting point of a process, outbound only, sometimes called the *source*.

        + **Transformer**: Accepts input, optionally performs a transformation on some or all of the data, then forwards it to the outbound pipe. Functional advocates will recognize this feature as *map*.

        + **Tester**: Accepts input, tests one or more criteria, then optionally produces output, then forwards it to the outbound pipe. Functional advocates will recognize this as similar to *reduce*.

        + **Consumer**: The termination of the flow.

  + ### Architecture Characteristics Ratings

![](/assets/fundamentals-of-software-architecture/fosa_1103.png)

## Chapter 12. Microkernel Architecture Style


  + The *microkernel *architecture style, also referred to as the *plug-in* architecture is widely used today, it's a natural fit for product-based deployment.

  + ### Topology

    + The microkernel architecture is a relatively simple monolithic architecture consisting of two architecture components: a core system and plug-in components.

    + In microkernel architecture, application logic is divided between independent plug-in components and the basic core system, providing extensibility, adaptability, and isolation of application features and custom processing logic.

![](/assets/fundamentals-of-software-architecture/fosa_1201.png)

    + #### Core System

      + The core system is formally defined as the minimal functionality required to run the system.

      + Another definition of the core system is the happy path (general processing flow) through the application, with little or no custom processing.

    + #### Plug-In Components

      + Plug-in components are standalone, independent components that contain specialized processing, additional features, and custom code meant to enhance or extend the core system.

  + ### Registry

    + The core system needs to know about which plug-in modules are available and how to get to them. One common way is to implementing this is through a plug-in registry.

  + ### Contracts

    + The contracts between the plug-in components and the core system are usually standard across a domain of plug-in components and include behavior, input data, and output data returned from the plug-in component.

  + ### Architecture Characteristics Ratings

![](/assets/fundamentals-of-software-architecture/fosa_1208.png)

## Chapter 13. Service-Based Architecture Style


  + Service-based architecture is a hybrid of the microservices architecture and is considered one of the most pragmatic architecture styles, mostly due to its architectural flexibility.

  + ### Topology

    + The basic topology of service-based architecture follows a distributed macro layered structure consisting of a separately deployed user interface, separately deployed remote coarse-grained services, and a monolithic database.


![](/assets/fundamentals-of-software-architecture/fosa_1301.png)

    + Services within this architecture style are typically coarse-grained "portions of an application" (usually called *domain services*) that are independent and separately deployed.

    + One important aspect of service-based architecture is that it typically uses a centrally shared database.

  + ### Topology Variants

    + Many topology variants exist within the service-based architecture style, making this perhaps one of the most flexible architecture styles.

    + The user interface may be broken into different parts:

![](/assets/fundamentals-of-software-architecture/fosa_1302.png)

    + Similarly, opportunities may exist to break apart a single monolithic database into separate databases:

![](/assets/fundamentals-of-software-architecture/fosa_1303.png)

  + ### Service Design and Granularity


    + Because domain services in a service-based architecture are generally coarse-grained, each domain service is typically designed using a layered architecture style consisting of an API façade layer, a business layer, and a persistence layer. Another popular design approach is to domain partition each domain service using sub-domains.

![](/assets/fundamentals-of-software-architecture/fosa_1305.png)

    + Regardless of the service design, a domain service must contain some sort of API access façade, the API access façade typically takes on the responsibility of **orchestrating** the business request from the user interface.

    + Because domain services are coarse-grained, regular ACID database transactions are used to ensure database integrity within a single domain service.

  + ### Database Partitioning


    + Although not required, services within a service-based architecture usually share a single, monolithic database due to the small number of services within a given application context. This database coupling can present an issue with respect to database table schema changes.

    + Within a service-based architecture, the shared class files representing the database table schemas reside in a custom shared library used by all the domain services.


![](/assets/fundamentals-of-software-architecture/fosa_1306.png)

    + Any change to the database table structures would also require a change to the single shared library, thus requiring a change and redeployment to every service. One way to mitigate the impact and risk of database changes is to logically partition the database and manifest the logical partitioning through federated shared libraries.

![](/assets/fundamentals-of-software-architecture/fosa_1307.png)

    + *Make the logical partitioning in the database as fine-grained as possible while still maintaining well-defined data domains to better control database changes within a service-based architecture.*

  + ### Architecture Characteristics Ratings

![](/assets/fundamentals-of-software-architecture/fosa_1309.png)

## Chapter 14. Event-Driven Architecture Style


  + The *event-driven* architecture style is a popular asynchronous architecture style used to produce highly scalable and high-performance applications. It's also highly adaptable. Event-driven architecture is made up of decoupled event processing components that asynchronously receive and process events.

  + Event-based model reacts to a particular situation and takes action based on that event.

  + ### Topology


    + There are two primary topologies within event-driven architecture: the mediator topology and the broker topology. The ***mediator topology*** is commonly used when you require control over the workflow of an event process, whereas the ***broker topology ***is used when you require a high degree of responsiveness and dynamic control over the processing of an event.

  + ### Broker Topology


    + The broker topology differs from the mediator topology in that *there is no central event mediator*. It's useful when you have a relatively simple event processing flow and you do not need central event orchestration and coordination.

    + There are four primary architecture components within the broker topology: an initiating event, the event broker, an event processor, and a processing event.

    + The initiating event the is initial event that starts the entire flow, it's send to an event channel in the event broker for processing, a single event processor accepts the initiating event from the event broker and begins to processing of that event, then asynchronously advertises what it did to the rest of the system by creating what is called a processing event.

![](/assets/fundamentals-of-software-architecture/fosa_1402.png)

    + It is always a good practice within the broker topology for each event processor to advertise what it did to the rest of the system, regardless of whether or not any other event processor cares about what that action was. This practice provides architectural **extensibility** if additional functionality is required for the processing of that event.


![](/assets/fundamentals-of-software-architecture/fosa_1403.png)

    + The best way to understand the broker topology is to think it as a relay race (接力赛). Once an event processor hands off the event, it is no longer involved with the processing of that specific event and is available to react to other initiating or procesing event.

    + While performance, responsiveness, and scalability are all great

benefits of the broker topology, there are also some negatives about it:

      + There's no control over the overall workflow associated with the initiating event, it's very dynamic based on various conditions.

      + Because there's no mediator monitoring or controlling the business transaction, error handling is a big problem.

      + The ability to restart a business transaction (recoverability) is not supported.

      + No component in the broker topology is aware of the state or even owns the state of the original business request, and therefore no one is responsible for restarting the business transaction and knowing where it left off.

    + Here are some trade-offs of the broker topology:

  + ### Mediator Topology


    + The mediator topology addresses some of the shortcomings of the broker topology. Central to this topology is an event mediator, which manages and controls the workflow for initiating events that require the coordination of multiple event processors.

    + The architecture components that make up the mediator topology  are an initiating event, an event queue, an event mediator, event channels, and event processors.

![](/assets/fundamentals-of-software-architecture/fosa_1405.png)

    + The event mediator only knows the steps involved in processing the event and therefore generates corresponding processing event that are sent to dedicated event channels in a point-to-point messaging fashion.

    + It is important to know the type of the events that will processed through the mediator in order to make the correct choice for the implementation of the event coordinator.

    + Given that it's rare to have all events of one class of complexity, classifying events as simple, hard, or complex and having every event always go through a simple mediator is recommended.

![](/assets/fundamentals-of-software-architecture/fosa_1406.png)

    + Because the mediator controls the workflow, it can maintain event state and manage error handling, recoverability, and restart capabilities.

    + Another inherent difference between the broker and mediator topology is how the processing events differ in terms of their meaning and how they are used. In broker topology, processing occurrences are ***events ***(things that have already happened). However, processing occurrences are ***commands*** (things that need to happed). Also, in the mediator topology,  a command must be processed, whereas an event can be ignored in the broker topology.

    + Trade-offs of the mediator topology is listed below:

      + | Advantages | Disadvantages |
|-------------|-----------------|
| Workflow control | More coupling of event processors |
| Error handling | Lower scalability |
| Recoverability | Lower performance |
| Restart capabilities | Lower fault tolerance |
| Better data consistency | Modeling complex workflows |

    + The choice between the broker and mediator topology essentially comes down to a trade-off between workflow control and error handling capability versus high performance and scalability.

  + ### Asynchronous Capabilities


    + The event-driven architecture style offers a unique characteristic over other architecture styles in that it relies solely on asynchronous communication for both fire-and-forget processing (no response required) as well as request/reply processing (response required from the event consumer).

    + *When the user does not need any information back (other than an acknowledgement or a thank you message), don't make the user wait, use asynchronous instead.*

    + The main issue with asynchronous communications is error handling. While responsiveness is significantly improved, it is difficult to address error conditions, adding the complexity to the event-driven system.

  + ### Error Handling


    + The workflow event pattern of reactive architecture is one way of addressing the issues associated with error handling in an asynchronous workflow. It leverages delegation, containment, and repair through the use of a workflow delegate.

![](/assets/fundamentals-of-software-architecture/fosa_1414.png)

    + The event producer asynchronously passes data through a message channel to the event consumer. If the event consumer experiences an error while processing the data, it immediately delegates that error to the workflow processor and move on to the next message in the event queue. For example:

![](/assets/fundamentals-of-software-architecture/fosa_1415.png)

    + One of the consequences of the workflow event pattern is that messages in error are processed out of sequence when they are resubmitted.

  + ### Preventing Data Loss


    + Data loss is always a primary concern when dealing with asynchronous communications.

    + Unfortunately, there are many places for data loss to occur within an event-driven architecture.

![](/assets/fundamentals-of-software-architecture/fosa_1416.png)

    + Fortunately, there are basic out-of-the-box techniques that can be leveraged to prevent data loss when using asynchronous messaging.


      + Each of these areas of data loss can be mitigated through basic messaging techniques.

      + Issue 1 (the message never makes it to the queue) is easily solved by leveraging ***persistent message queues***, along with something called ***synchronous send***. Persisted message queues support what is known as *guaranteed delivery. *

      + Issue 2 (Event Processor B de-queues the next available message and crashes before it can process the event) can be solved by using a basic technique of messaging called ***client acknowledge mode***.

      + Issue 3 (Event Processor B is unable to persistent the message to the database due to some data error) is addressed through leveraging ACID transactions via database commit. Leveraging something called ***last participant support (LPS)*** removes the message form the persisted queue by acknowledging that processing has been completed and that the message has been persisted.

![](/assets/fundamentals-of-software-architecture/fosa_1417.png)

  + ### Request-Reply


    + In event-driven architecture, synchronous communication is accomplished through ***request-reply ***messaging. Each event channel within a request-reply messaging consists of two queues: a request queue and a reply queue. The initial request for information is asynchronously sent to the request queue, and then control is returned to the message producer. The message producer then does a blocking wait on the reply queue, waiting for the response. The message consumer receives and processes the message and then sends the response to the reply queue. The event producer then receives the message with the response data.

![](/assets/fundamentals-of-software-architecture/fosa_1419.png)

    + There are two primary techniques for implementing request-reply messaging. The first (and most common) technique is to use a ***correlation ID*** contained in the message header.

![](/assets/fundamentals-of-software-architecture/fosa_1420.png)

    + The other technique used to implement request-reply messaging is to use a ***temporary queue***
for the reply queue. A temporary queue is **dedicated** to the specific request, create when the request is made and deleted when the request ends.

![](/assets/fundamentals-of-software-architecture/fosa_1421.png)

  + ### Architecture Characteristics Ratings


![](/assets/fundamentals-of-software-architecture/fosa_1422.png)

## Chapter 15. Space-Based Architecture Style


  + In any high-volume application with a large concurrent user load, the database will usually be the final limiting factor in how many transactions you can process concurrently.
  + The *space-based *architecture style is specifically designed to addresses problems involving high scalability, elasticity, and high concurrency issues.

  + ### Generally Topology


    + Space-based architecture get its name from the concept of tuple space, the technique of using multiple parallel processors communicating through shared memory. High scalability, high elasticity, and high performance are achieved by removing the central database as a synchronous constraint in the system and instead leveraging** replicated in-memory data grids**. Application data is kept in-memory and replicated among all the active processing units. When a processing unit updates data, it asynchronously sends that data to the database, usually via messaging with persistent queues.

    + Processing units start up and shut down dynamically as user load increases and decreases, thereby addressing variable scalability. Because there is no central database involved in the standard transactional processing of the application, the database bottleneck is removed, thus providing nearly infinite scalability within the application.

    + There are several architecture components that make up a space-based architecture: a ***processing unit*** containing the application code, ***virtualized middleware*** used to manage and coordinate the processing units, ***data pumps*** to asynchronously send updated data to the database, ***data writers*** that perform the updates from the data pumps, and ***data readers*** that read database data and deliver it to processing units upon startup.

![](/assets/fundamentals-of-software-architecture/fosa_1502.png)

    + #### Processing unit

      + The processing unit contains the application logic, usually including web-based components as well as business logic.

    + #### Virtualized Middleware


      + The virtualized middleware handles the infrastructure concerns within the architecture that control various aspects of data synchronization and request handling. It includes a *messaging grid, data grid, processing grid, and deployment manager*.

      + ##### Messaging grid


        + The messaging grid managers input requests and session state, it determines a incoming request is handled by which active processing components.

![](/assets/fundamentals-of-software-architecture/fosa_1504.png)

      + ##### Data grid


        + The data grid component is perhaps the most important and crucial component in this architecture style. In most modern implementations the data grid is implemented solely within the processing units as a replicated cache.

![](/assets/fundamentals-of-software-architecture/fosa_1505.png)

        + Data is synchronized between processing units that contain the same named data grid. A processing unit can contain as many replicated caches as needed to complete its work.

        + Each processing unit knows about all other processing unit instances through the use of a ***member list***. The member list contains the IP addresses and ports of all other processing units using the same named cache.

      + ##### Processing grid


        + The processing grid is an optional component within the virtualized middleware that manages orchestrated request processing when there are multiple processing units involved in a single business request.

![](/assets/fundamentals-of-software-architecture/fosa_1506.png)

      + ##### Deployment manager

        + The deployment manager component manages the dynamic startup and shutdown of processing unit instances based on load conditions.

    + #### Data pumps


      + A data pump is a way of sending data to another processor which when updates data in a database. Data pumps within a space-based architecture are always asynchronous, providing eventual consistency with the in-memory cache and the database.

      + Data pumps are usually implemented using messaging.

![](/assets/fundamentals-of-software-architecture/fosa_1507.png)

    + #### Data Writers


      + The data writer component accepts messages from a data pump and updates the database with the information contained in the message of the data pump.

    + #### Data Readers


      + Data readers take the responsibility for reading data from the database and sending it to the processing units via a reverse data pump.

![](/assets/fundamentals-of-software-architecture/fosa_1510.png)

  + ### Data Collisions


    + When using replicated caching in an active state where updates can occur to any service instance containing the same named cache, there is the possibility of a ***data collision*** due to replication latency.

  + ### Near-Cache Considerations


    + A near-cache is a type of caching hybrid model bridging in-memory data grids with a distributed cache.

![](/assets/fundamentals-of-software-architecture/fosa_1514.png)

    + In this model, the distributed cache is referred to as the *full backing cache*, and each in-memory data grid contained within each processing unit is referred to as the *front cache*.

  + ### Architecture Characteristics Ratings


![](/assets/fundamentals-of-software-architecture/fosa_1515.png)

## Chapter 17. Microservices Architecture


  + Microservices is heavily inspired by the ideas in domain-driven design (DDD), a logical design process for software projects. One concept in particular from DDD, ***bounded context***, decidedly inspired microservices. The concept of bounded context represents a decoupling style.

  + In a traditional monolithic architecture, developers would try to building reusable classes and database schemas. While reuse is beneficial, remember the First Law of Software Architecture regarding trade-offs. The negative trade-off of reuse is coupling.

  + However, if the architect's goal requires high degrees of decoupling, then they favor duplication over reuse. The primary goal of microservices is ***high decoupling***, physically modeling the logical notion of bounded context.

  + ### Topology


    + The topology of microservices is shown below:

![](/assets/fundamentals-of-software-architecture/fosa_1701.png)

    + Architects expect each service to include all necessary parts to operate independently, including databases and other dependent components.

  + ### Distributed


    + Microservices forms a distributed architecture: each service runs in its own process. Decoupling the services to this degree allows for a simple solution to a common problem in architectures that heavily feature multitenant infrastructure for hosting applications.

    + Separating each service into its own process solves the problems brought on by sharing.

    + Performance is often the negative side effect of the distributed nature of microservices. Network calls take much longer than method calls, and security verification on every endpoints adds additional processing time, requiring architects to think carefully about he implications of granularity when design the system.

  + ### Bounded Context


    + The driving philosophy of microservices is the notion of *bounded context*: each service models a domain of workflow. Microservices take the concept of domain-partitioned architecture of the extreme, each service is meant to represent a domain of subdomain.

    + #### Granularity

      + Architects struggle to find the correct granularity for services in microservices.

      + {{< logseq/orgQUOTE >}}The term "microservice" is a label, not a description.
--- Martin Fowler
{{< / logseq/orgQUOTE >}}

      + However, many developers take the term "microservices" as a commandment, not a description, and create services that are too fine-grained.

      + The purpose of service  boundaries in microservices is to capture a domain of workflow. Here some guidelines architects can use to help find the appropriate boundaries:


        + **Purpose**: The most obvious boundary relies on the inspiration for the architecture style, a domain.

        + **Transactions**: Bounded contexts are business workflow, and often the entities that need to cooperate in a transaction show architects a good service boundary.

        + **Choreography**: If an architect builds a set of services that offer excellent domain isolation yet require extensive communication to function, the architect may consider bundling these services back into a larger service to avoid the communication overhead.

      + Iteration is the only way to ensure good service design.

    + ### Data Isolation

      + Another requirement of microservices, driven by the bounded context concept, is data isolation. Data isolation is another factor an architect must consider when looking at service granularity.

  + ### Operational Reuse


    + Microservices  prefers duplication to coupling. But there are some parts of architecture that really do benefit from coupling, such as monitoring, logging, and circuit breakers. The ***sidecar ***pattern offers a solution to this problem.

![](/assets/fundamentals-of-software-architecture/fosa_1702.png)

    + The common operational concerns appear within each service as a separate component, which can be owned by either individual teams or a shared infrastructure team. The sidecar component handles all the operational concerns that teams benefit from coupling together.

    + Once teams know that each service includes a common sidecar, they can build a ***service mesh***, allowing unified control across the architecture for concerns like logging and monitoring. The common sidecar components connect to form a consistent operational interface across all microservices:

![](/assets/fundamentals-of-software-architecture/fosa_1703.png)

    + The service mesh itself forms a console that allows developers holistic access to services, which is shown below:

![](/assets/fundamentals-of-software-architecture/fosa_1704.png)

  + ### Communication


    + In microservices, service granularity affects both data isolation and communication. Finding the correct communication help teams keep services decoupled yet still coordinated in useful ways.

    + Fundamentally, architects must decide on *synchronous* or *asynchronous *communication. Microservices architectures typically utilize ***protocol-aware heterogeneous interoperability***.

      + **Protocol-aware**: Because microservices usually don't include a centralized integration hub to avoid operational coupling, each service should know how to call other services.

      + **Heterogeneous**: Each service may be written in a different technology stack. *Heterogeneous* suggests that microservices fully supports polyglot environments, where different services use different platforms.

      + **Interoperability**: Describes services calling one another.

    + *Domain/architecture isomorphism* is one key characteristic that architects should look for when assessing how appropriate an architecture style is for a particular problem. This term describes how the shape of an architecture maps to a particular architecture style.

    + Architects aspire to extreme decoupling in microservices, but hen often encounter the problem of how to do transactional coordination across services.

    + Building transactions across services boundaries violates the core decoupling principle of the microservices architecture. The best advice for architects who want to do transactions across service is: ***don't! Fix the granularity components instead***. Transaction boundaries is one of the common indicators of service granularity.

    + A few transactions across services is sometimes necessary; if it's the dominant feature of the architecture, mistakes were made!

  + ### Architecture Characteristics Ratings

![](/assets/fundamentals-of-software-architecture/fosa_1713.png)

## Chapter 18. Choosing the Appropriate Architecture Style


  + It depends! Nothing is more contextual to a number of factors within an organization and what software it builds. Choosing an architecture style represents the culmination of analysis and thought about trade-offs for architecture characteristics, domain considerations, strategic goals, and a host of other things.

  + However contextual the decision is, some general advice exists around choosing an appropriate architecture style.

  + ### Shift "Fashion" in Architecture


    + Preferred architecture styles shift over time, driven by a number of factors:


      + ***Observations from the past***: New architecture styles generally arise from observations and pain points from the past experiences. Architects must rely on their past experience---it is that experience that allowed that person to become an architect in the first place.

      + ***Changes in the ecosystem***: Constant change is a reliable feature of the software development ecosystem---everything changes all the time. The change in our ecosystem is particularly chaotic, making even the type of change impossible to predict.

      + ***New capabilities***: When new capabilities arise, architecture may not merely replace one tool with
another but rather shift to an entirely new paradigm. The constant change in the ecosystem also delivers a new collection of tools and capabilities on a regular basis. Architects must keep a keen eye open to not only new tools but new paradigms.

      + ***Acceleration***: Not only does the ecosystem constantly change, but the rate of change also
continues to rise. Architects live in a constant state of flux because change is both pervasive and constant.

      + ***Domain changes***: The domain that developers write software for constantly shifts and changes, either because the business continues to evolve or because of factors like mergers with other companies.

      + ***Technology changes***: As technology continues to evolve, organizations try to keep up with at least some of these changes, especially those with obvious bottom-line benefits.

      + ***External factor***: Many external factors only peripherally associated with software development may drive change within an organizations.

    + Regardless of where an organization stands in terms of current
architecture fashion, an architect should understand current industry trends to
make intelligent decisions about when to follow and when to make exceptions.

  + ### Decision Criteria


    + When choosing an architectural style, an architect must take into account all the various factors that contribute to the structure for the domain design. Fundamentally, an architect designs two things: whatever domain has specified, and all the other structural elements required to make the system a success.

    + Because synchronous communication presents fewer design, implementation, and debugging challenges, architects should default to synchronous when possible and use asynchronous when necessary.

## Chapter 19. Architecture Decisions


  + A good architecture is one that help guide development teams in making the right technical choices. Making architecture decisions involves gathering enough relevant information, justifying the decision, documenting the decision, and effectively communicating that decision to the right stakeholders.

  + ### Architecture Decision Anti-Patterns


    + The programmer Andrew Koenig defines the anti-pattern as something that seems like a good idea when you begin, but leads you into trouble. Another definition of an anti-pattern is repeatable process that produces negative results.

    + The three major architecture anti-patterns that can (and usually do) emerge when making architecture decisions are the *Covering Your Assets *anti-pattern, the *Groundhog Day *anti-pattern, and the *Email-Driven Architecture *anti-pattern.

    + #### Covering Your Assets Anti-Pattern


      + This anti-pattern occurs when an architect avoids or defers making an architecture decision out of fear of making the wrong choice.

      + There are two ways to overcome this anti-pattern:

        + Wait until the *last responsible moment* to make an important architecture decision. The last responsible moment means waiting until you have enough information to justify and validate you decision, but not waiting so long that you hold up development teams or fail in to the *Analysis Paralysis *anti-pattern.

        + Continually collaborate with development teams to ensure that the decision you made can be implemented as expect.

    + #### Groundhog Day Anti-Pattern


      + One an architect overcomes the Covering Your Assets anti-pattern and starts making decisions ,a second anti-pattern emerges: the Groundhog Day anti-pattern. The Groundhog Day anti-patten occurs when people don't know why a decision was made, so it keeps getting discussed over and over and over.

      + The Groundhog Day anti-pattern occurs because one an architect makes an architecture decision, they fail to provide an justification for the decision (or a complete justification). When justifying architecture decisions it is important to provide both technical and business justification for your decision.

      + Four of the most common business justifications include cost, time to market, user satisfaction, and strategic positioning. When focusing on these common business justifications, it is important to take into consideration what is important to the business stakeholders.

    + #### Email-Driven Architecture Anti-Pattern


      + Once an architect making decisions and fully justifies those decisions, a third architecture anti-pattern emerges: *Email-Driven Architecture*. The Email-Driven anti-pattern is where people lose, forget, or don't even know an architecture decision has been made and therefore cannot possibly implement that architecture decision.

      + *Email is a great tool for communication, but is makes a poor document repository system*.

      + There many ways to increase the effectiveness of communicating architecture decisions, thereby avoiding the Email-Driven anti-pattern:

      + Not include the architecture decisions in the body of an email.

      + Only notify those people who really care about the architecture decision

  + ### Architecturally Significant


    + Michael Nygard coined the term *architecturally significant*. Architecturally significant decisions are those decisions that affect the structure, nonfunctional characteristics, dependencies, interfaces, or construction techniques.

    + The *structure* refers to decisions that impact the patterns or styles of architecture being used. The *nonfunctional characteristics* are the architecture characteristics ("-ilities") that are important for the
application or system being developed or maintained. *Dependencies* refer to coupling points between components and/or services within the system. *Interfaces* refer to how services and components are accessed and orchestrated. *Construction techniques* refer to decisions about platforms, frameworks, tools, and even processes that might impact some aspects of the architecture.

  + ### Architecture Decision Records

    + ***Architecture Decision Records (ADRs)*** is one of the most effective ways of documenting architecture decisions. An ADR consists a short text file (usually one to two pages long) describing a specific architecture decision.

    + #### Basic Structure

      + The basic structure of an ADR consists of five main sections: *Title*, *Status*, *Context*, *Decision*, and *Consequences*. *Compliance, Notes*, or even *Alternatives* may also be added.

![](/assets/fundamentals-of-software-architecture/fosa_1901.png)

      + {{< logseq/orgTIP >}}Understanding why a decision was made is far more important than understanding how something works.
{{< / logseq/orgTIP >}}

## Chapter 20. Analyzing Architecture Risk


  + Every architecture has risk associated with it. Analyzing architecture risk is one of the key activities of architecture. By continually analyzing risk, the architect can address deficiencies within the architecture and take corrective action to mitigate the risk.

  + ### Risk Matrix

    + The first issue that arises when assessing architecture risk is determining whether the risk should be classified as low, medium and high.

    + Architects can leverage risk matrix to help reduce the level of subjectiveness and qualify the risk associated with a particular area of the architecture.


![](/assets/fundamentals-of-software-architecture/fosa_2001.png)

      + The risk matrix uses two dimensions to qualify risk: the overall impact of the risk and the likelihood of that risk occurring. Each dimensions has low(1), medium(2) and high (3) rating. The numbers are multiplied together within each grid of the matrix, providing an objective numerical number representing that risk:

        + 1 and 2 are considered as low risk (green)

        + 3 and 4 are considered as medium risk (yellow)

        + 6 through 9 are considered as high risk (red)

      + When leveraging the risk matrix to qualify the risk, consider the impact dimension first and the likelihood dimension second.

  + ### Risk Assessments

    + The risk matrix can be used to build what is called a *risk assessment*. A risk assessment is a summarized report of the overall risk of an architecture with respect to some sort of contextual and meaningful assessment criteria.

    + Risk assessment can vary greatly, but in general they contain the risk (qualified from the risk matrix) of some *assessment criteria* based on services or domain areas of an application.

![](/assets/fundamentals-of-software-architecture/fosa_2002.png)

    + To show whether things are improving or getting worse, we could leverage an arrow along the risk rating number it is trending toward: red arrow for worse, green arrow for better.

![](/assets/fundamentals-of-software-architecture/fosa_2005.png)

  + ### Risk Storming


    + No architect can single-handedly determine the overall risk of a system. Risk storming is a collaborative exercise used to determine architectural risk within a specific dimension.

    + The risk storming effort involves both an individual part and a collaborative part. In the individual part, all participants individually assign risk to areas of the architecture using the risk matrix. In the collaborative part, all participants work together to gain consensus on risk areas, discuss risk, and form solutions for mitigating the risk.

    + Risk storming is broken down into three primary activities:

      + Identification

      + Consensus

      + Mitigation

    + #### Identification

      + The identification activity of risk storming involves each participant individually identifying areas of risk within the architecture.

      + *Whenever possible, restrict risk storming efforts to a single dimension. This allows participants to focus their attention to that specific dimension and avoids confusion about multiple risk areas being identified for the same area of the architecture.*

    + #### Consensus

      + The consensus activity is highly collaborative with the goal of gaining consensus among all participants regarding the risk within the architecture.

      + *For unproven or unknown technologies, always assign the highest risk rating (9) since the risk matrix cannot be used for this dimension.*

    + #### Mitigation

      + Once all participants agree on the qualification of the risk areas of the architecture, it's time for risk mitigation. Mitigating risk within an architecture usually involves changes or enhancements to certain areas of the architecture that otherwise might have been deemed perfect the way they were.

## Chapter 21. Diagramming and Presenting Architecture


  + Effective communication becomes critic to an architect's success. No matter how brilliant an architect's technical ideas, if they can't convince managers to fund them and developers to build them, their brilliance will never manifest.

  + Diagramming and presenting architects are two critical soft skills for architects.

  + When visually descripting an architecture, the creator often must show different views of the architecture. If the architect shows a portion without indicating where it lies within the overall architecture, it confuses viewers. ***Representational consistency*** is the practice of always showing the relationship between parts of an architecture, either in diagrams or presentations, before changing views.

  + ### Diagramming

    + The topology of architecture is always of interest to architects and developers because it captures how the structure fits together and forms a valuable shared understanding across the team.

  + ### Presenting

    + One of the most important skills an architect can learn in their presentation tool of choice is how to manipulate time.

    + When presenting, the speaker has two information channels: verbal and visual. By placing too much text on the slides and then saying the same
words, the presenter is overloading one information channel and starving the other. The better solution to this problem is to use incremental builds for slides, building up (hopefully graphical) information as needed rather than all at once.

    + A common mistake that presenters make is building the entire context of the presentation into the slides. Slides Are Half of the Story.

    + *Invisibility* is a simple pattern where the presenter inserts a blank black slide within a presentation to refocus attention solely on the speaker.

## Chapter 22. Making Teams Effective


  + Being able to make teams productive is one of the ways effective and successful software architects differentiate themselves from other software architects.

  + ### Team Boundaries


    + One of the roles of a software architect is to create and communicate the constraints, or the box in which developers can implement the architecture. Architects can create boundaries that are too tight, too loose, or just right.


![](/assets/fundamentals-of-software-architecture/fosa_2201.png)

  + ### Architect Personalities


    + There are three basic types of architect personalities: A *control freak architect*, an *armchair architect*, and an *effective *architect. Control freak architects produce tight boundaries, armchair architects produce loose boundaries, and effective architects produce just the right kinds of boundaries.

    + #### Control Freak

      + The control freak architect tries to control every detailed aspect of the software development process. Every decision a control freak architect makes is usually too fine-grained and too low-level, resulting too many constraints on the development team.

      + It's very easy to become a control freak architect. An architect's role is to create the building blocks of the application (the components) and
determine the interactions between those components. The developer's role in this effort is to then take those components and determine how they will be implemented using class diagrams and design patterns.

    + #### Armchair Architect

      + The armchair architect is the type of architect who hasn't coded in a very long time (if at all) and doesn't take the implementation details into
account when creating an architecture. They are typically disconnected from the development teams.

      + Armchair architects create loose boundaries around development teams. As a result, development teams end up taking on the role of architect,
essentially doing the work an architect is supposed to be doing. Team velocity and productivity suffer as a result, and teams get confused about how the system should work.

      + The biggest indicator that an architect might falling into the armchair architect personality is not having enough time to spend with the development teams (or choosing not to spend time with the development teams).

      + Development teams need an architect's support and guidance, and they need the architect available for answering technical and business-related questions when they are arise.

    + #### Effective Architect

      + An effective architect produces the appropriate constraints and boundaries on the team, ensuring that the team members are working well together, guided, or even has the appropriate tools.

  + ### How Much Control


    + Being an effective architect is knowing how much control to exert on a given development team. At least five factors should be considered:

      + **Team familiarity**. Generally, the better team members know each other, the less control is needed because team members start to become self-organizing.

      + **Team size**. The larger the team, the more control is needed.

      + **Overall experience**. Teams with lots of junior developers require more control and monitoring.

      + **Project complexity**. The more complicated the project, the more control is needed.

      + **Project duration**. The shorter the duration, the les control is needed.

  + ### Team Warning Signs


    + Team size is one of the factors that influence the amount of control an architect should exert on the development team. Three factors come into play when considering the most effective development team size:

      + **Process loss**. The basic idea of process loss is the more people you add to a project, the more time the project will take.

      + **Pluralistic ignorance**. Pluralistic ignorance is when everyone agrees to (but private rejects) a norm because they think they are missing some obvious.

      + **Diffusion of responsibility**. This factor is based on the fact that as team size increases, it has a negative impact on communication.

  + ### Leveraging Checklists


    + Checklists provide an excellent vehicle for making sure everything is covered and addressed.  However, software developers don't require checklists for everything. They key to making teams effective is knowing when to leverage checklists and when not to.

    + Processes that are good candidates for checklists are those that don't have any procedural order or dependent tasks, as well as those that tend to be error-prone or have steps that are frequently missed or skipped.

    + Don't worry about stating the obvious in a checklist. It's the obvious stuff that's usually skipped or missed.

    + ***Hawthorne effect: ****If people know they are being observed or monitored, their behavior changes, and generally will do the right thing.*

  + ### Providing Guidance

    + A software architect can also make teams effective by providing guidance through the use of design principles.

![](/assets/fundamentals-of-software-architecture/fosa_2213.png)

## Chapter 23. Negotiation and Leadership Skills


  + Negotiation and leadership skills are hard skills to obtain.

  + ### Negotiation and Facilitation

    + Negotiation is one of the most important skills a software architect can have. Effective architects understand the politics of the organization, have strong negotiation and facilitation skills, and can overcome disagreements when they occur to create solutions that all stakeholders agree on.

    + #### Negotiating with Business Stakeholders


      + Leverage the use of grammar and buzzwords to better understanding the situation.

      + Gather as much information as possible before entering into a negotiation.

      + When all else fails, state things in terms of cost and time. Money and time (effort involved) are certainly key factors in any negotiation but should be used as a last resort so that other justifications and rationalizations that matters more be tried first.

      + Always leverage the "divide and conquer" rule to qualify demands or requirements.

    + #### Negotiating with Other Architects


      + Always remember that *demonstration defeats discussion*.

      + Avoid being too argumentative or letting things get too personal in a negotiation---calm leadership combined with clear and concise reasoning will always win a negotiation.

    + #### Negotiating with Developers


      + Ivory tower architects are ones who simply dictate from on high, telling development teams what to do without regard to their opinion or concerns.

      + When convincing developers to adopt an architecture decision or to do a specific task, provide justification rather than "dictating from on high".

      + Providing the justification or reason first is always a good approach.

      + If a developer disagrees with a decision, have them arrive at the solution on their own.

  + ### The Software Architect as a Leader

    + A software architect is also a leader, one who can guide a development team through the implementation of the architecture.

    + #### The 4C's of Architecture


      + Complexity exists within architecture as well as software development, and always will.

      + Essential complexity, in other words, "we have a hard problem".

      + Accidental complexity, in other words, "we have made a problem hard".

      + An effective way of avoiding accidental complexity is what called the 4C's architecture:


![](/assets/fundamentals-of-software-architecture/fosa_2302.png)

    + #### Be Pragmatic, Yet Visionary


      + An effective software architect must be pragmatic, yet visionary. To be pragmatic is to deal with things sensibly and realistically in a way that is based on practical rather than theoretical considerations. To be visionary is to think about or plan the future with imagination or wisdom.

      + A good software architect is one that strives to find an appropriate balance between being pragmatic whiling still applying imagination and wisdom to solving problems.

    + #### Leading Teams by Example

      + Bad software architects leverages their title to get people to do what they want them to do. Effective architects get people to do things by not leveraging their title as architect, but rather by leading through example, not by title. ***This is all about gaining the respect of others***.

## Chapter 24. Developing a Career Path


  + An architect must continue to learn throughout their career. The technology world changes at a dizzying pace.

  + Developers and architects alike struggle with the balance of working a regular job, spending time with the family, being available for our children, carving out personal time for interests and hobbies, and trying to develop careers, while at the same time trying to keep up with the latest trends and

buzzwords. One technique to maintain this balance is the *20-minute rule*: Devote at least 20 minutes a day to your career as an architect by learning something new or diving deeper into a specific topic.

![](/assets/fundamentals-of-software-architecture/fosa_2401.png)

  + Practice is the proven way to build skills and become better at anything in life…including architecture.

  + Always learn, always practice, and go do some architecture.
