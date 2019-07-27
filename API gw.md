
# API Gateway

An architecture pattern arise/needed with microservices deployments.

Microservices architecure specifies to split a large scale application into smaller services performing single task. Benefits of using microservices are:
- Independent service deloyments
- Independent release cycles
- Easier maintenance
- Grainular scalability

However using microservices pattern creates issue for both client and server
Client issues:
- Multiple API endpoints
- Too many roudtrips, Latency issues due to multiple endpoints. Affect a lot in mobile apps.
- Authentication and data format consistency
- Granularity of APIs
 - Microservices provide fine graines APIs, which might force client to interact with multiple services
- No of service instances and their host may change dynamically

Server issues:
- Duplication of common logic across services like
  - Authentication
  - Security
  - Logging
  - Authorization
  - Cache
  - Rate limiting
- Intense coupling (Clients are directly connectd with microservices, hence its hard to make changes)
- Client enforce services to maintain specific format(wither language or data specific)

API gateway architecure pattern is proposed to overcome above mentioned server issues.

API gateway is an abstraction layer which sits on execution path of every request, which provides different features commonly required by different services.
API gateway acts as aan API proxy server, where client connects with API gw which internally connects with corresponding services. Provides a way, how client of microservices can access the individual or combination of services.


## Advantages:
- Provides a single API endpoint to clients
- Centralized authentication and authorization
  - Client doesn't need to maintain multiple JWT tokens
- Added layer of security
- Abstract services from clients by adding intermediate hop
  - Services can made changes without affecting clients
- Multiple protocol support
  - Gateway format the request/responses into corresponding client's/service's format
- Service wrapping
- Service discovery
- Circuit breaking
- Throttling
- Transformation
- Faster communicatrion between client and server
  - Frontend application will communicate with SPOC(single point of control) instead of issuing multiple API calls.
  - Aggregate multiple responses from different microservices into one
- Load Balancing
- Extended Monitoring & Logging

## Disadvantages:
- Added another hop
- Additional component - need to deployed & maintained
- SPOF, single point of failure


API gateway advantages overcome the disadvantages.

## Features:
1. Reverse proxy or gateway routing
2. Requests aggregation
3. Gateway offloading (Offloading the common functionality of microservices in gateway)
4. 

API gateway shuold includes:
- High availability
- Circuit breaker
  - Fail fast for the services that is down or takes too long to respond
- Handle large load
- Easily scale



### **API gateways Examples**:
- Amazon - Amazon API gateway
- Google - APIGee
- Azure  - 
- Linux Foundation  - Kong
- Envoy  - 
- Gloo   - 





