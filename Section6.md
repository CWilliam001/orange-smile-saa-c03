Private vs Public IP (IPv4)
    IPv4 is still the most common format used online
    IPv6 is newer and solves problesm for the IoT

Public IP
    Machine can be identified on the Internet (www)
    Must be unique across the whole web (not two machines can have the same public IP)
    Can be geo-located easily

Private IP
    Machine can only be identified on a private network only
    The IP must be unique across the private network
    BUT two different private networks (two companies) can have the same IPs
    Machines connect to www using an Internet Gateway (a proxy)
    Only a specified range of IPs can be used as private IP

Elastic IPs
    When you stop and then start an EC2 instance, it can change its public IP
    If you need to have a fixed public IP for your instance, you need an Elastic IP
    An Elastic IP is a public IPv4 IP you own as long as you don't delete it
    You can attach it to one instance at a time
    With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account
    You can only have 5 Elastic IP in your account (you can ask AWS to increase that)
    Overall, try to avoid using Elastic IP:
        They often reflect poor architectural decisions
        Instead, use a random public IP and register a DNS name to it
        Use a Load Balancer and dont use a public IP


Placement Groups
    Sometimes you want control over the EC2 Instance placement strategy
    When you create a placement group, you specify one of the following strategies for the group
        Cluster
            Clusters Instances into a low-latency group in a single AZ
            Great network between instances with Enhanced Networking enabled
            If the AZ fails, all Instances fails at the same time
            Use case:
                Big Data job that needs to complete fast
                Application that needs extremely low latency and high network throuhgput
        Spread
            Spreads Instances across underlying hardware (max 7 Instances per group per AZ) - critical applications
            Can span across AZ
            Reduced risk is simultaneous failure
            EC2 Instances are on different per placement group
            Limited to 7 Instances per AZ per placement group
            Use case:
                Application that needs to maximize high availability
                Critical Applications where each Instance must be isolated from failure from each other
        Partition
            Spreads Instances across many different partitions (which rely on different sets of racks) within an AZ. Scales to 100s of EC2 Instances per group
            The Instances in a partition do not share racks with the instances in the other partitions
            A partition failure can affect many EC2 but won't affect other partitions
            EC2 Instances get access to the partition information as metadata
            Use case:
                Hadoop, Cassandra, Kafka

Elastic Network Interfaces (ENI)
    Logical component in a VPC that represents a Virtual Network Card
    The ENI can have the following attributes
        Primary private IPv4, one or more secondary IPv4
        One Elastic IP (IPv4) per private IPv4
        One Public IPv4
        One or more security groups
        MAC address
    Can create ENI independently and attach them on the fly (move them) on EC2 Instances for failover
    Bound to a specific AZ

EC2 Hibernate
    Stop - the data on disk (EBS) is kept intact in the next start
    Terminate - any EBS volumes (root) also set-up to be destroyed is lost
    On start, the following happens:
        First start: OS boots & EC2 User Data script is run
        Following starts: The OS boots up then your application starts, caches get warmed up, and that can take time
    The in-memory (RAM) state is preserved
    The instance boot is much faster (OS is not stopped / restarted)
    Under the hood: The RAM state is written to a file in the root EBS volume
    The root EBS volume must be encrypted
    Use case:
        Long-running processing
        Saving the RAM state
        Services that take time to initialize
    Supported Instance Families:
        Compute Optimized: C3, C4, C5
        Storage Optimized: I3
        General Purpose: M3, M4, T2, T3
        Memory Optimized: R3, R4
    Instance RAM Size must be less than 150GB
    AMI: Amazon Linux 2, Linux AMI, Ubuntu, RHEL, CentOS & Windows
    Root Volume: Must be EBS, encrypted, not Instance store and large
    Available for On-Demand, Reserved and Spot Instances
    An Instance can NOT be hibernated more than 60 days