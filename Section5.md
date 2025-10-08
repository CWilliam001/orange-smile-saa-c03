EC2 - 1

Instance Types

m5.2xlarge

m = Instance class
5 = Generation
2xlarge = Size of instance class => bigger size = bigger memory & CPU

General Purpose - Balance between Compute, Memory, Networking
M1 | M2 | M3 | M4 | T1 | T2
Compute Optimized - Greater CPU
C1 | C3 | C4
Memory Optimized - Greater Memory
R3 | R4 | X1 | X1e
Storage Optimized - Greater Storage
D2 | H1 | I2 | I3
Acclerated Computing - 
F1 | G3 | P2 | P3
High Performance Computing (HPC)
Hpc6a | Hpc6id | Hpc7a | Hpc7g
Previous Generation
A1


Security Groups
Only contain allow rules reference by IP or by security group
Firewall of EC2 instances
Can be attached into multiple instances

Classic Ports to know
22 = Secure Shell (SSH)
21 = File Transfer Protocol (FTP)
22 = Secure File Transfer Protocol (SFTP)
80 = Hypertext Transfer Protocol (HTTP)
443 = Hypertext Transfer Protocol Secure (HTTPS)
3389 = Remote Desktop Protocol (RDP)



Instance Purchasing Options

On-Demand Instances
    Short workload, predictable pricing, pay by 
    Pay for what you use
    Has the highest cost but no upfront payment
    No long term commitment
    Recommended for short-term and un-interrupted workloads, where you can't predict how the application will behave
Reserved (1 or 3 years)
    Long workloads
    Convertible Reserved Instances - long workloads with flexible instances
    Up to 72% discount compared to On-Demand
    Reserve a specific instance attributes
    The more duration you reserve will get more discount
    Payment Option (No Upfront, Partial Upfront, All Upfront)
    Can change EC2 instance type, instance family, OS scope and tenancy
    Upto 66% discount
Saving Plans (1 or 3 years)
    Commitment to an amount of usage, long workload
    Up to 72% discount same as Reserved
    Commit certain type of usage
    Usage beyond EC2 Saving Plans is billed at the On-Demand price
    Locked to a specific instance family & AWS region
Spot Instances
    Short workloads, cheap, can lose instances (less reliable)
    Up to 90% discount compared to On-Demand
    Instances that you can lose at any point of time if your max price is less than the current spot price 
    Most cost-efficient instances in AWS
    The workloads that are resilient to failure
        Batch jobs
        Data analysis
        Image processing
        Any distributed workloads
        Workloads with a flexible start and end time
Dedicated Host
    Book an entire physical server, control instance placement
    Get an actual physical server with EC2 instance fully dedicated to your use case
    Allows you address compliance requirements and use your existing server-bound software licenses
    Purchasing Options
        On-Demand - Pay per second for active dedicated host
        Reserved - 1 or 3 years
    The most expensive option
Dedicated Instances
    No other customers will share your hardware
    May share hardware with other instances in same account
    No control over instance placement
Capacity Reservations
    Reserve capacity in a specific AZ for any duration
    Have access to EC2 capacity when you need it
    No time commitment & no billing discounts
    Combine it with regional reserved instances and savings plans to benefit from billing discounts
    Charged at On Demand rate whethere you run instances or not
    Short term, uninterrupted workloads that needs to be in a specific AZ





Spot Instance can define a max spot price and get the instance while current spot price < max
The hourly spot price varies based on offer and capacity
If the current spot price > max price you can choose to stop or terminate your instance with a 2 minutes grace period

Spot Fleet - Get a set of Spot Instances + On-Demand Instances
The Spot Fleet will try to meet the target capacity with price constraints
Lowest Price
Diversified
Capacity Optimized
Price Capacity Optimized