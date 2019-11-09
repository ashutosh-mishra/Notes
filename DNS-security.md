
## DNS, Security Issues and solutions

DNS - Use to get IP address corresponding to a site name.

Domain name resolution - Process of converting a domain name to an IP address.

DNS Resolver - 
A DNS resolver is known by many names:
1. Full resolver (in contrast to stub resolver)
2. DNS recursor
3. Recursive DNS server
4. Recursive resolver

A resolver is a overall subsystem that does query resolution. User programs uses resolver to access name servers.

Basically 3 entities are involved while resolving DNS query.
1. Stub resolver

   Small DNS client, provided by OS, which received request from user programs and forward that to recursive resolver.
2. Recursive resolver

   Resolver is a first stop in DNS query. It acts as a middleman between stub resolver and a DNS server. 
   Resolver will either respond with cached data or send a request to a root name server followed by TLD server and the last request to authoritative server.
   Resolver to choose - By-default OS will just use the resolver which network recommends. When a system connects to network and gets it’s IP, network recommends a resolver to use.

3. Name servers (Authoritative server)

   Maintains mapping regarding site name and corresponding public IP address.

DNS server also be called as Name server. Examples of DNS resolver are 8.8.8.8(Google public DNS server) and 1.1.1.1 (cloud flare public DNS server)

**DNS servers** - 
There are 4 different types of DNS servers:

*Recursive resolvers, root name server, TLD servers and Authoritative servers.*

1. **Recursive resolver <==> Root nameserver**

   First, resolver talks to a root DNS (by-default 13 root DNS are configured with each resolver). It asks root DNS where it can find more info about top-level domain. There are multiple copies of all 13 root DNS all over the world.

   Root DNS will give the resolver an address of a server which knows more about requested domain.

2. **Recursive resolver <==> TLD server**

   This next server is known as Top Level Domain(TLD) server. TLD knows all about second level domains.
   Management of TLD servers will be handled by Internet Assigned Numbers Authority(IANA).

   TLD name server, will provide another server ip, which contains more info about subdomain.
   TLS servers further classified into 2 main groups:
   - Generic top-level domains: These are generic domains, that are not country-specific .com. .org. .edu, .gov.
   - Country code top-level domains: These include any domains that are specific to a country or a state .uk, .in, .us, .jp.

3. **Recursive resolver <==> Authoritative server**

   Authoritative server contains information specific to the domain name.
   It can either return the IP address of a server found in the DNS A record or return an alias domain if domain has CNAME (alias record).
   In case of CNAMe record response, resolver need to perform the new query corresponding to this domain.


**Example of a DNS record**

*Example of an NS record:*

| example.com | record type: | value: | TTL |
|-------------|--------------|--------|-----|
| @           | NS           | ns1.exampleserver.com | 21600 |

*Example of a CNAME record:*

| blog.example.com | record type: | value: | TTL |
|------------------|--------------|--------|-----|
| @                | CNAME        | is an alias of example.com | 32600 |


|NAME                   | TYPE  | VALUE           |
|-----------------------|-------|-----------------|
|bar.example.com.       | CNAME | foo.example.com.|
|foo.example.com.       | A     | 192.0.2.23      |




For Ex: **To resolve en.wikipedia.org**

First
- The resolver asks the Root DNS where it can find more info about addresses in the .org top-level domain.
The Root DNS will give the resolver an address for a server(TLD server) that knows about .org addresses.
- TLD server knows about all of the second-level domains that end with .org. TLD name server will tell the resolver to ask Wikipedia’s name server.
- Wikipedia’s name server is what’s called the authoritative server. It knows about all of the domains under wikipedia.org. So this server knows about en.wikipedia.org, and other subdomains like the German version, de.wikipedia.org.


Every resolver can cache DNS response, to increase performance, also known as caching DNS server.



### Issues:
1. Tracking

   Every intermediate router knows about requested domain and your IP address.Someone can maintain this data, about browsing history.

2. Spoofing

   Someone in-between, can spoof the actual response and return wrong IP address of site. This way, they can block you from visiting the real site or send you to a scam one.

There are three threats here:
1.	You could end up using an untrustworthy resolver that tracks your requests, or tampers with responses from DNS servers.
2.	On-path routers can track or tamper in the same way.
3.	DNS servers can track your DNS requests.


Information leakages can occur at following places:
- the communications links and devices between the stub resolver and the recursive resolver,
- the recursive resolver;
- the communications links and devices between the recursive resolver and the authoritative DNS servers, and;
- the authoritative nameservers

Recursive resolvers normally re-send the same original query in each step of the iterative resolution

Securing link between stub resolver and recursive resolver is simple, does’t require much changes.
But securing link between recursive resolver and TLD, all authoritative name servers require these changes in many servers all over the world. Adding security in authoritative servers might require additional computing power to support crypto operations, which might also affect performance.



### Solutions:
1. DNS Query Name Minimisation

   Minimize the amount of information sent in DNS queries.
   For root servers and TLD, send only .com and example.com as domain instead of sending whole www.example.com name.

2. Trusted Recursive Resolver(TRR)


3. DNSSec: Only allows authentication of DNS responses, does not provide confidentiality of DNS transactions.

4. DNSCrypt: Provide confidentiality between stub resolvers and recursive resolvers.

5. DNS over HTTPS(DOH)

   DNS requests will be sent via port 443 as encrypted HTTPS traffic, instead of cleartext via port 53. DNS traffic will be send as HTTPS traffic and connection with resolver will be secured as any other HTTPS traffic.


DNS name lookup:
```
$ nslookup example.com
Server:		192.168.3.1
Address:	192.168.3.1#53

Non-authoritative answer:
Name:	example.com
Address: 93.184.216.34
```


```
$ dig www.example.com

; <<>> DiG 9.10.6 <<>> www.example.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24357
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.example.com.		IN	A

;; ANSWER SECTION:
www.example.com.	15344	IN	A	93.184.216.34

;; Query time: 46 msec
;; SERVER: 192.168.3.1#53(192.168.3.1)
;; WHEN: Wed Oct 23 11:31:48 IST 2019
;; MSG SIZE  rcvd: 60
```




