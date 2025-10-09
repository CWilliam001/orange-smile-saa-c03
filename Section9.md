Relational Database Service (RDS)
    Managed DB service for DB use SQL as a query language
    Allows you to create databases in the cloud that are maanged by AWS
        PSQL
        MySQL
        MariaDB
        Oracle
        Microsoft SQL Server
        IBM DB2
        Aurora (AWS Proprietary database)

Advantage over using RDS vs deploying DB on EC2
    RDS is a managed service
        Automated provisioning, OS patching
        Continuous backups and restore to specifig timestamp (Point in Time Restore (PITR))
        Monitoring dashboards
        Read replicas for improved read performance
        Multi AZ setup for Disaster Recovery (DR)
        Maintenance windows for upgrades
        Scaling capability (vertical and horizontal)
        Storage backed by EBS
    BUT you can't SSH into your instaces

RDS - Storage Auto Scaling
    Helps youn increase storage on your RDS DB instance dynamically
    When RDS detects you are running out of free database storage, it scales automatically
    Avoid manually scaling your database storage
    You have to set Maximum Storage Threshold (maximum limit for DB storage)
    Automatically modify storage if:
        Free storage is less than 10% allocated storage
        Low-storage lasts at least 5 minutes
        6 hours have passed since last modification
    Useful for applications with unpredictable workloads
    Supports all RDS database engines

RDS Read Replicas for read scalability
    Scale your reads
    Up to 15 Read Replicas
    Within AZ, Cross AZ, or Cross Region
    Replication is ASYNC, so reads are eventually consistent
    Replicas can be promoted to their own DB
    Applications must update the connection string to leverage read replicase
    
RDS Read Replicas - Use Case
    You have a production database that is taking on normal load
    You want to run a reporting application to run some analytics
    You create a Read Replica to run the new workload there
    The production application is unaffected
    Read replicas are used for SELECT (=read) only kind of statemetns (not INSERT, UPDATE, DELETE)

RDS Read Replicas - Network Cost
    In AWS there's a network cost when data goes from on AZ to another
    For RDS Read Replicas within the same region, you don't pay that fee

RDS Multi AZ (DR)
    SYNC replication
    One DNS name - automatic app failover to standby
    Increase availability
    Failover in case of loss of AZ, loss of network, instance or storage failure
    No manual intervention in apps
    Not used for scaling
    Note: The Read Replicas be setup as Multi AZ for DR

RDS - From Single-AZ to Multi-AZ
    Zero downtime operation (no need to stop the DB)
    Just click on "modify" for the database
    The following happens internally:
        A snapshot is taken
        A new DB is restored from the snapshot in a new AZ
        Syncrhonization is established between the two databases

RDS Custom
    Managed Oracle and Microsoft SQL Server Database with OS and database customization
    RDS: Automates setup, operation, and scaling of database in AWS
    Custom: access to the underlying database and OS so you can:
        Configure settings
        Install patches
        Enable native features
        Access the underlying EC2 Instance using SSH or SSM Session Manager
    De-activate Automation Mode to perform your customization, better to take a DB snapshot before

RDS vs RDS Custom
    RDS: Entire database and the OS to be managed by AWS
    RDS Custom: Full admin access to the underlying OS and the database

Amazon Aurora
    A proprietary technology from AWS (not open sourced)
    PSQL and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a PSQL or MySQL database)
    Aurora is "AWS cloud optimized" and claims 5x performance improvement over MySQL on RDS, and 3x the performance of PSQL on RDS
    Aurora storage automatically grows in increments of 10GB, up to 128TB
    Aurora can have up to 15 replicas and the replication process is faster than MySQL (sub 10ms replica lag)
    Failover in Aurora is instaneous. It's HA native
    Aurora costs more than RDS (20% more) - but is more efficient

Aurora High Availability and Read Scaling
    6 copies of your data across 3 AZ:
        4 copies out of 6 needed for writes
        3 copies out of 6 need for reads
        Self healing with peer-to-peer replication
        Storage is striped across 100s of volumes
    One Aurora Instance takes writes (master)
    Automated failover for master in less than 30 seconds
    Master + up to 15 Aurora Read Replicas serve reads
    Support for Cross Region Replication

Features of Aurora
    Automatic fail-over
    Backup and Recovery
    Isolation and Security
    Industry compliance
    Push-button scaling
    Automated Patching with Zero Downtime
    Advanced Monitoring
    Routine Maintenance
    Backtrack: restore data at any point of time without using backups

Aurora Replicas - Auto Scaling
    Add Aurora Replicas when there are too many request on the reader endpoint that causing increasing CPU usage
    The new created Aurora Replicas reader endpoint will extended to cover these new replicas

Aurora - Custom Endpoints
    Imagine there are 4 read replicase but 2 of them are db.r3.large and 2 of them are db.r5.2xlarge
    The reason to do this is wanna define a subset of Aurora Instances as a Custom Endpoint
    Example: Run analytical queries on specific replicas
    The Reader Endpoint is generally not used after defining Custom Endpoints

Aurora Serverless
    Automated database instantiation and auto-scaling based on actual usage
    Good for infrequent intermittent or unpredictable workloads
    No capacity planning needed
    Pay per second, can be more cost-effective

Global Aurora
    Aurora Cross Region Read Replicase
        Useful for disaster recover
        Simple to put in place
    Aurora Global Database (recommended)
        1 Primary Region (read/write)
        Up to 10 secondary (read-only) regions, replication lag is less than 1 second
        Up to 16 Read Replicas per secondary region
        Helps for decreasing latency
        Promoting another region (for disaster recovery) has an RTO of < 1 minute
        Typical cross-region replication takes less than 1 second

Aurora Machine Learning
    Enables you to add ML-based predictions to your applications via SQL
    Simple, optimized, and secure integration between Aurora and AWS ML Services
    Supported services
        SageMaker (use with any ML model)
        Comprehend (for sentiment analysis)
    You don't need to have ML experience
    Use case: Fraud detection, ads targeting, sentiment analysis, product recommendations

Babelfish for Aurora PSQL
    Allows Aurora PSQL to understand commands targeted for Microsoft SQL Server (eg: T-SQL)
    Therefore Microsoft SQL Server based applications can work on Aurora PSQL
    Requires no to little code changes (using the same Microsoft SQL server client driver)
    The same applications can be used after a migration of your database (using AWS ScT and DMS)

RDS Backups
    Automated backups
        Daily full backup the database (during the backup window)
        Transaction logs are backed-up by RDS every 5 minutes
        Ability to restore to any point of time (from oldest backup to 5 minutes ago)
        1 to 35 days of retention, set 0 to disable automated backups
    Manual DB Snapshots
        Manually triggered by the user
        Retention of backup for as long as you want
    Trick: in a stopped RDS Database, you will still pay for storage. If you plan on stopping it for a long time, you should snapshot & restore instead

Aurora Backups
    Automated backups
        1 to 35 days (cannot be disabled) 
        Point-in-time recovery in that timeframe
    Manual DB Snapshots
        Manually triggered by the user
        Retention of backup for as long as you want

RDS & Aurora Restore options
    Restoring a RDS / Aurora backup or a snapshot creates a new database
    Restoring MySQL RDS database from S3
        Create a backup of your on-premises database
        Store it on S3
        Restore the backup file onto a new RDS instance running MySQL
    Restoring MySQL Aurora cluster from S3
        Create a backup of your on-premises database using Percona XtraBackup
        Store the backup file on S3
        Restore the backup file onto a new Aurora cluster running MySQL

Aurora Database Cloning
    Create a new AuroraDB Cluster from an existing one
    Faster than snapshot & restore
    Uses copy-on-write protocol
        Initially, the new DB cluster uses the same data volume as the orginal DB cluster (fast and efficient - no copying is needed)
        When updates are made to the new DB cluster data, then additional storage is allocated and data is copied to be separated
    Very fast & cost-effective
    Useful to create a "staging" database from a "production" database without impacting the production database

RDS & Aurora Security
    At-rest encryption:
        Database master & replicase encryption using AWS KMS - must be defined as launch time
        If the master is not encrypted, the read replicas cannot be encrypted
        To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted
    In-flight encryption: TLS-ready by default, use the AWS TLS root certificates client-side
    IAM Authentication: IAM roles to connect your database (instead of username/pw)
    Security Groups: Control Network access to your RDS / Aurora DB
    No SSH available except on RDS Custom
    Audit Logs can be enabled and sent to CloudWatch Logs for longer retention

RDS Proxy
    Fully managed database proxy for RDS
    Allows apps to pool and share DB connections established with the database
    Improving database efficiency by reducing the stress on database resources (eg: CPU, RAM) and minimize open connections (and timeouts)
    Serverless, autoscaling, highly available (multi-AZ)
    Reduced RDS & Aurora failover time by up 66%
    Supports RDS (MySQL, PSQL, MariaDB, Microsoft SQL Server) and Aurora (MySQL, PSQL)
    No code changes required for most apps
    Enforce IAM Authentication for DB, and securely store credentials in AWS Secrets Manager
    RDS Proxy is never publicly accessible (must be accessed from VPC)

ElastiCache
    Same way RDS is to get managed Relational Databases
    ElastiCache is to get managed Redis or Memcached
    Caches are in-memory databases with really high performance, low latency
    Helps reduce load off of databases for read intensive workloads
    Helps make your application stateless
    AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
    Using ElastiCache involves heavy application code changes

ElastiCache Solution Architecture - DB Cache
    Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache
    Helps relieve load in RDS
    Cache must have an invalidation strategy to make sure only the most current data is used in there

ElastiCache Solution Architecture - User Session Store
    User logs into any of the application
    The application writes the session data inot ElastiCache
    The user hits another instance of our application
    The instance retrieves the data and the user is already logged in

ElastiCache - Redis vs Memcached
    Redis
        Multi AZ with Auto-Failover
        Read Replicas to scale reads and have high availability
        Data Durability using AOF persistence
        Backup and restore features
        Supported Sets and Sorted Sets
    Memcached
        Multi-node for partitioning of data (sharding)
        No high availability (replication)
        Non persistent
        Backup and restore (Serverless)
        Multi-threaded architecture

ElastiCache - Cache Security
    ElastiCache supports IAM Authentication for Redis
    IAM policies on ElastiCache are only used for AWS API-level security
    Redis Auth
        You can set a "password/token" when you create a Redis cluster
        This is an extra level of security for your cache (on top of security groups)
        Support SSL in flight encryption
    Memcached
        Supports SASL-based authentication (advanced)

Patterns for ElastiCache
    Lazy Loading: All the read data is cached, data can become stale in cache
    Write Through: Adds or Update data in the cahce when written to a DB (no stale data)
    Session Store: Store temporary session data in a cache (using TTL features)
    
ElastiCahce - Redis Use Case
    Gaming Leaderboards are computationally complex
    Redis Sorted sets guarantee both uniqueness and element ordering
    Each time a new element added, it's ranked in real time, then added in correct order

List of Ports to be familiar with
    Important ports
        FTP: 21
        SSH: 22
        SFTP: 22 (same as SSH)
        HTTP: 80
        HTTPS: 443
    Database Ports
        PSQL: 5432
        MySQL: 3306
        Oracle RDS: 1521
        Microsoft SQL Server: 1433
        MariaDB: 3306 (same as MySQL)
        Aurora: 
            5432 (if PSQL compatible)
            3306 (if MySQL compatible)
