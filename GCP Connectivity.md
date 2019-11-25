

Google cloud offers different options for extending on-premises network.    
Interconnect solution extends on-premises network with GCP VPC so that internal IP address are directly reachable without any NAT or VPN.    
Interconnect solution provides direct physical connection between on-premises network to google cloud’s network.
Also enabled large data transfer between networks instead of using public network in a cost effective way.



### 1. Google cloud Interconnect - Dedicated (Dedicated Interconnect)
A cross connect is provisioned between the Google network and the on-premises router in a common colocation facility. This cross connect is a Dedicated Interconnect connection.    
To exchange routes a BGP session is configured over the interconnect between cloud Router and on-premises router.    

**_Interconnect_** - A physical connection between Google’s network and on-premises network. Interconnect exists in a colocation facility
where both networks are physical available.    
**_VLAN Attachment_** - A VLAN attachment attaches a interconnect with cloud router.
A VLAN attachment is a virtual point-to-point tunnel between your on-premises network and a single region in a VPC network.    
**_Cloud Router_** - Used to exchange routes between VPC and on-premises network.
A BGP session will be created between cloud router and on-premises router to exchange routes.    

- Additional hardware need to install and manage at colocation facility.
- The connection between GCP VPC and on-premise network is not encrypted. For security, use application-level encryption or cloud VPN solution.

![Dedicated Interconnect](https://cloud.google.com/interconnect/images/single-interconnect.svg)

### 2. Google cloud Interconnect - partner (Partner Interconnect)
Provides network connectivity between GCP VPC and on-premises network through a service provider. Used when on-premise network is not available at colocation facility to connect via dedicated interconnect. [Service providers](https://cloud.google.com/interconnect/docs/concepts/service-providers) like AT&T, Verizon, other telcos will be used.

- Service provider will be having a dedicated interconnect setup between Google and Service provider network.
- Service providers might support layer-2 connectivity, layer-3 connectivity or both.
- *Layer-2 Connectivity*

    For layer-2 connections, a new GBP session needs to create between cloud router and on-premises router for each individual VLAN attachment.
    ![Layer-2 topology](https://cloud.google.com/interconnect/images/layer2-basic.svg)
- *Layer-3 Connectivity*

    For layer-3 connections, a BGP session needs to create between cloud router and service provider interconnect router for each individual VLAN attachment.
    ![Layer-3 topology](https://cloud.google.com/interconnect/images/layer3-basic.svg)

- Compared to Dedicated Interconnect, you don't need to install and maintain routing equipment in a colocation facility.
- The connection between GCP VPC and on-premise network is not encrypted. For security, use application-level encryption or cloud VPN solution.
- Google, bills based on interconnect attachment’s capacity and egress traffic, Service provider charges are not included.

### 3. Peering

Peering is the direct interconnection between Google's network and another network to support the exchange of traffic. Networks peer to gain some combination of economic, performance and traffic control benefits.    
a. Direct peering   
b. Carrier peering   
c. CDN Interconnect   


### 4. VPN
A Virtual Private Network allows to securely connect GCP VPC to other/on-premise network. Google VPN uses IKEv1 or IKEv2 to establish IPSec connectivity.
VPN Options:

i. *HA VPN*
- Supports only dynamic routing(BGP)
- 2 tunnels will be created between PVN gateway and peer gateway

ii. *Classic VPN*
- Support both static routing(policy based, route based) and dynamic routing(BGP)
- Single tunnel will be created between VPN gateway and peer gateway



