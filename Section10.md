Domain Name System (DNS)
    Trnaslates the human friendly hostnames into machine IP address
    Backbone of the Internet
    Hierarchical naming structure
        .com
        example.com
        www.example.com
        api.example.com

Domain Registrar: Hostinger, Amazon Route 53, GoDaddy
DNS Records: A, AAAA, CNAME, NS
Zone File: Contains DNS records
Nameserver: Resolves DNS queries (Authoritative or Non-Authoritative)
Top Level Domain (TLD): .com, .us, .in, .gov, .org, ...
Second Level Domain (SLD): amazon.com, google.com
    eg: http://api.www.example.com
    http: Protocol
    api: Fully Qualified Domain Name (FQDN)
    www: Sub Domain
    example: SLD
    com: TLD



Route 53
    A highly available, scalable, fully managed and Authoritative DNS
        Authoritative = The customer cna update the DNS records
    Serve as a Domain Registrar too
    Ability to check the health of your resources
    Provides 100% availability SLA




Each Records contains;
    Domain/Subdomain Name: example.com
    Record Type: A, AAAA
    Value: 233.152.0.184
    Routing Policy: How Route 53 responds to queries
    Time To Live (TTL): Amount of time the record cached at DNS Resolvers
    Supports the following DNS Record types:
        Common: A, AAAA, CNAME, NS
        Advanced: CAA, DS, MX, NAPTR, PTR, SOA, TXT, SPF, SRV

Record Types
    A: Maps a hostname to IPv4
    AAAA: Maps a hostname to IPv6
    CNAME: Maps a hostname to another hostname
        The target is a domain name which must have an A or AAAA record
        Can't create a CNAME record for the top node of a DNS Namespace (Zone Apex)
    NS: Nameservers for the Hosted Zone



Hosted Zones
    A container for records that define how to route traffic to a domain and its subdomains
    Public Hosted Zones: Contains records that specify how to route traffic on the Internet (Public Domain Names)
        eg: application1.mypublicdomain.com
    Private Hosted Zones: Contain records that specify how your route traffic within one or more VPCs (Private Domain Names)
        eg: application1.company.internal
    Cost $0.50 per month per Hosted Zone
    Domain Name might cost minimum $12 per year


TTL
    Client send request to Route 53 to get the IP address of example.com
    Then Route 53 respond it
        example.com: 12.34.56.78
        TTL: 300ms
    High TTL (24h)
        Less traffic on Route 53
        Possibly outdated records because the update duration is too long to wait
    Low TTL (1min)
        More traffic on Route 53 (which will cost a lot of money)
        Records are outdated for less time
        Easy to change records



CNAME vs Alias
    CNAME: app.mydomain.com => blabla.anything.com
        Only for NON ROOT DOMAIN (aka.something.mydomain.com)
    Alias: app.mydomain.com => resource.amazonaws.com
        Only map to AWS resource app.mydomain.com => mylab.us-east-1.elb.amazonaws.com
        An extension to DNS functionality
        Automatically recognizes changes in the resource IP address
        Unlike CNAME, it can be used for the top node of a DNS Namespace (Zone Apex)
            eg: example.com
        Alias Record always of type A, AAAA for AWS resources (IPv4, IPv6)
        Cant set TTL




Alias Records Targets
    ELB
    CloudFront Distributions
    API Gateway
    Elastic Beanstalk
    S3 websites
    VPC Interface Endpoints
    Global Accelerator accelerator
    Route 53 record in the same Hosted Zone
    Cannot set an ALIAS record for an EC2 DNS name





Routing Policies
    Define how Route 53 responds to DNS queries
    DNS does not route any traffic, it only responds to the DNS queries
    Supports the following Routing Policies
        Simple
            Typically, route traffic to a single resource
            Can specify multiple values in the same record
            If multiple values are returned, a random one is chosen by the client
            When Alias enabled, specify only one AWS resource
            Cant be associated with Health Checks
        Weighted
            Control the % of the requests that go to each specific resource
            Assign each record a relative weight: traffic (%) = Weight for a specific record / Sum of all the weights for all records
            DNS records must have the same name and type
            Can be associated with Health Checks
            Used for load balancing between regions, testing new application versions
            Assign a weight of 0 to a record to stop sending traffic to a resource
            If all records have weight of 0, then all records will be returned equally
        Latency Based
            Redirect to the resource that has the least latency close to us
            Super helpful when latency for users is a priority
            Latency is based on traffic between users and AWS Regions
            Germany users may be directed to the US (if that's the lowest latency)
            Can be associated with Health Checks (has a failover capability)
        Health Checks
            Only for public resources
            Automated DNS Failover:
                Health checks that monitor an endpoint (Application, Server, other AWS resource)
                Health checks that monitor other health checks (Calculated Health Checks)
                Health checks that monitor CloudWatch Alarms (Full control)
            Health Checks are integrated with CloudWatch metrics
        Failover
            One of the Disaster Recovery method
                If Pirmary resource Health Check is failed then route to the other resource
        Geolocation
            Different from Latency Based
            Routing based on user location
            Specify location by Continent, Country or by US State (if there's overlapping most precise location selected)
            Should create a "Default" record i(in case there's no match on location)
            Use cases: website localization, restrict content distribution, load balancing
            Can be associated with Health Checks
        Geoproximity (using Route 53 Traffic Flow feature)
            Route traffic to your resources based on the geographic location of users and resources
            Ability to shift more traffic to resources based on the defined bias
            To change the size of the geographic region, specify bias values:
                To expand (1 to 99): More traffic to the resource
                To shrink (-1 to -99): Less traffic to the resource
            Resources can be:
                AWS resources (specify AWS region)
                Non AWS resources (specify Lattitude and Longitude)
            Must use Route 53 Traffic Flow (advanced) to use this feature
        IP-Based
            Routing is based on client's IP addresses
            You provide a list of CIDRs for your clients and the corresponding endpoints/locations (user-IP-to-endpoint mappings)
            Use cases: Optimize performance, reduce network costs
                eg: Route end users from a particular ISP to a specific endpoint
        Multi-Value Answer
            Use when routing traffic to multiple resources
            Route 53 return multiple values/resources
            Can be associated with Health Checks (return only values for healthy resources)
            Up to 8 healthy records are returned for each Multi-Value query
            Multi-Value is not a substitute for having an ELB



Domain Registrar vs DNS Service
    You buy or register your domain name with a Domain Registrar typically by paying annual charges
    The Domain Registrar usually provides you with a DNS service to manage your DNS records
    But you can use another DNS service to manage your DNS records
        eg: Purchase the domain from Hostinger and use Route 53 to manage your DNS records
    If you buy your domain on a 3rd party registrar, you can still use Route 53 as the DNS Service Provider
        1.  Create a Hosted Zone in Route 53
        2.  Update NS Records on 3rd party website to use Route 53 Name Servers
    Domain Registrar != DNS Service
    But every Domain Registrar usually comes with some DNS features





Hybrid DNS
    Automatically answers DNS queries for:
        Local domain names for EC2 instances
        Records in Private Hosted Zones
        Records in public Nameservers
    Resolving DNS queries between VPC (Route 53 Resolver) and your networks (other DNS Resolvers)
    Networks can be:
        VPC itself / Peered VPC
        On-premises Network (connected through Direct Connect or AWS VPN)
    Inbound Endpoint
        Allows your DNS Resolvers to resolve domain names for AWS resources (eg: EC2 Instances) and records in Private Hosted Zones
    Outbound Endpoint
        Route 53 Resolver forwards DNS queries to your DNS Resolvers