Disaster Recovery (DR)
    Any event that has a negative impact on a company's business continuity or finances is a dister
    DR is about preparing for an recovering from a disaster
    What kind of DR?
        On-premise => On-premise: Traditional DR, and very expensive
        On-premise => AWS Cloud: Hybrid recovery
        AWS Cloud Region A => AWS Cloud Region B
    Need to define two terms:
        Recovery Point Objective (RPO)
        Recovery Time Objective (RTO)

RPO - How much of data loss are you willing to accept in case a disaster happens
RTO - The amount of downtime your application

DR Strategies
    Backup & Restore (High RPO)
    Pilot Light 
        A small version of the app is always running in the cloud
        Useful for the critical core (pilot light)
        Very similar to Backup and Restore
        Faster than Backup and Restore as critical systems are already up
    Warm Standby
        Full system is up and running, but at minimum size
        Upon disaster, we can scale to production load
    Hot Site / Multi Site Approach
        Very low RTO (minutes or seconds) - very expensive
        Full Production Scale is running AWS and On Premise

DR Tips
    Backup 
        EBS Snapshots, RDS automated backups / Snapshots, etc...
        Regular pushes to S3 / S3 IA / Glacier, Lifecycle Poicy, Cross Region Replication
        From On-Premise: Snowbal or Storage Gateway
    High Availability
        Use Route 53 to migrate DNS over from Region to Region
        RDS Multi-AZ, ElastiCache Multi-AZ, EFS, S3
        Site to Site VPN as a recovery from Direct Connect
    Replication
        RDS replication (Cross Region), AWS Aurora + Global Databases
        Database replication from on-premise to RDS
        Storage Gateway
    Automation
        CloudFormation / Elastic Beanstalk to re-create a whole new environment
        Recover / Reboot EC2 instances with CloudWatch if alarms fail
        AWS Lambda functions for customized automations
    Chaos
        Netflx has a "simian-army" randomly terminating EC2

Database Migration Service (DMS)
    Quickly and securely migrate databases to AWS, resilient, self healing
    The source database remains available during the migration
    Support
        Homogeneous migrations: Oracle to Oracle
        Heterogeneous migrations: Microsoft SQL Server to Aurora
    Continuous Data Replication using CDC
    You must create an EC2 Instance to perform the replication tasks

DMS Sources and Targets
    Sources
        On-Premises and EC2 Instances databases: Oracle, MS SQL Server, MySQL, MariaDB, PSQL, MongoDB, SAP, DB2
        Azure: Azure SQL Database
        Amazon RDS: All including Aurora
        S3
        DocumentDB
    Targets
        On-Premises and EC2 Instances database: Oracle, MS SQL Server, MySQL, MariaDB, PSQL, SAP
        RDS
        Redshift, DynamoDB, S3
        OpenSearch Service
        Kinesis Data Streams
        Apache Kafka
        DocumentDB & Amazon Neptune
        Redis & Babelfish

AWS Schema Conversion Tool (SCT)
    Convert your Database's Schema from one engine to another
    Example OLTP: (SQL Server or Oracle) to MySQL, PSQL, Aurora
    Example OLAP: (Teradata or Oracle) to Amazon Redshift
    You dont need to use SCT if you are migrating the same DB engine
        On-Premise PSQL => RDS PSQL
        The DB engine is still PSQL (RDS is the platform)

DMS - Multi-AZ Deployment
    When Multi-AZ enabled, DMS provisions and maintains a synchronously stand replica in a different AZ
    Advantages
        Provides Data Redundancy
        Eliminates I/O freezes
        Minimizes latency spikes

RDS & Aurora MySQL Migrations
    RDS MySQL to Aurora MySQL
        Option 1: DB Snapshots from RDS MySQL restored as MySQL Aurora DB
        Option 2: Create an Aurora Read Replica from your RDS MySQL, and when the replication lag is 0, promote it as its own DB cluster (can take time and cost)
    External MySQL to Aurora MySQL
        Option 1:
            Use Percona XtraBackup to create a file backup in S3
            Create an Aurora MySQL DB from S3
        Option 2:
            Create an Aurora MySQL DB
            Use the mysqldump utility to migrate MySQL into Aurora (slower than S3 method)
    Use DMS if both database are up and running

RDS & Aurora PSQL Migrations
    RDS PSQL to Aurora PSQL
        Option 1: DB Snapshots from RDS PSQL restored as PSQL AuroraDB
        Option 2: Create an Aurora Read Replica from your RDS PSQL, and when the replication lag is 0, promote it as its own DB cluster (can take time and cost)
    External PSQL to Aurora PSQL
        Create a backup and it it in S3
        Import it using the aws_s3 Aurora extension
    Use DMS if both database are up and running

On-Premise startegy with AWS
    Ability to download Amazon Linux 2 AMI as a VM (.iso format)
        VMWare, KVM, VirtualBox (Oracle VM), Microsoft Hyper-V
    VM Import / Export
        Migrate existing applications into EC2
        Create a DR repository strategy for your on-premise VMs
        Can export back the VMs from EC2 to on-premise
    AWS Application Discovery Service
        Gather information about your on-premise servers to plan a migrationo
        Server utilization and dependency mappings
        Track with AWS Migration Hub
    AWS Database Migration Service (DMS)
        Replicate On-Premise => AWS, AWS => AWS, AWS => On-Premise
        Works with various database technologies (Oracle, MySQL, DynamoDB, etc...)
    AWS Server Migration Service (SMS)
        Incremental replication of on-premise live servers to AWS


AWS Backup
    Fully managed service
    Centrally manage and automate backups across AWS services
    No need to create custom scripts and manual processes
    Supported services
        EC2 / EBS
        S3
        RDS (all DBs engines) / Aurora / DynamoDB
        DocumentDB / Neptune
        EFS / Fsx (Lustre & Windows File Server)
        Storage Gateway (Volume Gateway)
    Supports cross-region backups
    Supports cross-account backups
    Supports PITR for supported services
    On-Demand and Scheduled backups
    Tag-based backup policies
    You create backup policies known as Backup Plans
        Backup frequency (every 12 hours, daily, weekly, monthly, cron expression)
        Backup window
        Transition to Cold Storage (Never, Days, Weeks, Monthns, Years)
        Retention Period (Always, Days, Weeks, Months, Years)

AWS Backup Vault Lock
    Enforce a Write ONce Read Many (WORM) state for all the backups that you store in your AWS Backup Vault
    Additional layer of defense to protect your backups against:
        Inadvertent or malicious delete operations
        Updates that shorten or alter retention periods
    Even the root user cannot delete backups when enabled

AWS Application Discovery Service
    Plan migration projects by gathering information about on-premises data centers
    Server utilization data and dependency mapping are important for migrations
    Agentless Discovery (AWS Agentless Discovery Connector)
        VM inventory, configuration, and performance history such as CPU, memory, and disk usage
    Agent-based Discovery (AWS Application Discovery Agent)
        System configuration, system performance, running processes, and details of the network connections between systems
    Resulting data can be viewed within AWS Migration Hub

AWS Applicaiton Migration Service (MGN)
    Lift-and-shift (rehost) solution which simplify migrating applications to AWS
    Converts your physical, virtual, and cloud-based servers to run natively on AWS
    Supports wide range of platforms, OS, and databases
    Minimal downtime, reduced costs

Transferring large amount of data into AWS
    Imagine there are 200TB data to be transfer to cloud. And we have a 100MB/s Internet connection
    Over the Internet / Site-to-site VPN
        Immediate to setup
        Will take 200TB * 1000GB * 8GB/1GB/s = 16,000,000s = 185 days
    Over direct connect 1GB/s
        Long for the one-time setup (over a month)
        Will take 200TB * 1000GB * 8GB/1GB/s = 1,600,000s = 16.5 days
    Over Snowball
        Takes about 1 week for the end-to-end transfer
        Can be combined with DMS
    For on-going replication / transfers: Site-to-Site VPN or DX with DMS or DataSync

VMware Clud on AWS
    Some customers use VMware Cloud to manage their on-premises Data Center
    They want to extend the Data Center capacity to AWS, but keep using the VMware Cloud software
    ...Enter VMware Cloud on AWS
    Use case
        Migrate your VMware vSphere-based workloads to AWS
        Run your production workloads across VMware vSphere-based private, public, and hybrid cloud environments
        Have a disaster recover strategy