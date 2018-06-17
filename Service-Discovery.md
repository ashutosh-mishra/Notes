

# Service discovery 

## Service Registration
Process of implementing a service registry. Service registry is a DB populated with services info.


Service registeration happens via following ways :
1. Self registeration
2. Via other system components(Third party registeration)

### Self registeration
Service register itself with service registry on every change of states. Fo Ex. Whenever service comes up, goes down, configuring, services registers its current state with service registry.
![](/images/self-registeration.png)
![](https://cdn.auth0.com/blog/microservices/self-registration-diagram.png)

### Via other system components(Third party registeration)
In this approach services doesn't interface with service registry directly but registers via some other component. A typical approach would be a service manager responsible for getting the service states and updating those states in service registry. Some of the available tools are Marathon, Docker-swarm, etc.
![](/images/third-party-registeration.png)


## Service Discovery
To identify the service instances. Service instances have dynamically assigned addresses and the set of service instances
changes dynamically because of auto-scaling, failures, and upgrades.

Service discovery happens via following ways :
1. Client-side discovery
2. Server-side discovery

### Client-side

In cloud/microservices ip-address of service is constantly changing.
client made request to service registry first and then request corresponding server for service.
![](/images/client-side-discovery.png)

### Server-side
client always contact public ip of a loadbalancer/API server which internally contacts service registry and then connects to the corresponding
service.
![](/images/server-side-discovery.png)


#### Available solutions
* zookeeper - key-value based store, client handles failure, load balancing done by client, very good in maintaining consistency. Heavyweight for simple architecures. 
* etcd - Similar to zookeeper, distributed K/V staore. Exposes JSON based HTTP APIs, Supports TLS/SSL and TTLs on keys. Provides cluster coordination and state management.
* consul - Distributed KV store, REST based api. Provides high-availability KV store and service discovery(service configuration) tool. Biggest advantage over other tools are inbuilt DNS server, Dynamic load balancing, comprehensive health check(also supports custom solutions).


New solutions like kubernetes, Marathon are having service discovery built-in as part of infrastructure.  


One should select tool, which fits with current need, but not what is popular. Key points to note while selecting any specific tool
* Consistency
  - Strong
  - Eventual
* Registeration
  - Client
  - Third party/Automatic
* Discovery
  - Client
  - Server: DNS, LB
* Scale
* Performance
* Microservices environment
  - Kubernetes,
  - Docker,
  - Mesos, etc.


![Reference](https://www.youtube.com/watch?v=PptS7EgQvx4)
