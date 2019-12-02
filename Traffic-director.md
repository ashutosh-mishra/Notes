### Traffic director
Traffic management for open service mesh.    
- Background:
  - Service meshes are used to connect different micro services.
  - Service mesh data plane uses service proxy such as envoy.
  - Service mesh control plane configures these service proxies and sidecars proxies.
- Fully managed control plane that control traffic globally across kubernetes cluster(managed or not) and virtual machines.
- Traffic director comes with HA, so no need for check control plane’s health and maintenance.
- Traffic director is auto scalable i.e. with the size of service mesh traffic director also scales in the background.


Functionalities:
1. **Traffic management**    
LB support, content based routing
2. **Resilient services**    
Full HA, autoscaling, cross region support
3. **Health checks at scale**    
4. **Support for VMs**

Similar to istio, where some functionalities overlapped with istio.    
Biggest advantage is out-of-the-box multi cluster support and VMs support, whereas in istio all these needs to configure and maintain.

**Control plane**    
Istio has three core components:
* **Pilot for Traffic Management**,
* **Mixer for Observability** and 
* **Citadel for Service-to-Service Security**.    
Traffic Director delivers a GCP-managed Pilot along with additional capabilities such as global load balancing and centralised health checking.


Traffic director configuration includes configuring Load Balancing components:
- A global forwarding rule, VIP, URL paths and target proxy.
- Backend services
- Health checks, which provides health check for VM, prod in deployment



**Control plane:**    
Traffic director interacts with service proxies via xDS APIs for configuration.

**Data plane:**    
Traffic director configures global rule and corresponding VIP, which receives the traffic.

**Service Discovery:**    
Traffic director provides service discovery for both VMs and container endpoints. Discovery can be configured via Managed Instance groups for VMs and Network Endpoint groups for containers.

