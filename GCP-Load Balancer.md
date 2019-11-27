
## Load balancing
GCP provides load balancing solution to distribute load-balanced compute resources in a single or multiple regions.     
Types of Cloud Load Balancing:
1. Internal
Distribute traffic within your GCP network

2. External
Distribute traffic from Internet to GCP network


Load balancing can be used with Managed instance groups to provide autoscaling.

**Traffic type**    
The type of traffic you need your load balancer to handle is another factor in determining which load balancer to use.
-  HTTP and HTTPS traffic can be handled by external HTTP(S) or Internal HTTP(S) Load Balancing.
-  TCP traffic can be handled by Network Load Balancing or Internal TCP/UDP Load Balancing.
-  UDP traffic can be handled by Network Load Balancing or Internal TCP/UDP Load Balancing.


A Load Balancer is composed of several components:
1. Forwarding rule
2. Target HTTP proxy
3. URL mapper
4. Backend service
5. Backend instances
6. Health checks

**Typical LB Workflow:**
1. A forwarding rule must be present, which expose an external IP, and directs incoming traffic to target HTTP(s) proxy
2. Target proxy checks for each request with a URL map, to identify which backend service to use.
3. URL mapper can contain mapping of different backend services based on different url paths.
4. Backed service forward each request to appropriate backend based on capacity, zone and health of the attached backend instance.
5. The health of backend instance is verified using an HTTP health check. Health check runs periodically for each attached backends to verify whether instances and corresponding services are reachable or not. 

**GCP LB flow:**
1. Create Backend instance group, via
    1. Create Instance template
       This template is used to create instances. Template contains machine-type, image and access scopes.
    2. Create Network endpoint group
       Network endpoints represent services (applications, load balancing) and diverse infrastructure (VM instances, containers etc) regardless of their location.
        - Create Network endpoint by providing network details
        - Add network endpoints by providing corresponding instance IPs and ports

2. Create Instance group    
    Organise VM instances in a group to manage them together.
    Instance group contains configurations like: Instance template, region, zone, autoscaling, CPU utilisation, min and max no of instances.

3. Create Load Balancer in Network services, select Load Balancer is external or internal
    1. HTTP Load Balancer
        1. Create Backend configuration
            - Create Backend service or Backend bucket
                - Backend service: Specify protocol, port, backend type as instance group or network endpoint group and health check
                - Backend bucket: Create cloud storage bucket 
        2. Create URL path rules
        3. Create frontend configuration
            - Specify protocol, port and static or ephemeral ip

    2. TCP/UDP Load Balancer
        1. Create Backend configuration
        2. Create frontend configuration


If no backend instance exists, then instances will be created as part of backend creation due to minimum no of instances parameter in instance group.


**Forwarding rule:**    
Each forwarding rule specifies an IP address and port on which load balancer accepts traffic.
Depending on the load balancer type a forwarding rule will have external or internal IP address and global or regional.
Forwarding rule will be of 2 types, based on where traffic is originated from internet or from GPC internal network:
1. Internal forwarding rule
2. External forwarding rule

**Target proxies:**    
- Terminate HTTP(s)/TCP/SSL client connections.
- One or more forwarding rules can point to same target proxies.
- Supports adding custom request headers, while sending request to backend instances.

**URL Maps:**    
Define mapping to support URL path based routing. A default path entry will always present to map the requests which doesn’t map with any defined paths.
Default entry is also used in regional load balancing, where same URL path requests need to load balanced regionally.

**Backend Services:**    
Maintains and provide backend configurations to the load balancers. Each load balancer will have at least 1 backend service and can have more services.
- Backend service will have backends as Instance Groups or Network Endpoint Groups.
- Load distribution on backend instances will happen based on CPU capacity or requests per second.
- Supports connection draining, which can be enabled to ensure minimal interruptions to the user when an instance is terminated, removed manually or removed by autoscaler.


**Health Check:**    
Each backend service also specifies which health check to perform on each available instance.

**Backend Protocols:**    
Following protocols can be used with GCP Load Balancers: TCP, SSL, HTTP(s), HTTP2
  gRPC:
    As gRPC is based on HTTP/2 protocol, it can be used with GCP HTTP(s) Load Balancers. To support this, an HTTP Load balancer need to configure:
    - An HTTPS Loadbalancer for client connections
    - Enable HTTP/2 as protocol in backend service for load balancer to backend instances connection

**Backend buckets:**    
Storage buckets can be used as backends in loadbalancers. Typical example will be to send all /static content requests to a storage bucket and all dynamic content requests will go to instance groups.

**Firewall rules:**    
A firewall rule must be created in instances network to allows traffic from 130.211.0.0/22 and 35.191.0.0/16 to reach your instances.
- Both Lad Balancer and Health check uses this  range to connect to backend instances.

### Cloud Load Balancers
![External and Internal Load Balancing type](https://cloud.google.com/load-balancing/images/choose-lb-6.svg)

#### TCP proxy Load Balancers
Google cloud TCP proxy Load Balancers terminate SSL connections at load balancing layer, then distribute the connections to backend instances using SSL or TCP protocols.
Session Affinity - Sends all request of the same client to the same backend instance as long as it satisfies health check and utilisation checks.
- Supports client IP affinity i.e. All requests from same client IP address will be sent to same backend instance


#### SSL proxy Load Balancers
Google cloud SSL proxy Load Balancers terminate SSL connections at load balancing layer, then distribute the connections to backend instances using SSL or TCP protocols.
- Deployed globally
- Instances can reside in multiple regions, Load balancer choose the instance which is closest
- SSL proxy can handle HTTPS traffic, but its not recommended. For HTTPS traffic use HTTP Load balancers as they provide more features.
  When to use HTTPS Load Balancers instead of SSL proxy :
    - Negotiates HTTP/2 and SPDY/3.1
    - Rejects invalid HTTP requests and responses
    - Forward requests to different instance groups based on url paths

- SSL proxy load balancers do not support client based authentication i.e. mutual TLS authentication

- Supports TCP traffic that needs SSL (TLS) offload.
- Provides centralized certificate management.
- Supports end-to-end encryption when you configure the associated backend service to use the SSL protocol.
- Supports TCP traffic on ports 25, 43, 110, 143, 195, 443, 465, 587, 700, 993, 995, 1883, and 5222.
- Efficiently handles traffic bursts without pre-warming.
- Supports instance group backends, including autoscaling instance groups, and Network Endpoint Group backends.
- Supports client IP base session affinity
- Can be used for other protocols that use SSL such as websockets.



#### HTTP(s) Load Balancers
HTTP(s) Loadbalancers are reverse proxy load balancers i.e. client connection terminates at load balancer and load balancer opens a new connection to the backend.

- Client can connect with load balancer via HTTP1.1 or HTTP2 protocol.
- Load balancer does not support mutual TLS i.e. client authentication
- Session between load balancer and instance can use HTTP, HTTPS or HTTP2 protocol. If using HTTPS, backend instance must have a certificate(either issued or self-signed).

- Supports HTTP traffic on TCP ports 80 or 8080 and HTTPS traffic on TCP port 443.
- Efficiently handles traffic bursts without pre-warming.
- Supports instance group backends, including autoscaling instance groups, and Network EndpointGroups backends.
- Supports Cloud Storage bucket backends.
- Routes requests based on host name and path routing rules using URL maps.
- Supports utilization and rate-based (RPS) balancing.



#### Network TCP/UDP Load Balancing
Network Load Balancing enables you to load balance traffic on your systems based on incoming IP protocol data, including address, port, and protocol type. It is a regional, non-proxied load balancing system. Use Network Load Balancing for UDP traffic, and for TCP and SSL traffic on ports that are not supported by the SSL Proxy and TCP Proxy load balancers. A Network load balancer is a pass-through load balancer that does not proxy connections from clients.
Network Load Balancing provides the following characteristics and benefits:
- Supports regional load balancing.
- Reachable via an external IPv4 address.
- Supports TCP and UDP traffic.
- Supports terminating SSL on backends using TCP pass-through.
- Preserves client IP addresses.



**Connection draining:**    
Connection draining is a process to ensure that existing in-progress requests are given time to complete when a VM is removed from an instance group.
For a specified duration, existing requests to the removed VM are given time to complete. Load balancer does not send any new requests to the Instance.
Connection draining triggers whenever an instance
- Removed from instance group either manually or by resizing the instance
- GCP deletes the instance as part of autoscaling



