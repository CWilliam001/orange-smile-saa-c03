Classless Inter-Domain Routing (CIDR) - A method for allocating IP addresses
Used in Security Groups rules and AWS networking in general

CIDR consists of two components
    Base IP
        Represents an IP contained in the range (XX.XX.XX.XX)
        Example 192.168.10.1
    Subnet Mask
        Defines how many bits can change in the IP
        Example: /0, /24, /32
            /8 <-> 255.0.0.0
            /16 <-> 255.255.0.0

Public vs Private IP (IPv4)
    The Internet Assigned Numbers Authority (IANA) established certain blocks of IPv4 addresses for the use of private (LAN) and public (Internet) address
    Private IP can only allow certain values:
        10.0.0.0 - 10.255.255.255 (10.0.0.8) <- in big networks
        172.16.0.0 - 172.31.255.255 (172.16.0.0/12) <- AWS default VPC in that range
        192.168.0.0 - 192.168.255.255 (192.168.0.0/16) <- Home networks
    All the rest of the IP addresses on the Internet are Public

Virtual Private Cloud (VPC)
    Max 5 per region - soft limit
    Max CIDR per VPC is 5, for each CIDR
        Min size is /28 (16 IP addresses)
        Max size is /16 (65536 IP addresses)
    Your VPC CIDR should NOT overlap with your other networks (eg: corporate)

Subnet (IPv4)
    AWS reserves 5 IP addresses (first 4 & last 1) in each subnet
    These 5 IP addresses are not available for use and can't be assigned to an EC2 Instance
    If you need 29 IP addresses for EC2 Instances
        You can't choose a subnet of size /27 (32 IP addresses, 32 - 5 = 27 < 29)
        Only can 0, 2, 4, 8, 16, 32, 64, 128, 256

Internet Gateway (IGW)
    Allow resources in a VPC connect to the Internet
    It scales horizontally and is highly available and redundant
    Must be created separately from a VPC
    One VPC can only be attached to one IGW and vice versa
    IGW on their own do not allow Internet access
    Route tables must also be edited

Bastion Hosts
    Use Bastion Host to SSH into our private EC2 Instances
    Bastion is in the public subnet which is then connected to all other private subnets
    Bastion Host security group must allow inbound from the Internet on port 22 from restricted CIDR, for example the public CIDR of your corporation
    Security Group of the EC2 Instances must allow the Security Group of the Bastion Host, or the private IP of the Bastion Host

Network Address Translation Instance (NAT)
    Allow EC2 Instances in private subnets to connect to the Internet
    Must be launched in a public subnet
    Must disable EC2 setting: Source / destination Check
    Must have Elastic IP attached to it
    Route Tables must be configured to route traffic from private subnets to the NAT Instance

NAT Gateway (NATGW)
    AWS-managed NAT, higher bandwidth, high availability, no administration
    Pay per hour for usage and bandwidth
    NATGW is created in a specific AZ, uses an Elastic IP
    Can't be used by EC2 instance in the same subnet (only from other subnets)
    Requires an IGW (Private Subnet => NATGW => IGW)
    5GB/s of bandwidth with automatic scaling up to 100GB/s
    No Security Groups to manage / required

NAT Gateway with High Availability
    NAT Gateway is resilient within a single AZ
    Must create multiple NAT Gateways in multiple AZs for fault-tolerance
    There is no cross-AZ failover needed because if an AZ goes down it doesn't need NAT

Network Access Control List (NACL)
    NACL are like a firewall which control traffic from and to subnets
    One NACL per subnet, new subnets are assigned the Default NACL
    You define NACL Rules:
        Rules have a number (1-32766), higher precedence with a lower number
        First rule match will drive the decision
        Example: if you define #100 ALLOW 10.0.0.10/32 and #200 DENY 10.0.0.10/32, the IP address will be allowed because 100 has higher precedence over 200
        The last rule is "*" and denies a request in case of no rule match
        AWS recommends adding rules by increment of 100
        Newly created NACLs will deny everything
        NACL are a great way of blocking a specific IP address at the subnet level

Default NACL
    Accepts everything inbound/outbound with the subnets it's associated with
    Do NOT modify the Default NACL, instead create custom NACLs

Ephemeral Ports
    For any two endpoints to establish a connection, they must use ports
    Clients connect to a defined port, and expect a response on an ephemeral port
    Different OS use different port ranges
        Example:
            IANA & Microsoft Windows 10 -> 49152 - 65535
            Many Linux Kernels -> 32768 - 60999

VPC Peering
    Privately connect two VPCs using AWS' network
    Make them behave as if they were in the same network
    Must not have overlapping CIDRs
    VPC Peering connection is NOT transitive (must be established for each VPC that need to communicate with one another)
    You must update route tables in each VPC's subnets to ensure EC2 Instances can communicate with each other
    You can create VPC Peering connection between VPCs in differnt AWS accounts/regions
    You can reference a security group in a peered VPC (works cross accounts - same region)

VPC Endpoints (AWS PrivateLink)
    Every AWS service is publicly exposed (public URL)
    VPC Endpoints (powered by AWS PrivateLink) allows you to connect to AWS services using a private network instead of using the public Internet
    They're redundant and scale horizontally
    They remove the need of IGW, NATGW, ... to access AWS services
    In case of ussues:
        Check DNS Setting Resolution in your VPC
        Check Route Tables

Types of Endpoints
    Interface Endpoints (powered by PrivateLink)
        Provisions an ENI (private IP address) as an entry point (must attach a Security Group)
        Supports most AWS services
        $ per hour + $ per GB of data processed
        Interface Endpoint is preferred access is required from on-premises (Site to Site VPN or Direct Connect), a different VPC or a different region
    Gateway Endpoints
        Provisions a gateway and must be used as a target in a route table (does not use security groups)
        Supports both S3 and DynamoDB
        Cost: Free
        Gateway is most likely going to be preferred all the time at the exam

VPC Flow Logs
    Capture information about IP traffic going into your interfaces
        VPC Flow Logs
        Subnet Flow Logs
        Elastic Network Interface (ENI) Flow Logs
    Helps to monitor & troubleshoot connectivity issues
    Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose
    Captures network information from AWS managed interfaces too: ELB, RDS, ElastiCache, Redshift, WorkSpaces, NATGW, Transit Gateway

VPC Flow Logs - Troubleshot SG & NACL Issues
    Incoming Requests
        Inbound REJECT => NACL or SG
        Inbound ACCEPT, Outbound REJECT => NACL
    Outgoing Requests
        Outbound REJECT => NACL or SG
        Outbound ACCEPT, Inbound REJECT => NACL

VPC Flow Logs - Architectures
    VPC Flow Logs -> CloudWatch Logs -> CloudWatch Contributor Insights -> Top-10 IP addresses
    VPC Flow Logs -> CloudWatch Logs -> Metric Filter -> CloudWatch Alarm -> Alert -> SNS
    VPC Flow Logs -> S3 -> Athena -> QuickSight

AWS Site-to-Site VPN
    Virtual Private Gateway (VGW)
        VPN concentrator on the AWS side of the VPN connection
        VGW is created and attached to the VPC from which you want to create the Site-to-Site VPN connection
        Possibility to customize the Autonomous System Number (ASN)
    Customer Gateway (CGW)
        Software application or physical device on customer side of the VPN connection

Site-to-Site VPN Connections
    Customer Gateway Device (On-premises)
        What IP address to use?
            Public Internet-routable IP address for your Customer Gateway device
            If it's behind a NAT device that's enabled for NAT traversal (NAT-T), use the public IP address of the NAT device
    Important Step: Enable Route Propagation for the Virtual Private Gateway in the route table that is associated with your subnets
    If you need to ping your EC2 instances from on-premises, make sure you add the ICMP protocol on the inbound of your security groups

AWS VPN CloudHub
    Provide secure communication between multiple sites, if you have multiple VPN connections
    Low-cost hub-and-spoke model for primary or secondary network connectivity between different locations (VPN only)
    It's a VPN connection so it goes over the public Internet
    To set it up, connect multiple VPN connections on the same VGW, setup dynamic routing and configure route tables

Direct Connect (DX)
    Provides a dedicated private connectino from a remote network to your VPC
    Dedicated connection must be setup between your DC and DX locations
    You need to setup a Virtual Private Gateway on your VPC
    Access public resources (S3) and privte (EC2) on the same connection
    Use case
        Increase bandwidth throughput - working with large data sets - lower cost
        More consistent network experience - applications using real-time data feeds
        Hybrid Environments (on-prem + cloud)
    Support both IPv4 and IPv6

Direct Connect Gateway
    If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a Direct Connect Gateway

Direct Connect - Connection Types
    Dedicated Connections: 1GB/s, 10GB/s and 100GB/s capacity
        Physical ethernet port dedicated to a customer
        Request made to AWS first, then completed by AWS Direct Connect Partners
    Hosted Connections: 50MB/s, 500MB/s, to 10GB/s
        Connection requests are made via AWS Direct Connect Partners
        Capacity can be added or removed on demand
        1, 2, 5, 10 GB/s available at select AWS Direct Connect Partners
    Lead times are often longer than 1 month to establish a new connection

Direct Connect - Encryption
    Data in transit is not encrypted but is private
    AWS Direct Connect + VPN provides an IPsec-encrypted private connection
    Good for an extra level of security but slightly more complex to put in place

Site-to-Site VPN connection as a backup
    In case Direct Connect fails, you can set up a backup Direct Connect connection (expensive), or a Site-to-Site VPN connection

Transit Gateway
    For having transitive pering between thousands of VPC and on-premises, hub-and-spoke (star) connection
    Regional resource, can work cross-region
    Share cross-account using Resource Access Manager (RAM)
    You can peer Transit Gateways across regions
    Route Tables: Limit which VPC can talk with other VPC
    Works with Direct Connect Gateway, VPN connections
    Supports IP Multicast (not supported by any other AWS service)

Transit Gateway: Site-to-Site VPN ECMP
    Equal-cost multi-path routing (ECMP)
    Routing strategy to allow to forward a packet over multiple best path
    Use case: Create multiple Site-to-Site VPN connections to increase the bandwidth of your connectino to AWS

VPC - Traffic Mirroring
    Allows you to capture and inspect network traffic in your VPC
    Route the traffic to security appliances that you manage
    Capture the traffic
        From (Source) - ENIs
        To (Targets) - an ENI or a Network Load Balancer
    Capture all packets or capture the packets of your interest (optionally, truncate packets)
    Source and Target can be in the same VPC or different VPCs (VPC Peering)
    Use case: Content inspection, threat monitoring, troubleshooting

IPv6 in VPC
    IPv4 cannot be disabled for your VPC and subnets
    You can enable IPv6 (they're public IP addresses) to operate in dual-stack mode
    Your EC2 instances will get at least a private internal IPv4 and a public IPv6
    They can communicate using either IPv4 or IPv6 to the internet through an Internet Gateway

Ipv4 Troubleshooting
    IPv4 cannot be disabled for your VPC and subnets
    So, if you cannot launch an EC2 instance in your subnet
        It's not because it cannot acquire an IPv6 (the space is very large
        It's because there are no available IPv4 in your subnet
    Solution: Create a new IPv4 CIDR in your subnet

Egress-only Internet Gateway
    Used for IPv6 only
    (similar to a NAT Gateway but for IPv6)
    Allow instances in your VPC outbound connections over IPv6 while preventing the Internet to initiate an IPv6 connection to your instances
    You must update the Route Tables

VPC Section Summary
    CIDR - IP range
    VPC - We define a list of IPv4 & IPv6 CIDR
    Subnets - tied to an AZ, we define a CIDR
    Internet Gateway - at the VPC level, provide IPv4 & IPv6 Internet Access
    Route Tables - must be edited to add routes from subnets to the IGW, VPC Peering Connections, VPC Endpoints
    Bastion Host - public EC2 Instance to SSH into, that has SSH connectivity to EC2 instances in private subnets
    NAT Instances - gives Internet acess to EC2 Instances in private subnets. Old, must be setup in a public subnet, disable Source / Destination check flag
    NAT Gateway - managed by AWS, provides scalable Internet access to private EC2 Instances, when the target is an IPv4 address
    NACL - stateless, subnet rules for inbound and outbound, don't forget Ephemeral Ports
    Security Groups - stateful, operate at the EC2 Instance level
    VPC Peering - connect two VPCs with non overlapping CIDR, non-transitive
    VPC Endpoints - provide private access to AWS Services (S3, DynamoDB, CloudFormation, SSM) within a VPC
    VPC Flow Logs - can be setup at the VPC / Subnet / ENI Level, for ACCEPT and REJECT traffic, helps identifying attacks, analyze using Athena or CloudWatch Logs Insights
    Site-to-Site VPN - setup a Customer Gateway on DC, a Virtual Private Gateway on VPC, and Site-to-Site VPN over public Internet
    AWS VPN CloudHub - hub-and-spoke VPN model to connect your sites
    Direct Connect - setup a Virtual Private Gateway on VPC, and establish a direct private connection to an AWS Direct Connect Location
    Direct Connect Gateway - setup a Direct Connect to many VPCs in different AWS regions
    AWS Private Link / VPC Endpoint Services
        Connect services privately from your service VPC to customers VPC
        Doesn't need VPC Peering, public Internet, NAT Gateway, Route Tables
        Must be used with Network Load Balancer & ENI
    ClassicLink - connect EC2-Classic EC2 instances privately to your VPC
    Transit Gateway - transitive peering connections for VPC, VPN & DX
    Traffic Mirroring - copy network traffic from ENIs for further analysis
    Egress-only Internet Gateway - like a NAT Gateway, but for IPv6

Networking Costs in AWS per GB
    Encourage to use Private IP instead of Public IP for good savings and better network performance
    Use same AZ for maximum savings (at the cost of high availability)
    Any incoming traffic going into EC2 Instances is free
    Any traffic between two EC2 Instances in the same AZ is free assuming that they are using private IP to communicate
    Any traffic going to other EC2 Instances at the different AZ within the same region
        Public IP / Elastic IP $0.02 per GB
        Private IP $0.01 per GB
    Any traffic going to other EC2 Instances at the different Region
        Inter-region $0.02

Minimizing egress traffic network cost
    Egress traffic: Outbound traffic (from AWS to outside)
    Ingress traffic: Inbound traffic - from outside to AWS (typically free)
    Try to keep as much Internet traffic within AWS to minimize costs

S3 Data Transfer Pricing - Analysis for USA
    S3 ingress: Free
    S3 to Internet: $0.09 per GB
    S3 Transfer Acceleration
        Faster transfer times (50 to 500% better)
        Additional cost on top of Data Transfer
        Pricing: +$0.04 to $0.08 per GB
    S3 to CloudFront $0.00 per GB
    CloudFront to Internet $0.085 per GB (slightly cheaper than S3)
        Caching capability (lower latency)
        Reduce costs associated with S3 Requests Pricing (7x cheaper with CloudFront)
    S3 Cross Regions Replication: $0.02 per GB

NAT Gateway vs Gateway VPC Endpoint
    Internet Gateway
        $0.045 NAT Gateway / hour
        $0.045 NAT Gateway data processed / GB
        $0.09 Data transfer out to S3 (cross-region)
        $0.00 Data transfer out to S3 (same-region)
    VPC Endpoint
        No cost for using Gateway Endpoint $0.01 Data Transfer in/out (same-region)

Network Protection on AWS
    To protect network on AWS, we've seen
        NACL
        VPC Security Groups
        WAF
        Shield & Shield Advanced
        Firewall Manager
    But what if we want to protect in a sophisticated way our entire VPC

AWS Network Firewall
    Protect your entire VPC
    From Layer 3 to Layer 7 protection
    Any direction, you can inspect
        VPC to VPC traffic
        Outbount to Internet
        Inbound from Internet
        To/From Direct Connect & Site-to-Site VPN
    Internally, the AWS Network Firewall uses the AWS Gateway Load Balancer
    Rules can be centrally managed cross-account by AWS Firewall Manager to apply to many VPCs

Network Firewall - Fine Grained Controls
    Supports 1000s of rules
        IP & port - example: 10000 of IPs filtering
        Protocol - example: Block the SMB protocol for outbound communications
        Stateful domain list rule groups: Only allow outbound traffic to *.mycorp.com or third-party software repo
        General pattern matching using regex
    Traffic filtering: Allow, Drop, or Alert for the traffic that matches the rules
    Active flow inspection to protect against network threats with intrusion-prevention capabilities (like Gateway Load Balancer, but all managed by AWS)
    Send logs of rule matches to Amazon S3, CloudWatch Logs, Kinesis Data Firehose