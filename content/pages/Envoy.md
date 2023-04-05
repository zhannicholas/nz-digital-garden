---
tags:
- Istio
- Envoy
- Service Mesh
title: Envoy
categories:
date: 2022-08-15
lastMod: 2022-08-17
---




[Envoy](https://www.envoyproxy.io/) is an open source edge and service proxy, designed for cloud-native applications.

## Terminology


  + > Reference: [Terminology in Architecture](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/intro/terminology), [Terminology in Request Life](https://www.envoyproxy.io/docs/envoy/latest/intro/life_of_a_request#terminology).

  + **Host**: An entity capable of network communication (application on a mobile phone, server, etc.).

  + **Downstream**: A downstream host connects to Envoy, sends requests, and receives responses.
  + **Listener**: A listener is a named network location (e.g., port, unix domain socket, etc.) that can be connected to by downstream clients. Envoy exposes one or more listeners that downstream hosts connect to.
  + **Filter**: a module in the connection or request processing pipeline providing some aspect of request handling. **Filter chain** is a series of filters.

  + **Cluster**: A cluster is a group of logically similar upstream hosts that Envoy connects to. Envoy discovers the members of a cluster via service discovery. It optionally determines the health of cluster members via active health checking. The cluster member that Envoy routes a request to is determined by the load balancing policy.

    + Components forming the cluster name

![](/assets/istioinaction/ch10_f09_posta2.png)

  + **Endpoints**: network nodes that implement a logical service. They are grouped into clusters. Endpoints in a cluster are *upstream* of an Envoy proxy.

  + **Upstream**: An upstream host (endpoint) receives connections and requests from Envoy and returns responses.

  + **Mesh**: A group of hosts that coordinate to provide a consistent network topology.
  + **Runtime configuration**: Out of band realtime configuration system deployed alongside Envoy. Configuration settings can be altered that will affect operation without needing to restart Envoy or change the primary configuration.

![](/assets/istioinaction/ch03_f03_posta2.png)


## Threading Model


  + > Reference: [Envoy's threading model](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/intro/threading_model).

  + Envoy uses a single process with multiple threads architecture. A single *primary* thread controls various sporadic coordination tasks while some number of *worker* threads perform listening, filtering, and forwarding. Once a connection is accepted by a listener, the connection spends the rest of its lifetime bound to a single worker thread.

## Listeners


  + **Listener**: A listener is a named network location (e.g., port, unix domain socket, etc.) that can be connected to by downstream clients. Envoy exposes one or more listeners that downstream hosts connect to.


  + Envoy supports both TCP and UDP listeners.

  + Listeners are optionally configured with some number of [listener filters](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/listeners/listener_filters#arch-overview-listener-filters). These filters are processed before the network level filters, and have the opportunity to manipulate the connection metadata, usually to influence how the connection is processed by later filters or clusters.

  + The [network (L3/L4) filters](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/listeners/network_filters) are chained in a ordered list known as [filter chain](https://www.envoyproxy.io/docs/envoy/latest/api-v3/config/listener/v3/listener_components.proto#envoy-v3-api-msg-config-listener-v3-filterchain).

    + There are three different types of network filters:

      + **Read**: Read filters are invoked when Envoy receives data from a downstream connection.

      + **Write**: Write filters are invoked when Envoy is about to send data to a downstream connection.

      + **Read/Write**: Read/Write filters are invoked both when Envoy receives data from a downstream connection and when it is about to send data to a downstream connection.

## HTTP Connection Manager


  + [HTTP connection manager (HCM)](https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_conn_man/http_conn_man#config-http-conn-man) is an Envoy network filter (See #### Understanding Envoy's filter chaining

 ). It translates raw bytes into HTTP level messages and events (e.g., headers received, body data received, trailers received, etc.). It also handles functionality common to all HTTP connections and requests such as [access logging](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/observability/access_logging#arch-overview-access-logs), [request ID generation and tracing](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/observability/tracing#arch-overview-tracing), [request/response header manipulation](https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_conn_man/headers#config-http-conn-man-headers), [route table](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/http/http_routing#arch-overview-http-routing) management, and [statistics](https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_conn_man/stats#config-http-conn-man-stats).

![](/assets/istioinaction/ch14_f03_posta2.png)

    + HTTP level filters

      + Much like the [network level filter](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/listeners/network_filters#arch-overview-network-filters) stack, Envoy supports an HTTP level filter stack within the HTTP connection manager.

      + There are three types of HTTP level filters:


        + **Decoder**: Decoder filters are invoked when the connection manager is decoding parts of the request stream (headers, body, and trailers).

        + **Encoder**: Encoder filters are invoked when the connection manager is about to encode parts of the response stream (headers, body, and trailers).

        + **Decoder/Encoder**: Decoder/Encoder filters are invoked both when the connection manager is decoding parts of the request stream and when the connection manager is about to encode parts of the response stream.

      + The most important HTTP filter is the **[router filter](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/http/http_routing#arch-overview-http-routing)** which sits at the end of the HTTP filter chain.

      + 

## Upstream Clusters


  + Envoy's [cluster manager](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/cluster_manager) manages all configured upstream [clusters](**Clusters**: Specific upstream services to which Envoy can route traffic. **Subsets** are used to further divide workloads within a cluster, which enables fine-grained traffic management.
).

  + Clusters known to the cluster manager can be configured either statically, or fetched dynamically via the cluster discovery service (CDS) API.

  + ### Service Discovery


    + > When an upstream cluster is defined in the [configuration](https://www.envoyproxy.io/docs/envoy/latest/api-v3/config/cluster/v3/cluster.proto#envoy-v3-api-msg-config-cluster-v3-cluster), Envoy needs to know how to resolve the members of the cluster. This is known as [*service discovery*](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/service_discovery).

    + Supported service discovery types


      + **Static**: The configuration explicitly specifies the resolved network name (IP address/port, unix domain socket, etc.) of each upstream host.

      + **Strict DNS**: When using strict DNS service discovery, Envoy will continuously and asynchronously resolve the specified DNS targets. Each returned IP address in the DNS result will be considered an explicit host in the upstream cluster.

      + **Logical DNS**: Similar to strict DNS, but a logical DNS cluster only uses the first IP address returned *when a new connection needs to be initiated*, rather than taking the results of the DNS query and assuming that they comprise the entire upstream cluster.

      + **Original Destination**: Original destination cluster can be used when incoming connections are redirected to Envoy either via an iptables REDIRECT or TPROXY target or with Proxy Protocol.

      + **Endpoint Discovery Service (EDS)**: The *endpoint discovery service* is a [xDS management server based on gRPC or REST-JSON API server](https://www.envoyproxy.io/docs/envoy/latest/configuration/overview/xds_api#config-overview-management-server) used by Envoy to fetch cluster members. The cluster members are called “endpoint” in Envoy terminology.

      + **Custom Cluster**: Custom clusters are specified using [cluster_type field](https://www.envoyproxy.io/docs/envoy/latest/api-v3/config/cluster/v3/cluster.proto#envoy-v3-api-field-config-cluster-v3-cluster-cluster-type) on the cluster configuration.

    + Service discovery is eventually consistent, Envoy assumes that hosts come and go from the mesh in an **eventually consistent** way.

  + ### Health Checking


    + > Envoy mesh configuration uses eventually consistent service discovery along with [active health checking](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/health_checking#arch-overview-health-checking) (Envoy explicitly health checking upstream cluster members) to determine cluster health.

    + When health checking is configured for an upstream cluster, Envoy uses a 2x2 matrix to determine whether to route to a host:

| Discovery Status | Health Check OK | Health Check Failed |
| ---- | ---- | ---- |
| Discovered | Route | Don’t Route |
| Absent | Route | Don’t Route / Delete |

    + Envoy supports three different types of health checking

      + **HTTP**: During HTTP health checking Envoy will send an HTTP request to the upstream host. By default, it expects a 200 response if the host is healthy.

      + **L3/L4**: During L3/L4 health checking, Envoy will send a configurable byte buffer to the upstream host. It expects the byte buffer to be echoed in the response if the host is to be considered healthy. Connect only L3/L4 health checking is also supported.

      + **Redis**: Envoy will send a Redis PING command and expect a PONG response.

    + Envoy also supports passive health checking via [outlier detection](### Outlier detection
).

  + ### Connection Pooling


    + For HTTP traffic, Envoy supports abstract [connection pools](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/connection_pooling) that are layered on top of the underlying wire protocol (HTTP/1.1, HTTP/2, HTTP/3).

    + Each host in each cluster will have one or more connection pools.

  + ### Load Balancing


    + [Load balancing](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/overview) is a way of distributing traffic between multiple hosts within a single upstream cluster in order to effectively make use of available resources.

    + When a filter needs to acquire a connection to a host in an upstream cluster, the cluster manager uses a load balancing policy to determine which host is selected.

    + Supported load balancing policy


      + [Weighted round robin](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/load_balancers#weighted-round-robin).

      + [Weighted least request](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/load_balancers#weighted-least-request).

      + [Ring hash](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/load_balancers#ring-hash).

      + [Maglev](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/load_balancers#maglev).

      + [Random](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/load_balancers#random).

    + During load balancing, Envoy will generally only consider hosts configured at the highest priority level.

  + ### Outlier detection
    + [Outlier detection and ejection](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/outlier)  is the process of dynamically determining whether some number of hosts in an upstream cluster are performing unlike the others and removing them from the healthy [load balancing](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/load_balancing/overview#arch-overview-load-balancing) set.

    + Detected errors fall into two categories: externally and locally originated errors.

      + Externally generated errors are transaction specific and occur on the upstream server in response to the received request.

      + Locally originated errors are generated by Envoy in response to an event which interrupted or prevented communication with the upstream host.

    + [Ejection algorithm](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/upstream/outlier#ejection-algorithm).

  + 

## Rate Limiting


  + Envoy supports two kinds of rate limiting: global and local.


  + #### [Global rate limiting](https://www.envoyproxy.io/docs/envoy/latest/intro/arch_overview/other_features/global_rate_limiting)


  + #### [Local rate limiting](https://www.envoyproxy.io/docs/envoy/latest/configuration/listeners/network_filters/local_rate_limit_filter#config-network-filters-local-rate-limit)


  + 

## Observability


  + ### Access Logs

    + [Reference](https://www.envoyproxy.io/docs/envoy/latest/configuration/observability/access_log/access_log).

    + #### Access Logging

      + Default format string

        + {{< logseq/orgSRC >}}[%START_TIME%] "%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%"
%RESPONSE_CODE% %RESPONSE_FLAGS% %BYTES_RECEIVED% %BYTES_SENT% %DURATION%
%RESP(X-ENVOY-UPSTREAM-SERVICE-TIME)% "%REQ(X-FORWARDED-FOR)%" "%REQ(USER-AGENT)%"
"%REQ(X-REQUEST-ID)%" "%REQ(:AUTHORITY)%" "%UPSTREAM_HOST%"\n
{{< / logseq/orgSRC >}}

    + 

## Toubleshooting


  + Worth to read: ### Discovering misconfigurations manually from the Envoy config

  + Dump Envoy configs

    + Admin API: [GET /config_dump?resource={}](https://www.envoyproxy.io/docs/envoy/latest/operations/admin#get--config_dump?resource=).

    + Supported configs: [ConfigDump(proto)](https://www.envoyproxy.io/docs/envoy/latest/api-v3/admin/v3/config_dump.proto).

    + Example

`shell
# Dump dynamic_listeners configs
$ kubectl -n xxx-ns exec xxx-pod -c istio-proxy -- curl http://localhost:15000/config_dump?resource=dynamic_listeners
`

    + 
