Google Networking


Virtual Private Cloud: SDN

- **VPC Networks**
- **Subnets**
- **Firewall rules,**
- **Routes,**
- **Forwarding rules,**
- **IP Addresses (local, external)**
- [**VPC Sharing**](https://github.com/ashutosh-mishra/Notes/blob/master/shared-VPC-GCP.md)
- **VPC Peering**
- **Hybrid Cloud**
  - **VPN**
    - [Interconnect](https://github.com/ashutosh-mishra/Notes/blob/master/GCP%20Connectivity.md)
    - [Dedicated Interconnect](https://github.com/ashutosh-mishra/Notes/blob/master/GCP%20Connectivity.md)
    - [Partner Interconnect](https://github.com/ashutosh-mishra/Notes/blob/master/GCP%20Connectivity.md)
- [**Load Balancing**](https://github.com/ashutosh-mishra/Notes/blob/master/GCP-Load%20Balancer.md)
  - [Backend Services](https://github.com/ashutosh-mishra/Notes/blob/master/GCP-Load%20Balancer.md)
  - [Forwarding rules](https://github.com/ashutosh-mishra/Notes/blob/master/GCP-Load%20Balancer.md)
  - [URL Paths](https://github.com/ashutosh-mishra/Notes/blob/master/GCP-Load%20Balancer.md)
  - [Health check](https://github.com/ashutosh-mishra/Notes/blob/master/GCP-Load%20Balancer.md)
- **Config**
  - Private IP Access**
    Instances in VPC subnet can communicate with google API and services using just internal IP address without any external IP address.

Available networking Services/Features:
- **Carrier Peering**: Peer through a carrier
- **Direct Peering**: Peer with GCP
- **Dedicated Interconnect**: Dedicated private network connection with google network
- **Partner Interconnect**: Connect on-prem network to VPC via 3rd party service provider, which will have internal network connection with google network
- **Cloud Router**: VPC/on-prem network route exchange (BGP)
- **IPsec VPN**: Virtual private network connection    
********
- **Cloud DNS**: Programmable DNS serving    
********
- **Cloud Load Balancing**: Multi-region load distribution
********
- **Cloud NAT**: Network address translation service
********
- **Network Service Tiers**: premium tier, standard tier for network traffic, where premium tier uses google backbone network and standard tier uses ISP network. Standard tier offers less   performance with less pricing.
- **Network Telemetry**: Realtime data collection service w.r.t network traffic. While creating subnet, flow logging can be configured. By-default flow logging is disabled.
********
- **Traffic Director**: Service mesh traffic management
- **Google Cloud Service Mesh**: Service-aware network management    
********
- **Cloud Armor**: DDoS protection and WAF    
- **Cloud CDN**: Content delivery network

