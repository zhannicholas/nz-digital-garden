---
tags:
- Microservice
title: Mastering API Architecture
categories:
date: 2022-08-30
lastMod: 2022-09-02
---


This is my reading notes of [Mastering API Architecture (oreilly.com)](https://learning.oreilly.com/library/view/mastering-api-architecture/9781492090625/).

# Introduction


  + Broadly speaking, APIs can be broken into two general categories depending on whether the API invocation is in process or out of process.

    + The process being referred to here is an operating system (OS) process.

    + *In process* means the call is handled by the same process from which the call was made, such as a Java method invocation from one class to another.

    + *Out of process* means the call is handled by additional external process other than the process from which the call was made. Typically, an out of process API call will involve data traversing a network.

    + This book is focus on **out of process** API calls.

  + Generally speaking, **north-south traffic** is traffic that is ingressing from an external location into your system. **East-west traffic** is transiting internally from system-to-system or service-to-service.

# Chapter 1. Design, Build and Specify APIs


  + This chapter covers how to design, build and specify APIs and the different circumstances under which you may choose REST or gRPC. **It is important to remember that it is not REST vs gRPC, but rather given the situations what is the most appropriate choice for modelling the exchange**.
    + The barrier to building REST and RPC based APIs is low in most technologies, carefully considering the design and structure is an important architectural decision.

    + When choosing between REST and RPC models, consider the Richardson Maturity model and the degree of coupling between the producer and consumer.

    + REST is a fairly loose standard. When building APIs, conforming to an agreed API Standard ensures your APIs are consistent and have the expected behavior for your consumers. API Standards can also help to short circuit potential design decisions that could lead to an incompatible API.

    + OpenAPI specifications are a useful way of sharing API structure and automating many coding related activities. You should actively select OpenAPI features and choose what tooling or generation features will be applied to projects.

    + Versioning is an important topic that adds complexity for the producer but is necessary to ease API usage for the consumer. Not planning for versioning in APIs exposed to consumers is dangerous. Versioning should be an active decision in the product feature set and a mechanism to convey versioning to consumers should be part of the discussion.

    + gRPC performs incredibly well in high bandwidth exchanges and is an ideal option for east → west exchanges. Tooling for gRPC is powerful and provides another option when modelling exchanges.

    + Modelling multiple specifications starts to become quite tricky, especially when generating from one type of specification to another. Versioning complicates matters further but is an important factor to avoid breaking changes. Teams should think carefully before combining RPC representations with RESTful API representations, as there are fundamental differences in terms of usage and control over the consumer code.

# Chapter 2. Testing APIs

  + ## Testing Strategies

    + We need to be smart about deciding upon the coverage and proportions of the types of tests you should be using.

      + Avoid creating irrelevant tests, duplicating tests, and any tests that are going to take more time and resources than the value they provide

    + ### Testing Quadrant

      + The testing quadrant brings together tests that help technology and business stakeholders alike, each perspective will have different opinion on priorities.

      + **Agile Testing Quadrants**

![](https://lisacrispin.com/wp-content/uploads/2011/11/agile-testing-quadrants.png)

        + Read [Using the Agile Testing Quadrants]([Using the Agile Testing Quadrants - Agile Testing with Lisa Crispin](https://lisacrispin.com/2011/11/08/using-the-agile-testing-quadrants/)) from Lisa Crispin.

        + The four quadrants can be generally described as follows:

          + Q1 Unit and Component tests for technology, these should verify that **the service that has been created works** and this verification should be performed using automated testing.

          + Q2 Tests with the business are to ensure **what is being built is serving a purpose**, this is verified with automated testing and can also include manual testing.

          + Q3 Testing for the business, this is about ensuring that **functional requirements are met** and also to includes exploratory testing.

          + Q4 Ensuring that **what exists works from a technical standpoint**. From Q1 you know that what has been built works, however, when the product is being used, is it performing as expected. Examples of performing correctly from a technical standpoint could include, security enforcement, SLA integrity, autoscaling.

        + Each side of the quadrant has different purpose:

          + The left side of quadrant (Q1, Q2) is all about supporting the product it helps guide the product and prevent defects.

          + The right side (Q3, Q4) is about critiquing the product and finding defects.

          + The top of the pyramid (Q2,Q3) is the external quality of your product, making sure that it meets our users expectations, this is what the business find important.

          + The bottom of the pyramid (Q1, Q4) are the technology facing tests to maintain the internal quality of our application.

    + ### Testing Pyramid

      + The test pyramid, also known as the test automation pyramid, can be used as part of your strategy for test automation.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492090625/files/assets/maar_0203.png)

      + The test automation pyramid shows the tradeoffs that exist in terms of confidence, isolation and scope.

        + Testing small parts of the codebase gives us better isolation and faster tests, but less confidence that the whole application is working.

        + Testing the entire application gives use more confidence that the application is working. However, the scope of test will be large, it also makes it more difficult to maintain and slow.

      + This pyramid illustrates a notion of how much time should be spent on a given test area, its corresponding difficulty to maintain, and the value it provides in terms of additional confidence.

        + Unit tests, are at the bottom of the pyramid, they form the foundation of your testing. They test small, isolated units of your code to ensure that your defined unit is running as expected.

        + Service tests make up the middle tier, they will provide you with more confidence that your API is working correctly than unit tests. But a larger scope and less isolation comes with more expense.

        + UI tests sit at the top of the testing pyramid, it covers the same ground of a request flowing from a start to an end point.

  + ## Contract Testing

    + Contract testing has two entities, a consumer and a producer.

      + A consumer requests data from an API (e.g. web client, terminal shell)

      + and a producer (also known as a provider) responds to the API requests, i.e. it is producing data, such as a RESTful Web Service.

    + A contract is a definition of an **interaction** between the consumer and producer. It is a statement to say if a consumer makes a request that matches the contract request definition, then the producer will return a response that matches the contract response definition.

    + {{< logseq/orgTIP >}}A key benefit of using contracts is that once a contract is agreed to be implemented by the producer, then this decouples the dependency of building the consumer and producer.
{{< / logseq/orgTIP >}}

    + By having a written definition of an interaction, it is possible to generate a tests. The contract defines what a request and response should look like and these can be used to verify the producer (the API) is fulfilling the contract.

    + As the contract has the response definition it is also possible to generate a stub server. This stub server can be used by consumers to verify that they can call the producer correctly and parse the response from the producer.

      + {{< logseq/orgTIP >}}A stub server is a service that can be run locally and will return pre-canned responses.
{{< / logseq/orgTIP >}}

    + ### Contract Methodologies

      + **Producer Contracts**

        + Producer contract testing is when a producer defines its own contracts. This practice is used when there is when your API is being used outside your immediate organization.

        + {{< logseq/orgCAUTION >}}When developing an API for an external audience it needs to maintain its integrity as the interface can not make breaking changes without a migration plan.
{{< / logseq/orgCAUTION >}}

        + Producer driven contracts is ideal to build an **external** API.

      + **Consumer Driven Contracts (CDC)**

        + Consumer Driven Contracts are implemented by a consumer driving the functionality that they wish to see in an interaction. Consumers submit contracts, or changes to a contract, to the producer for new or additional API functionality.

        + CDC is ideal to build an **internal** API.

  + ## API Component Testing

    + Component testing can be used to validate that multiple units work together and should be used to validate behavior.

    + **Component tests should not call out to external dependencies**, like contract testing, you are not using these tests to verify external integration points.

  + ## API Integration Testing

    + Integration tests are tests across boundaries between the module being developed and any external dependencies.

    + When performing integration testing, you want to confirm that the communication across the boundary is correct, i.e. your service can correctly communicate with another service that is external to it.

  + ## End-to-End Testing

    + The essence of end-to-end testing is to test services and their dependencies together to verify it works as expected.

    + Using end-to-end tests replicate core user journeys to help validate your APIs all integrate correctly.

# Chapter 3. API Gateways: Ingress Traffic Management


  + >Expose your APIs and manage associated ingress traffic from end users and other external systems in a reliable, observable, and secure way using an API gateway.

  + An API gateway is a critical part of any modern technology stack, a *management tool* that sits at the network “edge” of systems between a consumer and a collection of backend services, and acts as a *single point of entry for a defined group of APIs*.

  + An API gateway is implemented with two high-level fundamental components, a **control plane** and **data plane**.

    + The control plane is where operators interact with the gateway and define routes, policies, and required telemetry.

    + The data plane is the location where all of the work specified in the control plane occurs; where the network packets are routed, the policies enforced, and telemetry emitted.

  + Proxy, forward proxy, and reverse proxy

    + A proxy server, sometimes referred to as a forward proxy, is an intermediary server that forwards requests for content from multiple clients to different servers across the Internet. **A forward proxy is used to protect clients.**

      + [[draws/2022-08-30-16-20-51.excalidraw]]

    + A reverse proxy server, on the other hand, is a type of proxy server that typically sits behind the firewall in a private network and routes client requests to the appropriate backend server. **A reverse proxy is designed to protect servers**.

      + [[draws/2022-08-30-16-50-33.excalidraw]]

  + **Why use an API gateway?**

    + Reduce Coupling

      + Adapter / Facade Between Frontends and Backends

    + Simplify Consumption

      + Aggregating / Translating Backend Services

    + Protect APIs from Overuse and Abuse

      + Threat Detection and Mitigation

    + Understand How APIs Are Being Consumed

      + Observability

    + Manage APIs as Products

      + API Lifecycle Management

    + Monetize APIs

      + Account Management, Billing, and Payment

  + API Gateway as a single Point of Failure

    + In a typical web-based system, the first obvious single point of failure is typically **DNS**.

    + The next single points of failure will typically then be the **global and regional layer 4 load balancers**, and depending on the deployment location and configuration, the security edge components, such as the firewall or WAF.

    + After these core edge components, the next layer is typically the **API gateway**.

# Chapter 4. Service Mesh: Service-to-Service Traffic Management


  + > Manage traffic for internal APIs, i.e. service-to-service communication.

  + ## Service Mesh


    + Fundamentally, “service mesh” is a pattern for managing all service-to-service (or application-to-application) communication within a distributed software system.

    + **Service mesh vs API gateway**

      + There is a lot of overlap between the service mesh and API gateway patterns, with the primary differences being twofold.

        + First, service mesh implementations are optimized to handle service-to-service, or east-west, traffic within a cluster or data center.

        + Second, following from this, the originator of the communication is typically a (somewhat) known internal service, rather than a user’s device or a system running external to your applications.

      + Unlike an API gateway, the mapping from a service mesh data plane to a service is typically one-to-one, meaning that a service mesh proxy does not aggregate calls across multiple services.

    + A service mesh is implemented with two high-level fundamental components, a **control plane** and a **data plane**.

      + {{< logseq/orgNOTE >}}In a service mesh these components are always deployed **separately**.  
{{< / logseq/orgNOTE >}}

      + The control plane is where operators interact with the service mesh and define routes, policies, and required telemetry.

      + The data plane is the location where all of the work specified in the control plane occurs; where the network packets are routed, the policies enforced, and telemetry emitted.

    + Differences between ingress (north-south) and service-to-service(east-west) traffic properties

      + | | Ingress (n/s) | Service-to-service (e/w) |
| ---- | ---- | ---- |
| Traffic source | External (user, third-party, Internet) | Internal (within trust boundary) |
| Traffic destination | Public or business-facing API, or web site | Service or domain API |
| Authentication | “user” (real world entity) focused | “service” (machine entity) and “user” (real world entity) focused |
| Authorization | “user” roles or capability level | “service” identity or network segment focused, and “user” roles or capability level |
| TLS | One-way, often enforced (e.g. protocol upgrade) | Mutual, can be made mandatory (strict mTLS) |
| Primary implementations | API gateway, reverse proxy | Service mesh, application libraries |
| Primary owner | Gateway/networking/ops team | Platform/cluster/ops team |
| Organizational users | Architects, API managers, developers | developer |

    + Evolution of service mesh

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492090625/files/assets/maar_0405.png)

# Chapter 5. Deploying and Releasing APIs


  + ## Separating Deployment and Release

    + Thoughtworks Technology Radar has a great explanation of the difference between deployment and release.

      + {{< logseq/orgQUOTE >}}Implementing Continuous Delivery continues to be a challenge for many organizations, and it remains important to highlight useful techniques such as decoupling deployment from release. We recommend strictly using the term Deployment when referring to the act of deploying a change to application components or infrastructure. The term Release should be used when a feature change is released to end users, with a business impact. Using techniques such as feature toggles and dark launches, we can deploy changes to production systems more frequently without releasing features. More-frequent deployments reduce the risk associated with change, while business stakeholders retain control over when features are released to end users.
Thoughtworks Technology Radar 2016
{{< / logseq/orgQUOTE >}}

    + In a word, Release means bringing live traffic over to our new deployment.

    + Feature flags are typically hosted in a configuration store outside of the running application and allows code to be deployed with the feature off.

  + ## Release Strategies

    + It is important to choose a release strategy that allows you to reduce risk in production, perform a test or experiment and verify before completely release the change.

    + ### Canary Release

      + A canary releases introduces a new version of the software and flow a small percentage of the traffic to the canary.

    + ### Traffic Mirroring

      + We can also use traffic mirroring to copy or duplicate traffic and send this to an additional location or series of locations.

        + {{< logseq/orgNOTE >}}Frequently with traffic mirroring, the results of the duplicated requests are not returned to the calling service or end user. Instead the responses are evaluated out-of-band for correctness.  
{{< / logseq/orgNOTE >}}

    + ### Blue-Green

      + Blue-Green is usually implemented at a point in the architecture which uses a router, gateway or load balancer, behind which sits a complete blue environment and a green environment.

        + The current blue environment represents the current live environment and the green environment represents the next version of the stack.

        + The green environment is checked prior to switching live traffic, and at go live the traffic is flipped over from blue to green. The blue environment is now “off”, but if a problem is spotted it is a quick rollback. The next change would go from green to blue, oscillating from first release onward.

      + 

  + 

  + 

# Chapter 6. Operational Security: Threat Modeling for APIs


  + [OWASP (Open Web Application Security Project)](https://www.owasp.org) is a nonprofit foundation that works to improve the security of software.

  + ## Threat Modeling


    + > Threat modeling is a “technique you can use to help you identify threats, attacks, vulnerabilities, and countermeasures that could affect your application”

    + You don’t need to be a security expert to conduct threat modeling, and a key skill is “thinking like an attacker or a bad actor”.

    + The process of threat modeling includes:

      + Identify your objectives - Create a list of the business and security objectives. Keep them simple e.g. Avoid unauthorized access.

      + Gather the right information - Generate a high-level design of the system and ensure you have the right information.

      + Decompose the system - Break down your high-level design so that you can start to model the threats.

      + Identify threats - Systematically look for threats to your systems.

        + [STRIDE](https://shostack.org/files/microsoft/The-Threats-To-Our-Products.docx) is a methodology to identify threats.

      + Evaluate the risk of the threats - Prioritize threats to focus on the most likely ones, then identify mitigations to these likely threats.

      + Validate - Ask yourself and your team if the changes in place have been successful. Should you perform another review?

    + **STRIDE** --- a methodology to identify threats

      + **S**poofing（欺骗/仿冒）: Breaching the user’s authentication information.

        + In this case, the hacker has obtained the user’s personal information or something that enables him to replay the authentication procedure.

      + **T**ampering（篡改）: Modifying system or user data with or without detection.

        + There are two primary ways that tampering occurs, through payload injection and mass assignment.

          + Payload injection occurs when a bad actor attempts to inject a malicious payload into the request made to an API or application. SQL Injection is a typical example.

          + Mass Assignment is typical where client input data is bound to internal objects without thought of the repercussions, which is often a consequence when exposing a database API as an web-based API. （例如 API 反映出了数据库设计）

      + **R**epudiation（否认）: An untrusted user performing an illegal operation without the ability to be traced. Repudiability threats are associated with users (malicious or otherwise) who can deny a wrongdoing without any way to prove otherwise.

      + **I**nformation disclosure（信息暴露，privacy breach (侵犯隐私)）: Compromising the user’s private or business-critical information. Information disclosure threats expose information to individuals who are not supposed to see it.

        + Two common antipatterns in this category of threat include excessive data exposure and improper assets management.

          + [Excessive data exposure](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa3-excessive-data-exposure.md) means data is exposed inappropriately and this should be prevented.

          + [Improper Asset Management](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa9-improper-assets-management.md) typically occurs as your systems evolves, and the organization loses track of which APIs (and which versions) are exposed, or which APIs were designed for internal consumption only.

      + **D**enial of Service（拒绝服务）: Making the system temporarily unavailable or unusable.

        + **Rate limiting**: limits the amount of requests that can be made to your API over a period of time.

          + {{< logseq/orgTIP >}}The use of rate limiting typically refers to rejecting traffic based on properties of individual requests (too many from a given user, client application, or location).
{{< / logseq/orgTIP >}}

          + Limiting strategy

            + Fixed window （固定窗口） - A fixed limit within a period, e.g. 2400 requests per day

            + Sliding window （滑动窗口） - A limit within the last period, e.g. 100 requests within the last hour

            + Token bucket （令牌桶）- A set amount of total requests are allowed (bucket of tokens) and each request takes a token when a request is made, the bucket is refilled periodically.

            + Leaky bucket （漏桶）- Like the Token bucket, however, the rate at which requests are processed is a fixed rate, this is the leak of the bucket.

        + **Load-shedding** refers to rejecting requests based on the overall state of the system (database at capacity, no more worker threads available).

      + **E**levation of privilege（特权提升）: An unprivileged user gains privileged access and thereby has sufficient access to completely compromise or destroy the entire system.

# Chapter 7. API Authentication and Authorization

  + OAuth2 is the defacto standard for securing APIs, often leverage JWT as part of the bearer header. JWT tokens are often encoded and signed to ensure they are tamper free.

  + ## Authentication


    + Authentication is the act of verifying an identity (who you are?).

    + MFA is useful to give higher levels of assurance that the user is who they say they are.

    + For machine-to-machine authentication credentials can be in the form of keys or certificates.

    + ### End User Authentication with Tokens

      + In token based authentication the user would enter their username and password which is exchanged for a token.

        + The token is sent in the REST request as part of the [Authentication Bearer header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication).

        + Though tokens have the advantage that long-lived credentials, such as passwords, are not going across the network for every request to access resources, they should have a limited lifetime.

    + ### System-to-System Authentication

      + In some situations an end user is not involved in the interaction and system-to-system communication is required.

        + One option would be to use an API key, it's very similar to password when using.

          + To use API Keys for Authentication you simply add the API key into a request header and send it to the endpoint.

          + The API key is associated with an application or project, so it is possible to identify the requester.

  + ## OAuth2


    + OAuth2, the replacement for [OAuth](https://oauth.net/core/1.0/), is a token based authorization framework.

    + OAuth2 allows a user to consent that a third-party application can access their data on their behalf.


      + The consent that the user gives is the authorization, they are allowing or denying the access.

      + {{< logseq/orgNOTE >}}OAuth2 removes a need for a user to hand over their credentials to the third party, this gives the user control over their data.
{{< / logseq/orgNOTE >}}

    + ### Different roles in [OAuth2 Specification](https://datatracker.ietf.org/doc/html/rfc6749#section-1.1):


      + **Resource Owner**: An entity capable of granting access to a protected resource. When the resource owner is a person, it is referred to as an end-user.

      + **Authorization Server**: The server issuing access tokens to the client after successfully authenticating the resource owner and obtaining authorization. The Authorization Server has two endpoints:

        + The Authorization Endpoint, is used when a *resource owner* needs to authorize access to protected resources.

        + The Token Endpoint, requests is made by a *client* to get an Access Token.

      + **Client**: An application making protected resource requests on behalf of the resource owner and with its authorization

      + **Resource Server**: The server hosting the protected resources, capable of accepting and responding to protected resource requests using access tokens.

    + ### JSON Web Tokens (JWT)


      + [JSON (JavaScript Object Notation) Web Tokens](https://datatracker.ietf.org/doc/html/rfc7519) are an RFC standardized token format that is the defacto standard token for OAuth2.

      + A JSON Web Token also known as a JWT (pronounced “jot”), consists of [claims](https://datatracker.ietf.org/doc/html/rfc7519#section-4) and these claims have associated values.

      + JWTs are structured and encoded using standards to ensure the token is unmodifiable and additionally can be encrypted.

      + #### Encoding and Verifying JWT


        + There are two popular encoding mechanisms for JWT

          + [JWS (JSON Web Signatures)](https://datatracker.ietf.org/doc/html/rfc7515) provides integrity to a JWT, the content’s of the token are visible to anyone that receives the token.

          + [JWE (JSON Web Encryption)](https://datatracker.ietf.org/doc/html/rfc7516) provides integrity but is also encrypted, this means that the content of the token can not be examined.

          + {{< logseq/orgTIP >}}It is commonplace that when the term JWT is used, the implied meaning is JWT using JWS and that the term Encrypted JWT is using JWE.
{{< / logseq/orgTIP >}}

    + ### Terminology and Mechanisms of OAuth2 Grants


      + OAuth2 is designed to be extensible. The [Abstract Protocol Flow](https://datatracker.ietf.org/doc/html/rfc6749#section-1.2):

`txt
	 +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+
`

      + The flow includes the following steps:


        + 1. The *Client* requests *Authorization* from the *Resource Owner*.
2. The *Resource Owner* will grant or deny the *Client* access to their resources.
3. The *Client* will ask for an *Access Token* from the *Authorization Server* for the Authorization it has been granted.
4. The *Authorization Server* will issue an *Access Token* if the *Client* has been Authorized by the *Resource Owner*.
5. The *Client* makes a request for the resource to the *Resource Server*, which in our case is the API. The request will send the *Access Token* as part of the request.
6. The *Resource Server* will return the Resource if the Access Token is valid.

    + ### Authorization Code Grant


      + The [authorization code grant](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1) type is used to obtain both access tokens and refresh tokens and is optimized for confidential clients.


`txt
	 +----------+
     | Resource |
     |   Owner  |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier      +---------------+
     |         -+----(A)-- & Redirection URI ---->|               |
     |  User-   |                                 | Authorization |
     |  Agent  -+----(B)-- User authenticates --->|     Server    |
     |          |                                 |               |
     |         -+----(C)-- Authorization Code ---<|               |
     +-|----|---+                                 +---------------+
       |    |                                         ^      v
      (A)  (C)                                        |      |
       |    |                                         |      |
       ^    v                                         |      |
     +---------+                                      |      |
     |         |>---(D)-- Authorization Code ---------'      |
     |  Client |          & Redirection URI                  |
     |         |                                             |
     |         |<---(E)----- Access Token -------------------'
     +---------+       (w/ Optional Refresh Token)

   Note: The lines illustrating steps (A), (B), and (C) are broken into
   two parts as they pass through the user-agent.
`

      + The flow above includes the following steps:


        + (A)  The client initiates the flow by directing the resource owner's
      user-agent to the authorization endpoint.  The client includes
      its client identifier, requested scope, local state, and a
      redirection URI to which the authorization server will send the
      user-agent back once access is granted (or denied).

        + (B)  The authorization server authenticates the resource owner (via
      the user-agent) and establishes whether the resource owner
      grants or denies the client's access request.

        + (C)  Assuming the resource owner grants access, the authorization
      server redirects the user-agent back to the client using the
      redirection URI provided earlier (in the request or during
      client registration).  The redirection URI includes an
      authorization code and any local state provided by the client
      earlier.

        + (D)  The client requests an access token from the authorization
      server's token endpoint by including the authorization code
      received in the previous step.  When making the request, the
      client authenticates with the authorization server.  The client
      includes the redirection URI used to obtain the authorization
      code for verification.

        + (E)  The authorization server authenticates the client, validates the
      authorization code, and ensures that the redirection URI
      received matches the URI used to redirect the client in
      step (C).  If valid, the authorization server responds back with
      an access token and, optionally, a refresh token.

      + #### Authorization Code Grant (+ PKCE)


        + PKCE stands for Proof Key for Code Exchange, and is used to mitigate interception attacks.

        + [Authorization Code Grant + PKCE](https://datatracker.ietf.org/doc/html/rfc7636#section-1.1) protocol flow:

`txt
                                           +-------------------+
                                                 |   Authz Server    |
       +--------+                                | +---------------+ |
       |        |--(A)- Authorization Request ---->|               | |
       |        |       + t(code_verifier), t_m  | | Authorization | |
       |        |                                | |    Endpoint   | |
       |        |<-(B)---- Authorization Code -----|               | |
       |        |                                | +---------------+ |
       | Client |                                |                   |
       |        |                                | +---------------+ |
       |        |--(C)-- Access Token Request ---->|               | |
       |        |          + code_verifier       | |    Token      | |
       |        |                                | |   Endpoint    | |
       |        |<-(D)------ Access Token ---------|               | |
       +--------+                                | +---------------+ |
                                                 +-------------------+
`

        + The flow above includes the following steps:

          + 1. The authorization request is made and the code_verifier is sent to the authorization server. In the Diagram  `t(code_verifier)`  is the transformation of the code_verifier to the code_challenge and  `t_m`  is the transformation method, as described previously this is a hash.
2. Like in the Authorization Code Grant an authorization code is returned.
3. The client requests the access token by sending the authorization Request which is authorization code and the code_verifier. No client secret is sent as this is a public client.
4. The Access Token is then issued to the client application.

    + ### Refresh Tokens


      + It is good practice to issue tokens that are short-lived, however asking a user to re-enter their username and password would soon become a jarring experience.

      + A refresh token is a long-lived token used by the client to request additional access tokens when the previous expires.

      + Refresh token [work flow](https://datatracker.ietf.org/doc/html/rfc6749#section-1.5)

`txt
  +--------+                                           +---------------+
  |        |--(A)------- Authorization Grant --------->|               |
  |        |                                           |               |
  |        |<-(B)----------- Access Token -------------|               |
  |        |               & Refresh Token             |               |
  |        |                                           |               |
  |        |                            +----------+   |               |
  |        |--(C)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(D)- Protected Resource --| Resource |   | Authorization |
  | Client |                            |  Server  |   |     Server    |
  |        |--(E)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(F)- Invalid Token Error -|          |   |               |
  |        |                            +----------+   |               |
  |        |                                           |               |
  |        |--(G)----------- Refresh Token ----------->|               |
  |        |                                           |               |
  |        |<-(H)----------- Access Token -------------|               |
  +--------+           & Optional Refresh Token        +---------------+
`

    + ### Client Credentials Grant


      + The [client credentials grant](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4) type MUST only be used by **confidential** clients.

      + Client credentials flow

`txt
     +---------+                                  +---------------+
     |         |                                  |               |
     |         |>--(A)- Client Authentication --->| Authorization |
     | Client  |                                  |     Server    |
     |         |<--(B)---- Access Token ---------<|               |
     |         |                                  |               |
     +---------+                                  +---------------+
`

      + There are no additional steps as there is no resource owner to give permission. The client is acting on its own behalf so only is required to identify itself.

    + ### OAuth2 Scopes

      + Scopes are important mechanism in OAuth2 and are effectively used to limit the access of a client acting on behalf of a user.

      + When you look at authorization with OAuth2, you must keep in mind that Scopes are used to specify what a resource owner has stated regarding the range of actions a client can perform.

  + ## OIDC

    + OAuth2 provides a mechanism for the client to access APIs using authentication and authorization. However, *OAuth2 grants do not provide a way to obtain (or verify) the identity of the end user.*

    + [Open ID Connect (OIDC)](https://openid.net/connect/) provides an identity layer on top of OAuth2. It allows Clients to verify the identity of the End-User based on the authentication performed by an Authorization Server, as well as to obtain basic profile information about the End-User in an interoperable and REST-like manner.

  + ## SAML 2.0

    + [SAML(Security Assertion Markup Language)](https://auth0.com/intro-to-iam/what-is-saml/) is an open standard that transfers assertions.

    + It is often used for single sign on, and the assertions that are transferred are user identities. In other words, *it allows a user to authenticate in a system and gain access to another system by providing proof of their authentication*.
