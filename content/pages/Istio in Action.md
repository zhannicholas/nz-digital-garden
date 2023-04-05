---
tags:
- Istio
- Service Mesh
title: Istio in Action
categories:
date: 2022-08-09
lastMod: 2023-03-28
---




Reading Notes of [Istio in Action](https://learning.oreilly.com/library/view/istio-in-action/9781617295829/), a book about how to achieve the goal of decoupling applications from infrastructure, on O'reilly.
[Christian Posta's blog](https://blog.christianposta.com/).

## Front Matter


  + What is a service mesh, and why do I need one?

    + The real answer has to do with decoupling applications from infrastructure. Istio is the third major step in that direction. First, Docker provided a way to **package an application** (and its library choices) separately from the machine on which it runs. Next, Kubernetes made it easy to create a service with automation to help with **autoscaling and management**. Together, Docker and Kubernetes enabled the practical movement to fine-grain services, often called _microservices_. This book guides you through implementing a service mesh with Istio to achieve this third step: application decoupling.

## Chapter 1 Introduction to the Istio service mesh


  + _Service mesh_ is a relatively recent term used to describe a decentralized application-networking infrastructure that allows applications to be secure, resilient, observable, and controllable. It describes an architecture made up of a **data plane that uses application-layer proxies to manage networking traffic** on behalf of an application and **a control plane to manage proxies**.

  + [Envoy](http://envoyproxy.io) is a service proxy that has emerged in the open source community as a versatile, performant, and capable application-layer proxy.

![The envoy proxy out-of-process from the application](/assets/istioinaction/ch01_f06_posta2.png)

![A sidecar deployment is an additional process that works cooperatively A sidecar deployment is an additional process that works cooperatively with the main application process to deliver a piece of functionality](/assets/istioinaction/ch01_f07_posta2.png)

  + A **service mesh** is a distributed application infrastructure that is responsible for handling network traffic on behalf of the application in a transparent, out-of-process manner.
![A service mesh architecture with co-located application-layer proxies (data plane) and management components (control plane)](/assets/istioinaction/ch01_f08_posta2.png)

    + The **data plane** is responsible for establishing, securing, and controlling the traffic through the mesh. The data plane behavior is configured by the control plane. The **control plane** is the brains of the mesh and exposes an API for operators to manipulate network behaviors.

  + With Istio, applications don’t have to know that they’re part of the service mesh: whenever they interact with the outside world, Istio handles the networking on their behalf.

  + Istio’s data plane uses the **Envoy proxy** and helps you configure your application to have an instance of the service proxy (Envoy) deployed alongside it. Istio’s control plane (`istiod`) is made up of a few components that provide APIs for end users/operators, configuration APIs for the proxies, security settings, policy declarations, and more.

  + With a service proxy next to each application instance, applications no longer need language-specific resilience libraries for circuit breaking, timeouts, retries, service discovery, load balancing, and so on.

  + An overview of separation of concerns in cloud-native applications.

![](/assets/istioinaction/ch01_f13_posta2.png)

  + The drawbacks of using service mesh

    + First, using a service mesh puts another piece of middleware, specifically a proxy, in the request path. This proxy can deliver a lot of value; but for those unfamiliar with the proxy, it can end up being a black box and make it harder to debug an application’s behavior.

    + Another drawback of using a service mesh is in terms of tenancy. A mesh is as valuable as the services running in the mesh. That is, the more services in the mesh, the more valuable the mesh becomes for operating those services.

    + Finally, a service mesh becomes a fundamentally important piece of your services and application architecture since it’s on the request path.

  + 

## Chapter 2 First steps with Istio


  + ### Istiod

    + Istio’s control-plane responsibilities are implemented in the `istiod`component. `istiod`, sometimes referred to as Istio Pilot, is responsible for taking higher-level Istio configurations specified by the user/operator and turning them into proxy-specific configurations for each data-plane service proxy.

![](/assets/istioinaction/ch02_f02_posta2.png)

    + Istio uses Envoy as its service proxy, so these configurations are translated to Envoy configurations.

    + Istio’s configuration resources are implemented as Kubernetes custom resource definitions (CRDs). In the case of Istio, we can use Istio’s custom resources (CRs) to add Istio functionality to a Kubernetes cluster and use native Kubernetes tools to apply, create, and delete the resources. Istio implements a controller that watches for these new CRs to be added and reacts to them accordingly.

  + ### Ingress and egress gateway

![](/assets/istioinaction/ch02_f04_posta2.png)

    + Ingress/egress gateways are really Envoy proxies that can understand Istio configurations.

## Chapter 3 Istio's data plane: The Envoy Proxy


  + Envoy can understand layer 7 protocols that an application may speak when communicating with other services.

  + ### Envoy's core features
    + > [Terminology in Envoy](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/intro/terminology#terminology)

    + **Downstream**: A downstream host connects to Envoy, sends requests, and receives responses.

    + **Upstream**: An upstream host receives connections and requests from Envoy and return responses.

    + **Listeners**: Expose a port to the outside world to which applications can connect. A listener in Envoy is a way to open a port on a networking interface and start listening to incoming traffic.
    + **Routes**: Routing rules for how to handle traffic that comes in on listeners.

    + **Clusters**: Specific upstream services to which Envoy can route traffic. **Subsets** are used to further divide workloads within a cluster, which enables fine-grained traffic management.
    + Traffic comes into a _listener_ from a _downstream system_. This traffic is routed to one of Envoy’s _clusters_, which is responsible for sending that traffic to an _upstream system_.

![](/assets/istioinaction/ch03_f03_posta2.png)
      + 

  + ### Configuring Envoy

    + #### Static configuration

      + We can specify listeners, route rules, and clusters using Envoy’s configuration file.
    + #### Dynamic configuration


      + Envoy uses its xDS APIs for dynamic configuration. xDS APIs include the following APIs:

        + Listener discovery service (LDS): An API that allows Envoy to query what listeners should be exposed on this proxy.

        + Route discovery service (RDS): Part of the configuration for listeners that specifies which routes to use. This is a subset of LDS for when static and dynamic configuration should be used.

        + Cluster discovery service (CDS): An API that allows Envoy to discover what clusters and respective configuration for each cluster this proxy should have.

        + Endpoint discovery service (EDS): Part of the configuration for clusters that specifies which endpoints to use for a specific cluster. This is a subset of CDS.

        + Secret discovery service (SDS): An API used to distribute certificates.

        + Aggregate discovery service (ADS): A serialized stream of all the changes to the rest of the APIs. You can use this single API to get all of the changes in order.

      + Envoy’s xDS APIs are built on a premise of eventual consistency and that correct configurations eventually converge

## Chapter 4: Istio gateways: Getting traffic into a cluster


  + ### Traffic ingress concepts


    + The networking community has a term for connecting networks via well-established entry points: **ingress points**. **Ingress** refers to traffic that originates outside the network and is intended for an endpoint within the network. The traffic is first routed to an ingress point that acts as a gatekeeper for traffic coming into the network. The ingress point enforces rules and policies about what traffic is allowed into the local network. If the ingress point allows the traffic, it proxies the traffic to the correct endpoint in the local network. If the traffic is not allowed, the ingress point rejects the traffic.

    + **Virtual IPs**: Simplifying service access


      + The virtual IP is bound to a type of ingress point known as a **reverse proxy**. The reverse proxy is an intermediary component that’s responsible for distributing requests to backend services and does not correspond to any specific service.

![](/assets/istioinaction/ch04_f03_posta2.png)

    + **Virtual hosting**: Multiple services from a single access point


      + In addition to virtual IP, we can also represent multiple different hostnames using a single virtual IP.

![](/assets/istioinaction/ch04_f04_posta2.png)

      + Hosting multiple different services at a single entry point is known as **virtual hosting**.

  + ### Istio ingress gateways

    + Istio has the concept of an **ingress gateway** that plays the role of the network ingress point and is responsible for guarding and controlling access to the cluster from traffic that originates outside of the cluster. Additionally, Istio’s ingress gateway handles load balancing and virtual-host routing.

    + Istio uses a single Envoy proxy as the ingress gateway.

![](/assets/istioinaction/ch04_f05_posta2.png)

    + Istio gateway: expose a specific port, expect a specific protocol on that port, and define specific hosts to serve from the port/protocol pair.

    + In Istio, a `VirtualService` resource defines how a client (inbound traffic) talks to a specific service through its fully qualified domain name. In other words, `VirtualService` allows us to route traffic from the ingress gateway to a specific service.

    + The `Gateway` resource defines ports, protocols, and virtual hosts that we wish to listen for at the edge of our service-mesh cluster. `VirtualService` resources define where traffic should go once it’s allowed in at the edge.

    + Istio `Gateway` handles the L4(transport) and L5(session) concerns, while `VirtualService` handles the L7(application) concerns.

  + ### Securing gateway traffic

    + Istio’s gateway implementation allows us to terminate incoming TLS/SSL traffic, pass it through to the backend services, redirect any non-TLS traffic to the proper TLS ports, and implement mutual TLS.

    + Basic model of how TLS is established between a client and server.

![](/assets/istioinaction/ch04_f08_posta2.png)

    + Basic model of how mTLS is established between a client and server
![](/assets/istioinaction/ch04_f10_posta2.png)

## Chapter 5: Traffic control: Fine-grained traffic routing


  + ### deployment vs release


    + Workloads can be separated into smaller subsets, such as version v1 and version v2, using `DestinationRules`. Then we can configure `VirtualService`s to use these subsets to route traffic in a fine-grained manner.

    + When we do a _deployment_ to production, we install the new code onto production resources (servers, containers, and so on), but we do not send any traffic to it.
![](/assets/istioinaction/ch05_f02_posta2.png)

    + Releasing code means bringing live traffic over to our new deployment.

![](/assets/istioinaction/ch05_f03_posta2.png)

      + Now the old version of our software is taking the bulk of the live traffic, and the newer version is taking a small fraction of the traffic. This approach is known as _canarying_ or a _canary release_.

      + We can continue to shift traffic over to our new deployment until it’s fully released. At any point in this process, we may find that the new code doesn’t deliver the functionality, behavior, or performance we expected and validated through real user interaction. We can then roll back the release by directing traffic back to the previous version.


![](/assets/istioinaction/ch05_f05_posta2.png)

      + 

## Chapter 6: Resilience: Solving application networking  challenges


  + ### Client-side load balancing


    + _Client-side load balancing_ is the practice of informing the client about the various endpoints available for a service and letting the client pick specific load-balancing algorithms for the best distribution of requests over the endpoints.

    + Service operators and developers can configure what load-balancing algorithm a client uses by defining a `DestinationRule` resource. Istio’s service proxy is based on Envoy and supports Envoy’s load-balancing algorithms, some of which include:


      + Round robin (default)

        + Round robin (or next-in-loop) delivers requests to endpoints in a successive loop.

      + Random

        + Random uniformly picks an endpoint at random.

      + Weighted least request

        + Round robin and random do not take any runtime behavior into account. The least-connection load balancer (in Envoy, it’s implemented as least request) _does_ take into account the latencies of the specific endpoints.

        + Even though the Istio configuration refers to the least-request load balancing as `LEAST_CONN`, Envoy is tracking request depths for endpoints, not connections. The load balancer picks two random endpoints, checks which has the fewest active requests, and chooses the one with the fewest active requests.

  + ### Locality-aware load balancing


    + One role of a control plane like Istio’s is understanding the topology of services and how that topology may evolve. An advantage of understanding the overall topology of services in a service mesh is automatically making routing and load-balancing decisions based on heuristics like the locations of services and peer services.

    + Istio supports a type of load balancing that gives weights to routes and makes routing decisions based on where a particular workload is. Istio’s ability to load-balance across localities includes region, zone, and even a finer-grained subzone. Istio’s locality-aware load balancing is enabled by default.

    + When deploying in Kubernetes, region and zone information can be added to labels on the Kubernetes nodes. For example, the labels `failure-domain.beta.kubernetes.io/region` and `failure-domain.beta.kubernetes.io/zone` allow us to specify the region and zone, respectively. In recent, generally available versions of the Kubernetes API, those labels have been replaced with `topology.kubernetes.io/region` and `topology.kubernetes.io/zone`.

    + For locality-aware load balancing to work in Istio, we need to configure one last piece of the puzzle: **health checking**. Without health checking, Istio does not know which endpoints in the load-balancing pool are unhealthy and what heuristics to use to spill over into the next locality.

    + **Outlier detection** passively watches the behavior of endpoints and whether they appear healthy. It does so by tracking errors that an endpoint may return and marking them as unhealthy.

  + ### Transparent timeouts and retries


    + Istio allows us to configure various types of timeouts and retries to overcome inherent network unreliability.

    + Generally, it makes sense to have larger timeouts at the edge (where traffic comes in) of an architecture and shorter (or more restrictive) timeouts for the layers deeper in the call graph.

    + We have to balance out the fact that unbridled retries can contribute to degraded system health, including causing cascading failures. If a service is legitimately overloaded and misbehaving, retrying requests will only amplify the degraded situation.

    + Istio has retries enabled by default and will retry up to **two** times.

    + Typically, it is safe to retry a request in these default situations because they indicate that network connectivity could not be established, and the request could not have been sent on the first try:

      + `connect-failure`

      + `refused-stream`

      + `unavailable` (gRPC status code 14)

      + `cancelled` (gRPC status code 1)

      + `retriable-status-codes` (defaults to HTTP 503 in Istio)

  + ### Circuit breaking with Istio


    + We use circuit-breaking functionality to help guard against partial or cascading failures.

    + Istio doesn’t have an explicit configuration called “circuit breaker,” but it provides two controls for limiting load on backend services, especially those experiencing issues, to effectively enforce a circuit breaker.

      + The first is to manage how many connections and outstanding requests are allowed to a specific service.

        + In Istio, we use the `connectionPool` settings in a destination rule to limit the number of connections and requests that can pile up when calling a service. If too many requests pile up, we can short-circuit them (fail fast) and return to the client.

        + When a request fails for tripping a circuit-breaking threshold, Istio’s service proxy adds an `x-envoy-overloaded` header.

      + The second control is to observe the health of endpoints in the load-balancing pool and evict misbehaving endpoints for a time. Istio uses `OutlierDetection` to guard against unhealthy services.

        + If certain hosts in a service pool are experiencing failures, we can skip sending traffic to them. If we exhaust all hosts, the circuit is effectively “open” for a while.

## Chapter 7: Observability: Understanding the behavior of you services


  + ### Observability vs Monitoring

    + **[Observability]({{< ref "/pages/Observability" >}})** is a characteristic of a system that is measured by the level to which you can understand and reason about a system’s internal state just by looking at its external signals and characteristics.

    + Observability is important to implement controls for a system in which we can change its run-time behavior.

    + ** [Monitoring]({{< ref "/pages/Monitoring" >}}) ** is the practice of collecting metrics, logs, traces, and so on, aggregating them, and matching them against predefined criteria of system states that we should carefully watch.

    + Monitoring is a subset of observability. With monitoring, we are specifically collecting and aggregating metrics to watch for known undesirable states and then alert on them. On the other hand, observability supposes that our systems are highly unpredictable, and we cannot know all of the possible failure modes in advance.

  + Envoy has a notion of _internal origin_ versus _external origin_ when identifying traffic. _Internal_ is typically what we recognize as traffic from within the mesh, and _external_ is traffic originating outside the mesh (traffic coming into an ingress gateway).

  + ### Scraping Istio metrics with Prometheus

    + To configure [Prometheus]({{< ref "/pages/Prometheus" >}}) to collect metrics from Istio, we will use the Prometheus Operator custom resources `ServiceMonitor` and `PodMonitor`.

    + We can set up a `ServiceMonitor` resource to scrape the Istio control-plane components.

    + To enable scraping for the data plane, we use a `PodMonitor` resource that configures the [Prometheus]({{< ref "/pages/Prometheus" >}}) Operator to scrape metrics from every Pod containing the istio-proxy container.

  + ### Customizing  Istio's standard metrics

    + A `metric` is a counter, gauge, or histogram/distribution of telemetry between service calls (inbound/outbound).

## Chapter 8: Observability: Visualizing network behavior with Grafana, Jaeger and Kiali

  + ### Using Grafana to visualize Istio service and control-plane metrics

    + There are many out-of-box Grafana dashboards in [grafana.com](https://grafana.com/grafana/dashboards/).

  + ### Distributed tracing

    + Distributed tracing gives us insights into the components of a distributed system involved in serving a request. It was introduced by the Google Dapper paper (“Dapper, a Large-Scale Distributed Systems Tracing Infrastructure,” 2010, https://research.google/pubs/pub36356) and involves annotating requests with **correlation IDs** that represent service-to-service calls and **trace IDs** that represent a specific request through a graph of service-to-service calls.

    + OpenTelemetry is a community-driven framework that includes OpenTracing, which is a specification that captures concepts and APIs related to distributed tracing.

    + #### How does distributed tracing work?

      + At its simplest form, distributed tracing with OpenTracing consists of applications creating `Span`s, sharing those `Span`s with an OpenTracing engine, and propagating a trace context to any of the services it subsequently calls. A `Span` is a collection of data representing a _unit of work_ within a service or component. This data includes things like the start time of the operation, the end time, the operation name, and a set of tags and logs.

      + In turn, those upstream services do the same thing: create a `Span` capturing its part of the request, send that to the OpenTracing engine, and further propagate the trace context to other services. Using these `Span`s and the trace context, the distributed-tracing engine can construct a `Trace`, which is a causal relationship between services that show direction, timing, and other debugging information. Spans have their own ID and a `Trace` ID. These IDs are used for correlation and are expected to be propagated between services.


![](/assets/istioinaction/ch08_f07_posta2.png)

      + Istio can handle sending the `Span`s to the distributed tracing engine. When a request traverses the Istio service proxy, a new trace is started if there isn’t one in progress, and the start and end times for the request are captured as part of the `Span`. Istio appends HTTP headers, commonly known as the Zipkin tracing headers, to the request that can be used to correlate subsequent `Span` objects to the overall `Trace`. If a request comes into a service and the Istio proxy recognizes the distributed-tracing headers, the proxy treats it as an in-progress trace and does not try to generate a new one. The following Zipkin tracing headers are used by Istio and the distributed-tracing functionality:


        + `x-request-id`

        + `x-b3-traceid`

        + `x-b3-spanid`

        + `x-b3-parentspanid`

        + `x-b3-sampled`

        + `x-b3-flags`

        + `x-ot-span-context`

      + For the distributed-tracing functionality provided by Istio to work across the entire request call graph, each application needs to propagate these headers to any outgoing calls it makes.


![](/assets/istioinaction/ch08_f08_posta2.png)

## Chapter 9: Securing microservice communication


  + Istio is _secure by default_. Istio provides an automated way to issue the identity of services using the **Secure Production Identity Framework for Everyone (SPIFFE)** framework. The issued identity is used for services to mutually authenticate.

  + {{< logseq/orgTIP >}}SPIFFE identity is an RFC 3986 compliant URI composed in the format _spiffe://trust-domain/path_, where
* The _trust-domain_ represents the issuer of identities, such as an individual or organization.
* The _path_ uniquely identifies a workload within the trust domain.
{{< / logseq/orgTIP >}}

  + Istio populates the _path_ above using the service account under which a particular workload runs. This SPIFFE identity is encoded in an X.509 certificate, also known as a **SPIFFE Verifiable Identity Document (SVID)**, which Istio’s control plane mints for workloads. These certificates are then used to secure the transport for service-to-service communication by encrypting data in transit.

  + ### Istio security in a nutshell


    + Service mesh operator configures the service proxies using custom resources defined by Istio:

      + The `PeerAuthentication` resource configures the proxy to authenticate **service-to-service** traffic. On successful authentication, the proxy extracts the information encoded in the peer’s certificate and makes it available to authorize the request.

      + The `RequestAuthentication` resource configures the proxy to authenticate **end-user** credentials against the servers that issued them. On successful authentication, it also extracts the information encoded in the credential and makes it available for authorizing the request.

      + The `AuthorizationPolicy` resource configures the **proxy** to authorize or reject requests by making decisions based on the data extracted by the previous two resources.

![](/assets/istioinaction/ch09_f03_posta2.png)

  + ### Auto mTLS


    + Traffic between services that have the sidecar proxy injected is encrypted and mutually authenticated by default.

    + Basic model of how mTLS is established between a client and server


    + Workloads mutually authenticate using SVID certificates issued by the Istio certificate authority.


![](/assets/istioinaction/ch09_f04_posta2.png)

    + ### Understanding Istio's PeerAuthentication resource

      + The `PeerAuthentication` resource enables configuration of workloads to either strictly require mTLS or be permissive and accept clear-text traffic, using the `STRICT` or `PERMISSIVE` authentication mode, respectively. The mutual authentication mode can be configured in different scopes:

        + _Mesh-wide_ `PeerAuthentication` policies apply to all workloads of the service mesh.

          + A mesh-wide `PeerAuthentication` policy must fulfill two conditions: it must be applied in the Istio installation namespace, and it must be named "`default`".

        + _Namespace-wide_ `PeerAuthentication` policies apply to all workloads in a namespace.

        + _Workload-specific_ `PeerAuthentication` policies apply to all workloads that match the selector specified in the policy.

      + We can use workload-specific policies to allow non-mutually authenticated traffic until those are migrated into the mesh.


![](/assets/istioinaction/ch09_f06_posta2.png)

  + ### Authorizing service-to-service traffic


    + Istio provides the `AuthorizationPolicy` resource, which is a declarative API to define mesh-wide, namespace, or workload-specific access policies in a service mesh. Authorization reduces the attack scope to only what the stolen identity was authorized to access.


![](/assets/istioinaction/ch09_f09_posta2.png)

    + Evaluation flow of authorization policies


![](/assets/istioinaction/ch09_f10_posta2.png)

    + 

  + ### End-user authentication and authorization

    + The main purpose of the `RequestAuthentication` resource is to validate JWTs, extract the claims of valid tokens, and store those claims in filter metadata, which is used by authorization policies to take actions based on the data.

## Chapter 10: Troubleshooting the data plane


  + Components that participate in routing a request


![](/assets/istioinaction/ch10_f01_posta2.png)

  + ### Identifying data-plane issues


    + #### Verify that the data plane is up to date


      + Considering that the primary function of the control plane is to synchronize the data plane to the latest configuration, the first step is to verify that the control plane and data plane are in sync.

        + {{< logseq/orgTIP >}}The data-plane configuration is eventually consistent by design.
{{< / logseq/orgTIP >}}

      + Series of events until the configuration of a data-plane component is updated after a workload becomes unhealthy.

![](/assets/istioinaction/ch10_f03_posta2.png)

      + Check whether the data plane is synchronized with the latest configuration using `istioctl proxy-status` command. For example:

`shell
$ istioctl proxy-status
 
NAME                                      CDS      LDS      RDS
catalog.<...>.istioinaction               SYNCED   SYNCED   SYNCED
`

        + The output lists all workloads and their synchronization state for every xDS API.

          + `SYNCED`—Envoy has acknowledged the last configuration sent by the control plane.

          + `NOT SENT`—The control plane hasn’t sent anything to Envoy. This is usually because the control plane has nothing to send. Such is the case of the route discovery service (RDS) for istio-egressgateway, shown in the previous snippet.

          + `STALE`—The istiod control plane has sent an update, but it wasn’t acknowledged. This indicates one of the following: the control plane is overloaded; lack or drop of connectivity between Envoy and the control plane; or a bug on Istio.

    + #### Discovering misconfigurations with Kiali


      + The most common issues with the data-plane components are due to workload misconfiguration. Kiali can discover misconfigured services.

    + #### Discovering misconfigurations with istioctl

      + To automatically troubleshoot misconfigured workloads, two of the most useful [istioctl](https://istio.io/latest/docs/reference/commands/istioctl) commands are `istioctl analyze` and`istioctl describe` .

  + ### Discovering misconfigurations manually from the Envoy config
    + We can retrieve the Envoy configuration that is applied on a workload using the Envoy administration interface or `istioctl`.

    + #### Envoy administration interface

      + The [Envoy administration interface](https://www.envoyproxy.io/docs/envoy/latest/operations/admin) exposes the Envoy configuration and other capabilities to modify aspects of the proxy, such as increasing the logging level. This interface is accessible for every service proxy on port 15000.


      + Using  `istioctl` , we can port-forward it to our localhost:

`shell
$ istioctl dashboard envoy deploy/xxx-deployment -n xxx-ns
 
http://localhost:15000
`

      + Or we can use `kubectl exec` command directly:


`shell
$ kubectl -n xxx-ns exec xxx-pod -c istio-proxy -- curl http://localhost:15000/help
`

    + #### Querying proxy configurations using istioctl


      + The `istioctl proxy-config` command enables us to retrieve and filter the proxy configuration of a workload based on the Envoy xDS APIs, where each subcommand is appropriately named. [Read more](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-config).

![](/assets/istioinaction/ch03_f03_posta2.png)


      + Troubleshooting steps:

        + id:: 62fc54ff-d00f-443c-9fa3-10b2cca094f8
1. Query the Envoy listener configuration through `istioctl proxy-config listener` [command](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-config-listener).
2. Query the Envoy route configuration through `istioctl proxy-config route` [command](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-config-route).
3. Query the Envoy cluster configuration through `istioctl proxy-config cluster` [command](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-config-cluster).
4. Query the Envoy cluster endpoints through `istioctl proxy-config endpoint` [command](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-config-endpoint).

## Chapter 11: Performance-tuning the control plane

  + ### The control plane's primary goal

    + Istio’s control plane listens to events from Kubernetes and updates the configuration to reflect the new desired state.

    + A common phenomenon that crops up when performance degrades is known as *phantom workloads*: services are configured to route traffic to endpoints that are already long gone, and hence the requests fail.


![](/assets/istioinaction/ch11_f01_posta2.png)

    + Due to the eventually consistent nature of the data plane, having a stale configuration for a short time won’t cause too many negative consequences, as other protective mechanisms such as retry (to another health endpoint) and outlier detection (which ejects endpoints from the cluster) can be employed.

    + The sequence of actions to push the latest configuration to workloads.

![](/assets/istioinaction/ch11_f02_posta2.png)

    + `istiod` protects itself from becoming overloaded by using the two practices of **debouncing** and **throttling**.
      + {{< logseq/orgNOTE >}}**Debouncing**: The DiscoveryServer component of istiod listens for these events. To improve performance, it delays adding the event to the push queue for a defined time to batch and merge subsequent events for that period.
{{< / logseq/orgNOTE >}}

## Chapter 14: Extending Istio on the request path

  + ### Envoy's extension capabilities


    + #### Understanding Envoy's filter chaining

      + ### Envoy's core features


      + **Listeners**: Expose a port to the outside world to which applications can connect. A listener in Envoy is a way to open a port on a networking interface and start listening to incoming traffic.


        + A listener reads bytes off the networking stream and processes them through various **filters** or stages of functionality.

![](/assets/istioinaction/ch14_f02_posta2.png)

        + Envoy’s most basic filters are *network* filters, which operate on a stream of bytes for either encoding or decoding. You can configure more than one filter to operate on the stream in a sequence called a **filter chain**, and these chains can be used to implement the functionality of the proxy.

    + #### Filters intended for extension

      + Although you can write your own filters in C++ and build them into the proxy, there are ways to extend Envoy’s HTTP capabilities, including writing filters, without compiling changes into the Envoy binary itself, by using the following HTTP filters:

        + External processing

        + Lua

        + Wasm (WebAssembly)

      + With these filters, you can configure calls out to an external service, run a Lua script, or run custom code to enhance the capabilities of the HCM when processing HTTP requests or responses.

  + ### Configuring an Envoy filter with the EnvoyFilter resource
    + Istio’s `EnvoyFilter` resource is intended for advanced use cases where a user needs to either tweak or configure a portion of Envoy not exposed by Istio’s higher-level APIs.

    + {{< logseq/orgCAUTION >}}The `EnvoyFilter` resource is intended for advanced usage of Istio and is a “**break glass**” solution. The underlying Envoy API may change at any time between releases of Istio, so be sure to validate any `EnvoyFilter` you deploy.
{{< / logseq/orgCAUTION >}}

    + The first thing to know about an `EnvoyFilter` resource is that it applies to **all workloads in the namespace** for which it is declared, unless you specify otherwise. If you want to be more specific about the workloads in a namespace to which the custom  `EnvoyFilter`  configuration applies, you can use a **`workloadSelector`**.

    + The second thing to know about an `EnvoyFilter` resource is that it applies after all other Istio resources have been translated and configured.

    + Finally, you should take great care when configuring a workload with the `EnvoyFilter` resource. You should be familiar with Envoy naming conventions and configuration specifics. This really is an advanced usage of Istio’s API and can bring down your mesh if misconfigured.

  + ### Rate-limiting requests with external call-out

    + There are also out-of-the-box filters that enhance the data plane with functionality that exists as a *call-out*. With these filters, we call out to an external service and have it perform some functionality that can determine how or whether to continue with a request.

    + Multiple replicas of the same service call the same rate-limit service to get **global rate limiting** for a particular service.

![](/assets/istioinaction/ch14_f06_posta2.png)

    + With this architecture (global rate limiting), we can ensure that a rate limit is enforced **regardless of how many replicas of a service exist**.

![](/assets/istioinaction/ch14_f07_posta2.png)

    + #### Understanding Envoy rate limiting

      + Before configuring the Envoy **rate-limit server (RLS)**, we need to understand how rate limiting works.
      + When the rate-limit filter processes an HTTP request, it takes certain **attributes** from the request and sends them out to the RLS to make a rate-limit decision against a preconfigured set of descriptors. Envoy rate-limiting terminology uses the word ***descriptors*** to refer to attributes or groups of attributes. These descriptors, or attributes, of the request can be things like remote address, request headers, destination, or any other generic attributes about the request.

      + The RLS evaluates the request attributes that have been sent as part of a request against a set of predefined attributes.

![](/assets/istioinaction/ch14_f08_posta2.png)

      + Steps to configure global rate limiting:

        + 1. Configure the Envoy rate-limit server, register the rate-limit service as an envoy cluster. This is done via an envoy filter patch which is applied at the cluster level. 
2. Configure Envoy with which attributes to send for a particular request. Envoy terminology refers to this configuration as the rate-limit ***actions*** taken for a particular request path.

      + 

## Appendix B. Istio's sidecar and its injection options


  + Istio’s data-plane components


![](/assets/istioinaction/appb_f01_posta2.png)

  + sidecar injection

    + Manual injection


`shell
$ istioctl kube-inject -f deployment.yaml
`

![](/assets/istioinaction/appb_f02_posta2.png)

    + Auto injection

      + How it works


        + {{< logseq/orgTIP >}}Automatic sidecar injection uses Kubernetes mutating admission webhooks to inject data-plane components into the `Pod` definition before it is applied to the Kubernetes datastore
{{< / logseq/orgTIP >}}

![](/assets/istioinaction/appb_f03_posta2.png)

      + Automatic sidecar injection is an opt-in feature enabled on a namespace-by-namespace basis. To do this, you label the namespace with  `istio-injection=enabled` .

## Appendix D. Troubleshooting Istio components


  + Agent and Envoy proxy ports and their functionality

![](/assets/istioinaction/appd_f01_posta2.png)

  + The exposed `istiod`(Istio Pilot) ports and their functionality


![](/assets/istioinaction/appd_f02_posta2.png)

  + 
