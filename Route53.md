# Route53

- DNS is on port 53 , hence the name route53
- **ELBs do not have pre-defined IPv4, always have to resolve using DNS name**
- **by default, R53 can manage a max of 20 domain names, but can be increased by AWS support**
- **R53 is NOT inside AWS backbone**

### DNS 101
- convert human friendly domain names to IP
- IPv4 and IPv6
- IPv4 - 32 bits - over 4 billion address
- IPv6 - 128bits - created as IPv4 was running out
- TLD
    - .com, .edu, .gov, .gov.au
    - controlled by **Internet Assigned Numbers Authority (IANA)**
- second level domain - .gov in .gov.au
- **domain names have to be unique under a TLD**
- domain registrars
    - authority to assign domain names under TLD
    - TLDs are registered with InterNIC, a service if ICANN
    - **each domain is registered with a central db called WhoIS database**
    - e.g - Amazon, GoDaddy
- Start of Authority (SOA)
    - start of the domain name
    - stores name of the server that supplies data for zone
    - admin of the zone
    - current version of data file
    - TTL of data file
- Name Server Records
    - used by TLD servers to route traffic to Content Servers
    - Content Servers have authoritative DNS records
- A record
    - stands for **Address Record**
    - used to translate domain name to IP
- TTL -
    - time in secs the DNS record is cached in local computer or Resolving server
    - lower TTL means changes are propagated faster
    - **default TTL for DNS is 48hrs**
- CName
    - stands for **Canonical Name**
    - used to resolve one domain name to another
    - google.com and m.google.com can point to the same IP
    - here mm.google.com is mapped to google.com
    - **cannot be used for naked domain names (URL without www or mobile)**
    - **only A record or Alias record should be used**
- Alias Record
    - **provides R53 specific extension of DNS functionality**
    - used to map resource records in your hosted zone to ELB, CloudFront distribution, S3 buckets etc
    - similar to CName - maps one DNS name (www.example.com) to another DNS name - elb1234.elb.amazonaws.com
    - **given a choice always choose Alias records instead of CName**
    - **can also point to resources in other account manually by entering the ARN**
- Other DNS types
    - MX Records - used for mail servers
    - PTR Records - reverse of A record - translate domain name from IP

### Routing Policies on AWS
- Simple Routing
    - only one record with multiple IPs
    - if there are multiple records, then R53 returns all values in random order
    - **does not support health check of the resources**
- Weighted Routing
    - route traffic based on weights assigned
    - eg - 10% traffic to US-east-1 and 90% to US-east-2
    - health checks can be set up for individual record sets
    - if health check fails, then it will be removed by R53
    - can set up SNS notification if health check fails
- Latency based Routing
    - route traffic based on lowest latency for end user
    - record set with lowest latency with be chosen to respond
- Failover Routing
    - used to create an active/passive set up
    - like a DR set up
    - when active goes down, passive site will take on traffic
    - primary site health is monitored using health check
- Geolocation Routing
    - traffic is routed based on location of users
    - e.g European users (who use GBP) will be routed to European EC2 instances
- Geoproximity Routing
    - traffic is routed based on geographic location of user and resources
    - traffic can also be increased or decreased to a resource by specifying a **bias**
    - **a bias is a value used to shrink or expand size of geographic region**
    - **to use this routing, R53 Traffic Flow should be used**
- Multivalue Answer Routing
    - supports returning multiple resources like IPs in response to DNS queries
    - allows you to check health of each resource in the record
    - removes resources that are unhealthy and returns only health resources
    - **same as Simple Routing except allowing health check on resources**
